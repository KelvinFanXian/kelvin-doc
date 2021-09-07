## Builder

Separate the construction of a complex object from its representation so that the same construction process can create different representations.

将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

![image-20210201154450901](https://i.loli.net/2021/02/01/3IHOfybunaN2K75.png)



- Builder 用于规范产品的各个组成部分，并进行抽象，一般独立于应用程序的逻辑。
- Concrete Builder 实现抽象建造者中定义的所有方法，并且返回一个组建好的产品实例。
- Product 建造中的复杂对象，一个系统中会有多于一个的产品类，这些产品类并不一定有共同的接口，完全可以是不相关联的。
- Director 负责安排已有模块的顺序，然后告诉Builder开始建造。
  - 起到封装作用，避免高层模块深入到建造者内部的实现类。导演类可以有多个，根据项目的实际情况来决定使用的个数。

## 优点

- 封装性
- 建造者独立，容易扩展。
- 建造者独立，便于控制细节风险

## 场景

- 相同的方法，不同的执行顺序，产生不同的结果时
- 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时
- 产品类非常复杂，或者产品类中的方法调用顺序不同产生不同的效能

---

建造者模式关注的是**零件类型和装配工艺顺序**，这是与工厂方法模式最大的不同之处，虽然同为创建类模式，但是重点不同

