## FlyWeight

享元模式（Flyweight Pattern）是**池技术**的重要实现方式，可以降低大量重复、细粒度的类在内存中的开销。

> Use sharing to support large numbers of fine-grained objects efficiently.

使用共享对象可有效地支持大量的细粒度的对象

---

- 内部状态（Internal State）
  - 存储在享元对象内部
  - 可以共享的信息
  - 不会随环境变化
- 外部状态（External State）
  - 随环境变化
  - 不可共享
  - 享元对象的**外部状态必须由客户端保存**
    - 在享元对象被创建之后，在需要使用的时候再传入到享元对象内部

---

![image-20210203121712988](https://i.loli.net/2021/02/03/RrCBLoajZQisWm3.png)

- **Flyweight** 对享元类进行抽象，需要外部状态的操作可以通过参数的形式将外部状态传入
- **ConcreteFlyweight** 实现享元定义的业务，注意享元对象的内部状态必须与环境无关，从而使得享元对象可以在系统内共享。
- **FlyweightFactory** 构造一个池容器，负责创建和管理享元角色，并提供从池容器中获得对象的方法，保证享元对象可以被系统适当的共享。当一个客户端对象请求一个享元对象时，享元工厂角色会检查系统中是否已经有一个符合要求的享元对象。如果已经有了，享元工厂则提供这个已有的享元对象；否则创建一个合适的享元对象。
- **Client** 需要自行存储所有享元对象的外部状态。

---

:exclamation:除了上面4个角色，享元模式还会涉及**复合享元**角色，该角色是将一些单纯享元使用合成模式加以复合，形成复合享元对象，这些复合的享元对象本身不能共享，不会出现在享元工厂中，因此也称为“不可共享的享元角色”。但可以将复合享元对象分解成单纯的享元对象，而后者则可以共享。

---

```java
public interface Flyweight{
  public abstract void operation(String extrinsicState);
}
```

```java
public class ConcreteFlyweight implements Flyweight{
  private String intrinsicState;
  ConcreteFlyweight(String intrinsicState) {
    this.intrinsicState = intrinsicState;
  }
  @Override
  public void operation(String extrinsicState) {
    System.out.println("内部状态：" + intrinsicState
                      + ", 外部状态：" + extrinsicState);
  }
}
```

```java
public class FlyweightFactory{
  // 一个静态的 Map 集合来存放享元对象，该集合是池容器
  private static Map<String, Flyweight> pool = new HashMap<String, Flyweight>();
  private FlyweightFactory(){} // 私有构造方法
  public static Flyweight getFlyweight(String intrinsicState) {
    Flyweight flyweight = pool.get(intrinsicState);
    // 如果池容器中无对应的享元对象，则创建一个新的享元对象并保存到池容器中
    if (flyweight == null) {
      flyweight = new ConcreteFlyweight(intrinsicState);
      pool.put(intrinsicState, flyweight);
    }
    return flyweight;
  }
}
```



## 优缺点

- 优点
  - 大幅减少内存中的对象数量
- 缺点
  - 复杂，需要分出外部和内部状态
    - 内部状态具有固化特性，不随外部变化，这使得程序逻辑复杂化
  - 将享元对象的状态外部化
    - 而读取外部状态使得运行时间变长

## 使用场景

- 系统有大量相似对象，耗费大量内存
- 细粒度的对象都具备较接近的外部状态，而且内部状态与环境无关，即对象没有特定身份
- 需要缓冲池的场景

---

Java基础类库中大量使用了享元模式：

- String
- Integer
- Boolean
- Character

等类都通过享元模式提供了内部的池化机制。



## 实例（下围棋）

![image-20210203152252755](https://i.loli.net/2021/02/03/9SLEmT1by2Iw8NZ.png)

```java
public interface Chesspiece {
  void put(int x, int y);
}
```

```java
class ChesspieceFlyweight implements Chesspiece {
  private String color;
  public ChesspieceFlyweight(String color){
    this.color = color;
  }
  @Override
  public void put(int x, int y) {
    System.out.println(String.format("在(%d, %d)位置放了一个%s子", x,y,color));
  }
}
```

```java
public class ChesspieceFactory {
  static final Chesspiece WHITE = new ChesspieceFlyweight("白");
  static final Chesspiece BLACK = new ChesspieceFlyweight("黑");
  public static Chesspiece getChesspiece(String color) {
    if (color.equals("白"))
      return WHITE;
    else if(color.equals("黑"))
      return BLACK;
    return null;
  }
}
```

```java
public class Game {
  public static void main(String[] args) {
    Chesspiece p1 = ChesspieceFactory.getChesspiece("黑");
    p1.put(1, 1);
    Chesspiece p2 = ChesspieceFactory.getChesspiece("白");
    p1.put(2, 2);
    Chesspiece p3 = ChesspieceFactory.getChesspiece("黑");
    p1.put(3, 3);
    Chesspiece p4 = ChesspieceFactory.getChesspiece("白");
    p1.put(4, 4);
  }
}
```

