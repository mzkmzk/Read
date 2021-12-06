# Go语言编程之旅

## 1. 命令行应用: 打造属于自己的工具集

### 1.3 便捷的时间工具

time.Parse会尝试从入参参数读取时区信息, 入参参数没有指定时区, 则默认使用UTC时间

## 6. Go语言中的大杀器

### 6.1 Go大杀器PProf之性能剖析(上)

> 可以做什么

- cpu profiling: cpu分析, 按照一定的频率采集所监听的应用程序cpu的使用情况
- memory profiling: 内存分析, 在应用程序进行堆分配时记录堆栈跟踪, 用于监听当前和历史内存的使用情况, 以及检查内存泄漏
- block profiling: 阻塞分析. 记录goroutine阻塞等待同步(包括定时器通道)的位置, 默认不开启, 需要调用runtime.setBlockProfileRate进行设置
- mutex profiling: 互斥锁分析. 报告互斥锁的竞争情况, 默认不开启, 需要调用runtime.SetMutexProfileFraction进行设置
- goroutine profiling: goroutine分析, 可以对当前应用程序正在运行的goroutine进行堆栈跟踪

> 抓取命令

- allocs: 查看过去所有内存分配的样本, $host/debug/pprof/allocs, eg: [pprof的allocs显示](#pprof的allocs显示)
- block: 查看导致阻塞同步的堆栈跟踪, $host/debug/pprof/block
- cmdline: 当前程序命令行的完整调用路径
- goroutine: 查看当前所有运行的goroutines堆栈跟踪, $host/debug/pprof/goroutine, eg: [pprof的goroutine显示](#pprof的goroutine显示)
- heap: 查看活动对象的内存分配情况, $host/debug/pprof/heap, 默认采集`inuse_space`, eg: [pprof的heap显示](#pprof的heap显示)
- mutex: 查看导致互斥锁的竞争持有者的堆栈跟踪, $host/debug/pprof/mutex
- profile: 默认执行30s的cpu profiling, 会得到一个分析用的profile, $host/debug/pprof/profile
- threadcreate: 查看创建新OS线程堆栈跟踪, 访问路径为$host/debug/pprof/threadcreate, eg: [pprof的threadcreate显示](#pprof的threadcreate显示)

通过终端交互 `go tool pprof http://localhsot/6060/debug/pprof/profile\?seconds\=60`

> 内存分析分类

- inuse_space: 分析应用常驻内存的占用情况
- alloc_object: 分析应用程序的内存临时分配情况
- inuse_object: 每个函数的对象数量
- alloc_space: 每个函数分配的内存空间大小

> goroutine 查看traces

执行顺序为自下而上

```bash
➜  10500-read-git-book git:(master) go tool pprof http://localhost:6060/debug/pprof/goroutine
Fetching profile over HTTP from http://localhost:6060/debug/pprof/goroutine
Saved profile in /Users/maizhikun/pprof/pprof.goroutine.019.pb.gz
Type: goroutine
Time: Nov 6, 2021 at 9:18pm (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) traces
Type: goroutine
Time: Nov 6, 2021 at 9:18pm (CST)
-----------+-------------------------------------------------------
         1   runtime.gopark
             runtime.netpollblock
             internal/poll.runtime_pollWait
             internal/poll.(*pollDesc).wait
             internal/poll.(*pollDesc).waitRead (inline)
             internal/poll.(*FD).Read
             net.(*netFD).Read
             net.(*conn).Read
             net/http.(*connReader).backgroundRead
-----------+-------------------------------------------------------
         1   runtime.gopark
             runtime.netpollblock
             internal/poll.runtime_pollWait
             internal/poll.(*pollDesc).wait
             internal/poll.(*pollDesc).waitRead (inline)
             internal/poll.(*FD).Accept
             net.(*netFD).accept
             net.(*TCPListener).accept
             net.(*TCPListener).Accept
             net/http.(*Server).Serve
             net/http.(*Server).ListenAndServe
             net/http.ListenAndServe (inline)
             main.main
             runtime.main
```         

> 常看web交互界面

```bash
> go tool pprof http://localhost:6060/debug/pprof/profile
Fetching profile over HTTP from http://localhost:6060/debug/pprof/profile
Saved profile in /Users/maizhikun/pprof/pprof.samples.cpu.003.pb.gz

> go tool pprof -http=0.0.0.0:6001 /Users/maizhikun/pprof/pprof.samples.cpu.003.pb.gz
```

```go
package main

import (
	"net/http"
	_ "net/http/pprof"
	"runtime"
	"sync"
)

func init() {
	runtime.SetMutexProfileFraction(1)
}

func main() {
	var m sync.Mutex
	var datas = make(map[int]struct{})
	for i := 0; i < 999; i++ {
		go func(i int) {
			m.Lock()
			defer m.Unlock()
			datas[i] = struct{}{}
		}(i)
	}
	_ = http.ListenAndServe("0.0.0.0:6061", nil)
}
```

分析锁消耗的时间,和`list`查看函数中哪一行消耗时间

```
➜  10500-read-git-book git:(master) ✗ go tool pprof http://localhost:6061/debug/pprof/mutex
Fetching profile over HTTP from http://localhost:6061/debug/pprof/mutex
Saved profile in /Users/maizhikun/pprof/pprof.contentions.delay.002.pb.gz
Type: delay
Time: Nov 6, 2021 at 10:18pm (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) traces
Type: delay
Time: Nov 6, 2021 at 10:18pm (CST)
-----------+-------------------------------------------------------
    1.02ms   sync.(*Mutex).Unlock
             main.main.func1
-----------+-------------------------------------------------------
   48.16us   sync.(*Mutex).Unlock
             sync.(*Once).doSlow
             sync.(*Once).Do (inline)
             net/textproto.NewReader (inline)
             net/http.newTextprotoReader
             net/http.readRequest
             net/http.(*conn).readRequest
             net/http.(*conn).serve
-----------+-------------------------------------------------------
(pprof) list main
Total: 1.07ms
ROUTINE ======================== main.main.func1 in /Users/maizhikun/project/24300-pprof-study/cmd/2-mutex/main.go
         0     1.02ms (flat, cum) 95.48% of Total
         .          .     18:		go func(i int) {
         .          .     19:			m.Lock()
         .          .     20:			defer m.Unlock()
         .          .     21:			datas[i] = struct{}{}
         .          .     22:		}(i)
         .     1.02ms     23:	}
         .          .     24:	_ = http.ListenAndServe("0.0.0.0:6061", nil)
         .          .     25:}
```

## 附录

### pprof的allocs显示


```
heap profile: 5: 1212512 [11: 2613392] @ heap/1048576
1: 1212416 [1: 1212416] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

4: 96 [4: 96] @ 0x128e4c9 0x128e419 0x10715c1
#	0x128e4c8	main.Add+0xe8		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [0: 0] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 966656] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [2: 32] @ 0x128e429 0x10715c1
#	0x128e428	main.main.func1+0x48	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 311296] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 16] @ 0x10c9349 0x10d3537 0x128e474 0x10715c1
#	0x10c9348	fmt.Sprintf+0x88	/Users/maizhikun/go/go1.16/src/fmt/print.go:220
#	0x10d3536	log.Printf+0x56		/Users/maizhikun/go/go1.16/src/log/log.go:323
#	0x128e473	main.main.func1+0x93	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 122880] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15


# runtime.MemStats
# Alloc = 5083360
# TotalAlloc = 11889504
# Sys = 74531848
# Lookups = 0
# Mallocs = 235672
# Frees = 150308
# HeapAlloc = 5083360
...
```

### #pprof的goroutine显示

```
goroutine profile: total 5
1 @ 0x103b8c5 0x103423b 0x106bb75 0x10bf4c5 0x10c00f5 0x10c00d7 0x11542ef 0x115fb71 0x1224ff9 0x1125348 0x112603d 0x1126294 0x11d3fb6 0x122110a 0x122110b 0x122649d 0x122b0a5 0x10715c1
#	0x106bb74	internal/poll.runtime_pollWait+0x54		/Users/maizhikun/go/go1.16/src/runtime/netpoll.go:222
#	0x10bf4c4	internal/poll.(*pollDesc).wait+0x44		/Users/maizhikun/go/go1.16/src/internal/poll/fd_poll_runtime.go:87
#	0x10c00f4	internal/poll.(*pollDesc).waitRead+0x1d4	/Users/maizhikun/go/go1.16/src/internal/poll/fd_poll_runtime.go:92
#	0x10c00d6	internal/poll.(*FD).Read+0x1b6			/Users/maizhikun/go/go1.16/src/internal/poll/fd_unix.go:166
#	0x11542ee	net.(*netFD).Read+0x4e				/Users/maizhikun/go/go1.16/src/net/fd_posix.go:55
#	0x115fb70	net.(*conn).Read+0x90				/Users/maizhikun/go/go1.16/src/net/net.go:183
#	0x1224ff8	net/http.(*connReader).Read+0x1b8		/Users/maizhikun/go/go1.16/src/net/http/server.go:800
#	0x1125347	bufio.(*Reader).fill+0x107			/Users/maizhikun/go/go1.16/src/bufio/bufio.go:101
#	0x112603c	bufio.(*Reader).ReadSlice+0x3c			/Users/maizhikun/go/go1.16/src/bufio/bufio.go:360
#	0x1126293	bufio.(*Reader).ReadLine+0x33			/Users/maizhikun/go/go1.16/src/bufio/bufio.go:389
#	0x11d3fb5	net/textproto.(*Reader).readLineSlice+0xd5	/Users/maizhikun/go/go1.16/src/net/textproto/reader.go:57
#	0x1221109	net/textproto.(*Reader).ReadLine+0xa9		/Users/maizhikun/go/go1.16/src/net/textproto/reader.go:38
#	0x122110a	net/http.readRequest+0xaa			/Users/maizhikun/go/go1.16/src/net/http/request.go:1027
#	0x122649c	net/http.(*conn).readRequest+0x19c		/Users/maizhikun/go/go1.16/src/net/http/server.go:986
#	0x122b0a4	net/http.(*conn).serve+0x704			/Users/maizhikun/go/go1.16/src/net/http/server.go:1878

1 @ 0x103b8c5 0x103423b 0x106bb75 0x10bf4c5 0x10c0bd2 0x10c0bb4 0x11551a5 0x1166e32 0x1165e85 0x122f705 0x122f41a 0x128e3cd 0x128e396 0x103b496 0x10715c1
#	0x106bb74	internal/poll.runtime_pollWait+0x54		/Users/maizhikun/go/go1.16/src/runtime/netpoll.go:222
#	0x10bf4c4	internal/poll.(*pollDesc).wait+0x44		/Users/maizhikun/go/go1.16/src/internal/poll/fd_poll_runtime.go:87
#	0x10c0bd1	internal/poll.(*pollDesc).waitRead+0x211	/Users/maizhikun/go/go1.16/src/internal/poll/fd_poll_runtime.go:92
#	0x10c0bb3	internal/poll.(*FD).Accept+0x1f3		/Users/maizhikun/go/go1.16/src/internal/poll/fd_unix.go:401
#	0x11551a4	net.(*netFD).accept+0x44			/Users/maizhikun/go/go1.16/src/net/fd_unix.go:172
#	0x1166e31	net.(*TCPListener).accept+0x31			/Users/maizhikun/go/go1.16/src/net/tcpsock_posix.go:139
#	0x1165e84	net.(*TCPListener).Accept+0x64			/Users/maizhikun/go/go1.16/src/net/tcpsock.go:261
#	0x122f704	net/http.(*Server).Serve+0x284			/Users/maizhikun/go/go1.16/src/net/http/server.go:2981
#	0x122f419	net/http.(*Server).ListenAndServe+0xb9		/Users/maizhikun/go/go1.16/src/net/http/server.go:2910
#	0x128e3cc	net/http.ListenAndServe+0x6c			/Users/maizhikun/go/go1.16/src/net/http/server.go:3164
#	0x128e395	main.main+0x35					/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:20
#	0x103b495	runtime.main+0x255				/Users/maizhikun/go/go1.16/src/runtime/proc.go:225

1 @ 0x103b8c5 0x106e692 0x128e485 0x10715c1
#	0x106e691	time.Sleep+0xd1		/Users/maizhikun/go/go1.16/src/runtime/time.go:193
#	0x128e484	main.main.func1+0xa4	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:16

1 @ 0x106b7bd 0x127daee 0x127d8c5 0x127a452 0x128bd25 0x128d577 0x122c524 0x122e3ad 0x122f323 0x122b26d 0x10715c1
#	0x106b7bc	runtime/pprof.runtime_goroutineProfileWithLabels+0x5c	/Users/maizhikun/go/go1.16/src/runtime/mprof.go:716
#	0x127daed	runtime/pprof.writeRuntimeProfile+0xcd			/Users/maizhikun/go/go1.16/src/runtime/pprof/pprof.go:724
#	0x127d8c4	runtime/pprof.writeGoroutine+0xa4			/Users/maizhikun/go/go1.16/src/runtime/pprof/pprof.go:684
#	0x127a451	runtime/pprof.(*Profile).WriteTo+0x3f1			/Users/maizhikun/go/go1.16/src/runtime/pprof/pprof.go:331
#	0x128bd24	net/http/pprof.handler.ServeHTTP+0x384			/Users/maizhikun/go/go1.16/src/net/http/pprof/pprof.go:253
#	0x128d576	net/http/pprof.Index+0x8d6				/Users/maizhikun/go/go1.16/src/net/http/pprof/pprof.go:371
#	0x122c523	net/http.HandlerFunc.ServeHTTP+0x43			/Users/maizhikun/go/go1.16/src/net/http/server.go:2069
#	0x122e3ac	net/http.(*ServeMux).ServeHTTP+0x1ac			/Users/maizhikun/go/go1.16/src/net/http/server.go:2448
#	0x122f322	net/http.serverHandler.ServeHTTP+0xa2			/Users/maizhikun/go/go1.16/src/net/http/server.go:2887
#	0x122b26c	net/http.(*conn).serve+0x8cc				/Users/maizhikun/go/go1.16/src/net/http/server.go:1952

1 @ 0x1224a21 0x10715c1
#	0x1224a20	net/http.(*connReader).backgroundRead+0x0	/Users/maizhikun/go/go1.16/src/net/http/server.go:691

```

### pprof的heap显示


```
heap profile: 5: 966752 [10: 1400976] @ heap/1048576
1: 966656 [1: 966656] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

4: 96 [4: 96] @ 0x128e4c9 0x128e419 0x10715c1
#	0x128e4c8	main.Add+0xe8		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [2: 32] @ 0x128e429 0x10715c1
#	0x128e428	main.main.func1+0x48	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 311296] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 16] @ 0x10c9349 0x10d3537 0x128e474 0x10715c1
#	0x10c9348	fmt.Sprintf+0x88	/Users/maizhikun/go/go1.16/src/fmt/print.go:220
#	0x10d3536	log.Printf+0x56		/Users/maizhikun/go/go1.16/src/log/log.go:323
#	0x128e473	main.main.func1+0x93	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [1: 122880] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15

0: 0 [0: 0] @ 0x128e558 0x128e419 0x10715c1
#	0x128e557	main.Add+0x177		/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:25
#	0x128e418	main.main.func1+0x38	/Users/maizhikun/project/24300-pprof-study/cmd/1-base/main.go:15


# runtime.MemStats
# Alloc = 3532216
# TotalAlloc = 10001952
# Sys = 74531848
# Lookups = 0
# Mallocs = 215418
...
```

### pprof的threadcreate显示

```
threadcreate profile: total 12
11 @
#	0x0

1 @ 0x103ea6a 0x103f185 0x103f472 0x103b419 0x10715c1
#	0x103ea69	runtime.allocm+0x189			/Users/maizhikun/go/go1.16/src/runtime/proc.go:1754
#	0x103f184	runtime.newm+0x44			/Users/maizhikun/go/go1.16/src/runtime/proc.go:2074
#	0x103f471	runtime.startTemplateThread+0xb1	/Users/maizhikun/go/go1.16/src/runtime/proc.go:2144
#	0x103b418	runtime.main+0x1d8			/Users/maizhikun/go/go1.16/src/runtime/proc.go:204

```