- Centos7  以后其实推荐用 [FirewallD](https://firewalld.org/) , iptables 属于比较传统的方式
  - [How to Install Iptables on CentOS 7](https://linuxize.com/post/how-to-install-iptables-on-centos-7/)



## 开放80端口

- https://cloud.tencent.com/developer/article/1722236

```bash
vim /etc/sysconfig/iptables
# -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT

service iptables restart

sudo systemctl status iptables
```

