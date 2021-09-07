## RuntimeConstantPoolOOM

```bash
java -XX:PermSize=10M -XX:MaxPermSize=10M RuntimeConstantPoolOOM
```

JDK1.6，常量池在设计方法区（HotSpot虚拟机中的永久代）的一部分。

JDK1.7，常量池就被移到堆里了。