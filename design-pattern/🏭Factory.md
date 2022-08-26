## 工厂模式

- 简单工厂
- 工厂方法
- 抽象工厂



## 工厂方法，FactoryMethod

> Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory Method lets a class **defer instantiation to subclasses**.
>
> 定义一个用于创建对象的接口，让子类决定实例化哪个类。工厂方法使一个类的实例化延迟到其子类

![image-20220429101748907](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20220429101748907.png)



```java
public interface Factory{
  public <T extends Product> T create(Class<T> c);
}
public class ConcreteFactory implements Factory {
  public <T extends Product> t factory(Class<T> productClass) {
    product = (Product) Class.forName(productClass.getName()).newInstance();
  }
}

public interface Product {
}
public class ConcreteProduct implements Product {}

public class Client{
  public static void main(String args[]) {
    Factory factory = new ConcreteFactory();
    Product product = factory.create(ConcreteProduct.class);
  }
}
```



## 场景

比如说计算器的操作符，有➕➖✖️➗





