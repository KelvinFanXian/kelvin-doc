- 制造OOM并生成堆转储文件

```bash
java -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError HeapOOM
```



- 打开堆转储文件(*.hprof)

```shell
jvisualvm --openfile  JVM/java_pid14968.hprof
```

> 内存泄漏 & 内存溢出

- 内存泄漏？
  - 查看泄漏对象到GC Roots的**引用链**
    - 泄漏对象的类型信息
    - GC Roots引用链信息

- 内存溢出
  - 不存在泄漏，就是内存中的对象确实还必须存活着。
    - 调大参数, -Xmx与-Xms
    - 检查代码：缩短对象生命，持有状态时长