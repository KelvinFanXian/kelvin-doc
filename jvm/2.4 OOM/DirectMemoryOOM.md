```bash
java -Xmx20M -XX:MaxDirectMemorySize=10M DirectMemoryOOM
```

- MaxDirectMemorySize默认和-Xmx一样大
- OOM后Dump文件很小，且看不到明显异常，而程序中有间接或直接使用了NIO

