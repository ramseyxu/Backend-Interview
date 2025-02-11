1. [仅仅使用LIFO栈来实现FIFO的队列，然后使用FIFO的队列来实现FIFO的栈](#1-jin-jin-shi-yong-lifo-zhan-lai-shi-xian-fifo-de-dui-lie-ran-hou-shi-yong-fifo-de-dui-lie-lai-shi-xian-fifo-de-zhan)
2. [使用代码片段来实现栈溢出](#2-shi-yong-dai-ma-pian-duan-lai-shi-xian-zhan-yi-chu)
3. [使用尾递归版本的Fraction方法](#3-shi-yong-wei-di-gui-ban-ben-de-fraction-fang-fa)
4. [使用你最熟悉的语言，来实现一个REPL，它将输出任何输出；执行RPN表达式](#4-shi-yong-ni-zui-shou-xi-de-yu-yan-lai-shi-xian-yi-ge-repl-ta-jiang-shu-chu-ren-he-shu-chu-zhi-hang-rpn-biao-da-shi)
5. [如何设计一个磁盘整理碎片工具？](#5-ru-he-she-ji-yi-ge-ci-pan-zheng-li-sui-pian-gong-ju)
6. [编写一个随机生成迷宫的程序](#6-bian-xie-yi-ge-sui-ji-sheng-cheng-mi-gong-de-cheng-xu)
7. [编写一个实例代码能够导致内存泄漏](#7-bian-xie-yi-ge-shi-li-dai-ma-neng-gou-dao-zhi-nei-cun-xie-lou)
8. [生成一系列不同的随机数](#8-sheng-cheng-yi-xi-lie-bu-tong-de-sui-ji-shu)
9. [编写简单的垃圾回收系统](#9-bian-xie-jian-dan-de-la-ji-hui-shou-xi-tong)
10. [编写基础的消息发送broker](#10-bian-xie-ji-chu-de-xiao-xi-fa-song-broker)
11. [编写基础的web服务器，并且绘制出将来的要完成的功能](#11-bian-xie-ji-chu-de-web-fu-wu-qi-bing-qie-hui-zhi-chu-jiang-lai-de-yao-wan-cheng-de-gong-neng)
12. [如何对10GB的文件排序，那么10TB文件如何呢？](#12-ru-he-dui-10-gb-de-wen-jian-pai-xu-na-me-10-tb-wen-jian-ru-he-ne)
13. [如何自动检测冗余的文件？](#13-ru-he-zi-dong-jian-ce-rong-yu-de-wen-jian)

## 1 仅仅使用`LIFO`栈来实现`FIFO`的队列，然后使用`FIFO`的队列来实现`FIFO`的栈
  
```go
// Using two stacks to implement queue
type Queue struct {
    inStack Stack
    outStack Stack
}
func (q *Queue) Enqueue(item interface{}){
    for val, err:= q.outStack.Pop(); err==nil {
        q.inStack.Push(val)
    }
    q.inStack.Push(val)
}
func (q *Queue) Dequeue()(interface{}, error){
    for val, err := q.inStack.Pop(); err ==nil{
        q.outStack.Push(val)
    }
    return q.outStack.Pop()
}
// Using two queues to implement stack
type Stack struct{
    mainQueue Queue
    backupQueue Queue
}
func (s *Stack) Push(item interface{}){
    s.mainQueue.Enqueue(item)
}
func (s *Stack) Pop()(interface{}, error){
    var val interface{}
    err := errors.News("empty stack")
    for v, err := s.mainQueue.Dequeue(); err == nil {
        val = v
        s.backupQueue.Enqueue(v)
    }
    // switch queues
    s.backupQueue, s.mainQueue = s.mainQueue, s.backupQueue
    return val, err
}

```

## 2 使用代码片段来实现栈溢出
对于递归，如果没有递归基，也就是递归的出口。那么就会导致栈溢出。

```C#
public int Fib(n)
{
    return Fib(n-1) + Fib(n-2);
}
```

## 3 使用尾递归版本的Fraction方法
*todo*
## 4 使用你最熟悉的语言，来实现一个REPL，它将输出任何输出；执行RPN表达式
*todo*

## 5 如何设计一个磁盘整理碎片工具？
*todo*

## 6 编写一个随机生成迷宫的程序
*todo*

## 7 编写一个实例代码能够导致内存泄漏

内存泄露（Memory Leak) 是令软件开发工程师非常头疼的一件事，在托管开发语言中，比如 `C/C++`，内存的申请和释放都是由开发人员自己处理。因此如果申请的内存没有释放，就会导致出现内存泄露情况的发生。最常见的就是没有显示的调用 `free/delete` 语句来释放指针。

```C++
class MyClass {
    public:
        int MyNum;
        string myString;
}

void CreatePointer()
{
    MyClass* p = new MyClass();
    // do something
    return;
}
```

在这里指针 `p` 在申请内存后，并没有释放这个内存空间。所以当程序中多次调用这个方法之后，就会导致内存空间耗尽，知道出现 `Out of Memory` 这个异常使程序终止。

目前内存托管的开发语言的出现，使开发人员从内存管理任务中解放出来。它们都会带有一个垃圾回收机制(`GC`)。它们会定期清理内存中申请但是不再使用的对象，以便释放出内存。虽然这类开发语言帮我们解决这内存的问题，但是如果使用不当仍然也会导致内存泄露。

```C#
public string Read()
{
    FileStream fs = new FileStream(@"C:\data.txt", FileMode.Open, FileAccess.Read);
    return fs.ReadString();
}
```

在 `C#` 在读取外部资源，比如文件，网络，数据库的时候，如果没有调用 `Dispose` 方法，那么该资源占用的内存是不会被 `GC` 回收的，因此需要使用 `Dispose Pattern`。

还有一种内存泄露比较隐蔽，`GC` 通过扫描不可达的对象标记为待回收的对象。但是如果我们代码的中，如果存在隐藏的引用，也会导致内存泄露。

```C#
class Container<T> {
    private T[] _arr;

    public int Count = 0;

    public void Push<T>(T elem)
    {
        _arr[Count] = elem;
        Count++;
    }

    public T Pop<T>()
    {
        Count--
        return _arr[Count];
    }
}
```

这里我们用 `Count` 来作为一个数组的游标，但是我们要注意的是一点是在 `Pop` 操作之后，如果这个对象不会再使用，垃圾回收也不会将它们回收，因为我们 `_arr` 仍然隐式的引用了这个对象。所以使用不当的话，仍然会导致内存泄露。





## 8 生成一系列不同的随机数
*todo*

## 9 编写简单的垃圾回收系统
*todo*

## 10 编写基础的消息发送broker
*todo*

## 11 编写基础的web服务器，并且绘制出将来的要完成的功能
*todo*

## 12 如何对10GB的文件排序，那么10TB文件如何呢？
*todo*
## 13 如何自动检测冗余的文件？
*todo*

