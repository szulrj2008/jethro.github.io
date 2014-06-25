---
layout: post
title: Redis 大内存优化
description: 记录一次我们公司redis的优化过程
category: blog
---

突然发现我们的[Redis][] 已经用了30G了，好吧这是个很尴尬的数字因为我们的缓存机器的内存目前是32G的，内存已经告竭。幸好上上周公司采购了90G的机器，现在已经零时迁移到其中的一台机器上了。(跑题下，90G的内存太爽了是我除了koding.com 之外第二次用到90G的机器，koding 是个好网站，在线编程IDE。) 但是随着数据量越来越大单机始终无法承受的，改造势在必行。经过初步思考我们得出了很简单的方案 概括起来就是    "内外兼修"

##1.内功修炼

先从我们的应用层说起 看看redis 使用情况 ，有没有办法回收一些key ，先进入redis 服务器执行 info ,有删减

	redis 127.0.0.1:6391> info
	used_memory_human:35.58G
	keyspace_hits:2580207188
	db0:keys=2706740,expires=1440700

目前我们只使用了1个DB 但是key 太多了 有270W个key,已经过期的有144W。第一个想到的就是我勒个去,怎么会有这么多key ,第二个想法就是可能存在过大的key。

看看能不能针对过大的key 做优化？可是遗憾的是官方并没有命令显示db 的key 大小，我们只能自己想办法了

Google 一番，发现国外友人已经写好了shell----[传送门][1]

可以列出每个key 大小了。可是这并不适用我们，因为我们key 太大了 执行了9个小时都没跑完，无力吐槽了。 其实还有一个选择就是用另外一个[工具]:[2]

可惜这个太重了 ，不想麻烦ops ，我们就只能撩起袖子，造轮子。

把shell 代码简单看了下发件DEBUG OBJECT 是个好东西啊 ，google 下发现[官网][3]

已经有简单的调试信息了，剩下的就好处理了

	#coding=utf-8
	import redis

	COLOR_RED = "\033[31;49;1m %s \033[31;49;0m"
	COLOR_GREED = "\033[32;49;1m %s \033[39;49;0m"
	COLOR_YELLOW = "\033[33;49;1m %s \033[33;49;0m"
	COLOR_BLUE = "\033[34;49;1m %s \033[34;49;0m"
	COLOR_PINK = "\033[35;49;1m %s \033[35;49;0m"
	COLOR_GREENBLUE = "\033[36;49;1m %s \033[36;49;0m"

	def getHumanSize(value):
	gb = 1024 * 1024 * 1024.0
	mb = 1024 * 1024.0
	kb = 1024.0
	if value >= gb:
	return COLOR_RED % (str(round(value / gb, 2)) + " gb")
	elif value >= mb:
	return COLOR_YELLOW % (str(round(value / mb, 2)) + " mb")
	elif value >= kb:
	return COLOR_BLUE % (str(round(value / kb, 2)) + " kb")
	else:
	return COLOR_GREED % (str(value) + "b")

	month = 3600 * 24 * 30
	result = []
	client = redis.Redis(host="10.0.0.1", port=6379)
	client.info()

	count = 0
	for key in client.keys('*'):
	try:
	count += 1

	##idleTime表示与key相关联的值自存储以来处于空闲状态（IDLE）的秒数（未被read或##write等操作所请求）
	##refcount表示与指定key相关联的值的引用个数

	idleTime = client.object('idletime', key)
	refcount = client.object('refcount', key)
	length = client.debug_object(key)['serializedlength']
	value = idleTime * refcount
	print "%s key :%s , idletime : %s,refcount :%s, length : %s , humSize :%s" % (count, key, idleTime, refcount, length, getHumanSize(length))
	except Exception:
	pass

写了个简单的python 脚本输出每个key 的大小和idle time,和refer count 。有了这么多数据结合awk 就可以很好的统计每个key 的使用情况。有一点要注意的是这个size 是key 在redis 中的大小，并非实际的大小，这个是经过redis 压缩的。经过分析之后发现不存在过大的key ,但是存在有些key 半年都没有被访问过 Orz 。

接下来就很好处理了,我们为每个key 设置的过期时间,若key 被hit 上则更新这个expire time。这样可以逐步淘汰冷数据，达到冷热分离

 

##2. 外功修炼

我们对内清理了无效的key，对外我们要做到水平扩展，单机的承载始终有限，于是我们开始了传说中的分布式改造

分布式这东西看起来很唬人做起来更唬人，幸好我们是缓存服务 CAP约束有限。 缓存服务做分布式最好的当然是一致性hash 咯。其实当我们改造完成之后，才发现官方已经准备做这个分布式的缓存体系了（流口水啊） 只是现在还在开发中 给了个备用的响当当的  Twemproxy  奈何我们已经做好了，就先用着，坐等官方测试之后再说

传送门： http://redis.io/topics/cluster-spec

我们实现了数据的平滑迁移，而且对server 的修改实现了最小影响。 因为原来是用的是phpredis 所以就扩展了下，代码可以平滑过渡。

我们自己的实现：https://github.com/trigged/redis_con_hash

其实扯了这么多就是要把redis 的数据分散开，单机的承载始终是个瓶颈，但是redis 在这方面没有Memcached 完善，不过以后会越来越好。

 

From：http://www.cnblogs.com/trigged/p/3240774.html
[Redis]:  http://redis.io  "Redis"
[1]:  https://gist.github.com/epicserve/5699837
[2]:  https://github.com/sripathikrishnan/redis-rdb-tools
[3]:  http://redis.io/commands/object