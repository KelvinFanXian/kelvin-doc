## JavaVMStackSOF

```bash
java -Xss160k JavaVMStackSOF
```

> 在**单个线程**下，无论是由于栈帧太大还是虚拟机栈容量太小，当内存无法分配的时候，虚拟机抛出的都是`StackOverflowError`异常。
>
> - 栈帧太大
> - 虚拟机栈容量太小

---

## JavaVMStackOOM

```bash
java -Xss2M JavaVMStackOOM
```



多线程导致内存溢出 `JavaVMStackOOM`

- 剩余内存 = 操作系统 ➖ Xmx(最大堆容量) :heavy_minus_sign: MaxPermSize(最大方法区容量)
  - 每格线程分配到的**栈容量**越大，可以建立的线程数量自然就越少，建立线程时就越容易把剩下的内存耗尽。
  - 如果线程数不能减少，则只能通过减少最大堆和减少栈容量来换取更多的线程。
    - 通过“减少内存”的手段来解决内存溢出

