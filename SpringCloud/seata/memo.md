# scm-financial-dev.yml

```yaml
      seata: false    # 开启seata代理，开启后默认每个数据源都代理，如果某个不需要代理可单独关闭

# seata配置
seata:
  # 默认关闭，如需启用spring.datasource.dynami.seata需要同时开启
  enabled: false
```

- 启用的话，需要将这两个seata值设为true
- 未用时，需要将这两个seata值设为false