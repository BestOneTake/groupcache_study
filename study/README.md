### 函数singleflight.Do中，当回调函数执行时间非常短时，会出现结果不一致的情况。见tag: singleflight\_do\_fn\_step1

<b>执行以下命令:</b> 

```shell
git checkout singleflight_do_fn_step1
go test ./singleflight
```
<b>执行结果：</b> 
>
--- FAIL: TestDoDupSuppress (0.11s)<br>
	singleflight_test.go:83: number of calls = 10; want 1<br>
FAIL<br>
FAIL	github.com/golang/groupcache/singleflight 0.112s<br>

<b>问题代码在于:singleflight.go的第59行开始的3行</b>

```go
	g.mu.Lock()
	delete(g.m, key)
	g.mu.Unlock()	
```
当多个协程同时执行，当第一个协程执行到第59行时，其他协程因为42行的lock，还没执行到第46行。导致这些协程会跳过46行的判断。

<b>解决办法：见tag:singleflight\_do\_fn\_step1\_fix</b>


