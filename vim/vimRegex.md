### Regex for substitute

#### 反向引用

```bash
%s/\(\$\?\w\+\:\)/'\1'/g  # map的key加上引号
```

