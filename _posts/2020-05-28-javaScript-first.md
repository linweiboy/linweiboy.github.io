---
title: JavaScript之常用的设计模式
description: 对于要做前端架构者来说，我觉得有两点比较重要，一个是架构思想，还有一个就是架构的设计模式
categories:
 - JavaScript
tags:
---

> 设计模式的定义：在面向对象的软件设计过程中针对特定问题而给出简单而优雅的解决方案

<!-- more -->

当然我们可以用一个通俗的说法：设计模式是解决某个特定场景下对某种问题的解决方案。因此，当我们遇到合适的场景时，我们可能会条件反射一样自然而然的想到符合这种场景的设计模式。

比如，当系统中某个接口的结构已经无法满足我们现在的业务需求，但又不能改动这个接口，因为可能原来的系统很多功能都依赖于这个接口，改动接口会牵扯到太多文件。因此应对这种场景，我们可以很快的想到可以用适配器模式来解决这个问题。又比如，我们组件化开发，通过多个组件的共性定制出一个基础组件，在这个基础组件上进行二次开发封装，应对这种场景我们可以用到创建者模式、单例模式。代码封装可以用到工厂模式、原型模式等。

## 为什么会有设计模式

只是让看起来或者写起来更优雅（阅读性好，扩展性好），优雅这个词是”艺术性“的，所以设计代码也是一种艺术
比如,buider模式，如果一个对象有多个参数（以后还有可能增加），那么使用构造方法就不优雅，另外，不可能每次新增一个属性，都要修改构造方法吧，
如果采用builder模式就很好得解决这个问题了

#### 设计模式的优点

1. 重用设计和代码 重用设计比重用代码更有意义，自动带来代码重用
2. 提高扩展性 大量使用面向接口编程，预留扩展插槽，新的功能或特版性很容易加入到系统中来
3. 提高灵活性 通过组合提高灵活性，可允许代码修改平稳发生，对一处修改不会波及到其他模块
4. 提高开发效率 正确使用权设计模式，可以节省大量的时间

## 设计模式的分类

设计模式主要分三个类型:创建型、结构型和行为型。

#### 创建型
1. Singleton，单例模式：保证一个类只有一个实例，并提供一个访问它的全局访问点
```
public class Singleton {
     private static final Singleton singleton = new Singleton();
//限制产生多个对象
     private Singleton(){
     }
     //通过该方法获得实例对象
     public static Singleton getSingleton(){
             return singleton;
     }  
     //类中其他方法，尽量是static
     public static void doSomething(){
     }
}
```
**使用场景：**
* 要求生成唯一序列号的环境；
* 在整个项目中需要一个共享访问点或共享数据，例如一个Web页面上的计数器，可以不用把每次刷新都记录到数据库中，使用单例模式保持计数器的值，并确保是线程安全的；
* 创建一个对象需要消耗的资源过多，如要访问IO和数据库等资源；
* 需要定义大量的静态常量和静态方法（如工具类）的环境，可以采用单例模式（当然，也可以直接声明为static的方式）。

**线程不安全实例**
```
public class Singleton {
     private static Singleton singleton = null; 
     //限制产生多个对象
     private Singleton(){
     }  
     //通过该方法获得实例对象
     public static Singleton getSingleton(){
             if(singleton == null){
                    singleton = new Singleton();
             }
             return singleton;
     }
}
```
**解决办法：**
在getSingleton方法前加synchronized关键字，也可以在getSingleton方法内增加synchronized来实现。**最优的办法是如通用代码那样写**。

2. Abstract Factory，抽象工厂模式：提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们的具体类。
```
public abstract class AbstractCreator {
     //创建A产品家族
     public abstract AbstractProductA createProductA(); 
     //创建B产品家族
     public abstract AbstractProductB createProductB();
}
```
**使用场景：**
一个对象族（或是一组没有任何关系的对象）都有相同的约束。
涉及不同操作系统的时候，都可以考虑使用抽象工厂模式

3. Factory Method，工厂模式：定义一个用于创建对象的接口，让子类决定实例化哪一个类，Factory Method使一个类的实例化延迟到了子类。

Product为抽象产品类负责定义产品的共性，实现对事物最抽象的定义；
Creator为抽象创建类，也就是抽象工厂，具体如何创建产品类是由具体的实现工厂ConcreteCreator完成的。

```
public class ConcreteCreator extends Creator {
public <T extends Product> T createProduct(Class<T> c){
             Product product=null;
             try {
                    product = (Product)Class.forName(c.getName()).newInstance();
             } catch (Exception e) {
                    //异常处理
             }
             return (T)product;         
     }
}
```
**简单工厂模式：**
一个模块仅需要一个工厂类，没有必要把它产生出来，使用静态的方法

**多个工厂类：**
每个人种（具体的产品类）都对应了一个创建者，每个创建者独立负责创建对应的产品对象，非常符合单一职责原则

**代替单例模式：**
单例模式的核心要求就是在内存中只有一个对象，通过工厂方法模式也可以只在内存中生产一个对象

**延迟初始化：**
ProductFactory负责产品类对象的创建工作，并且通过prMap变量产生一个缓存，对需要再次被重用的对象保留

**使用场景：**
jdbc连接数据库，硬件访问，降低对象的产生和销毁

4. Builder，建造模式：将一个复杂对象的构建与他的表示相分离，使得同样的构建过程可以创建不同的表示。

* Product产品类
通常是实现了模板方法模式，也就是有模板方法和基本方法，例子中的BenzModel和BMWModel就属于产品类。
* Builder抽象建造者
规范产品的组建，一般是由子类实现。例子中的CarBuilder就属于抽象建造者。
* ConcreteBuilder具体建造者
实现抽象类定义的所有方法，并且返回一个组建好的对象。例子中的BenzBuilder和BMWBuilder就属于具体建造者。
* Director导演类
负责安排已有模块的顺序，然后告诉Builder开始建造

**使用场景：**
* 相同的方法，不同的执行顺序，产生不同的事件结果时，可以采用建造者模式。
* 多个部件或零件，都可以装配到一个对象中，但是产生的运行结果又不相同时，则可以使用该模式。
* 产品类非常复杂，或者产品类中的调用顺序不同产生了不同的效能，这个时候使用建造者模式非常合适。
 
**建造者模式与工厂模式的不同：**
建造者模式最主要的功能是基本方法的调用顺序安排，这些基本方法已经实现了，顺序不同产生的对象也不同；
工厂方法则重点是创建，创建零件是它的主要职责，组装顺序则不是它关心的。

5. Prototype，原型模式：用原型实例指定创建对象的种类，并且通过拷贝这些原型来创建新的对象。
```
public class PrototypeClass  implements Cloneable{
     //覆写父类Object方法
     @Override
     public PrototypeClass clone(){
             PrototypeClass prototypeClass = null;
             try {
                    prototypeClass = (PrototypeClass)super.clone();
             } catch (CloneNotSupportedException e) {
                    //异常处理
             }
             return prototypeClass;
     }
}
```
**原型模式实际上就是实现Cloneable接口，重写clone（）方法。**

**使用原型模式的优点：**
* 性能优良
原型模式是在内存二进制流的拷贝，要比直接new一个对象性能好很多，特别是要在一个循环体内产生大量的对象时，原型模式可以更好地体现其优点。

* 逃避构造函数的约束
这既是它的优点也是缺点，直接在内存中拷贝，构造函数是不会执行的（参见13.4节）。

**使用场景：**
* 资源优化场景
类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。

* 性能和安全要求的场景
通过new产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。

* 一个对象多个修改者的场景
一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。
 
**浅拷贝和深拷贝：**
* 浅拷贝：
Object类提供的方法clone只是拷贝本对象，其对象内部的数组、引用对象等都不拷贝，还是指向原生对象的内部元素地址，这种拷贝就叫做浅拷贝，其他的原始类型比如int、long、char、string（当做是原始类型）等都会被拷贝。

**注意：**　使用原型模式时，引用的成员变量必须满足两个条件才不会被拷贝：一是类的成员变量，而不是方法内变量；二是必须是一个可变的引用对象，而不是一个原始类型或不可变对象。
* 深拷贝：
对私有的类变量进行独立的拷贝    
如：thing.arrayList = (ArrayList<String>)this.arrayList.clone();


#### 行为型

1. Iterator，迭代器模式：提供一个方法顺序访问一个聚合对象的各个元素，而又不需要暴露该对象的内部表示。

* Iterator抽象迭代器
抽象迭代器负责定义访问和遍历元素的接口，而且基本上是有固定的3个方法：first()获得第一个元素，next()访问下一个元素，isDone()是否已经访问到底部（Java叫做hasNext()方法）。

* ConcreteIterator具体迭代器
具体迭代器角色要实现迭代器接口，完成容器元素的遍历。

* Aggregate抽象容器
容器角色负责提供创建具体迭代器角色的接口，必然提供一个类似createIterator()这样的方法，在Java中一般是iterator()方法。

* Concrete Aggregate具体容器
具体容器实现容器接口定义的方法，创建出容纳迭代器的对象。

**ps：** 迭代器模式已经被淘汰，java中已经把迭代器运用到各个聚集类（collection）中了，使用java自带的迭代器就已经满足我们的需求了。

2. Observer，观察者模式：定义对象间一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知自动更新。

* Subject被观察者
定义被观察者必须实现的职责，它必须能够动态地增加、取消观察者。它一般是抽象类或者是实现类，仅仅完成作为被观察者必须实现的职责：管理观察者并通知观察者。

* Observer观察者
观察者接收到消息后，即进行update（更新方法）操作，对接收到的信息进行处理。

* ConcreteSubject具体的被观察者
定义被观察者自己的业务逻辑，同时定义对哪些事件进行通知。

* ConcreteObserver具体的观察者
每个观察在接收到消息后的处理反应是不同，各个观察者有自己的处理逻辑。

```
public abstract class Subject {
     //定义一个观察者数组
     private Vector<Observer> obsVector = new Vector<Observer>();
     //增加一个观察者
     public void addObserver(Observer o){
             this.obsVector.add(o);
     }
     //删除一个观察者
     public void delObserver(Observer o){
             this.obsVector.remove(o);
     }
     //通知所有观察者
     public void notifyObservers(){
             for(Observer o:this.obsVector){
                     o.update();
}
     }
}
```
**使用场景：**
* 关联行为场景。需要注意的是，关联行为是可拆分的，而不是“组合”关系。
* 事件多级触发场景。
* 跨系统的消息交换场景，如消息队列的处理机制。

**注意：**
* 广播链的问题
在一个观察者模式中最多出现一个对象既是观察者也是被观察者，也就是说消息最多转发一次（传递两次）。
* 异步处理问题
观察者比较多，而且处理时间比较长，采用异步处理来考虑线程安全和队列的问题。

3. Template Method，模板方法：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，TemplateMethod使得子类可以不改变一个算法的结构即可以重定义该算法得某些特定步骤。

**AbstractClass叫做抽象模板，它的方法分为两类：**
* 基本方法
基本方法也叫做基本操作，是由子类实现的方法，并且在模板方法被调用。
* 模板方法
可以有一个或几个，一般是一个具体方法，也就是一个框架，实现对基本方法的调度，完成固定的逻辑。
**注意：**　为了防止恶意的操作，一般模板方法都加上final关键字，不允许被覆写。
**具体模板：**ConcreteClass1和ConcreteClass2属于具体模板，实现父类所定义的一个或多个抽象方法，也就是父类定义的基本方法在子类中得以实现
**使用场景：**
* 多个子类有公有的方法，并且逻辑基本相同时。
* 重要、复杂的算法，可以把核心算法设计为模板方法，周边的相关细节功能则由各个子类实现。
* 重构时，模板方法模式是一个经常使用的模式，把相同的代码抽取到父类中，然后通过钩子函数（见“模板方法模式的扩展”）约束其行为。

4. Command，命令模式：将一个请求封装为一个对象，从而使你可以用不同的请求对客户进行参数化，对请求排队和记录请求日志，以及支持可撤销的操作。

* Receive接收者角色
该角色就是干活的角色，命令传递到这里是应该被执行的，具体到我们上面的例子中就是Group的三个实现类（需求组，美工组，代码组）。

* Command命令角色
需要执行的所有命令都在这里声明。

* Invoker调用者角色
接收到命令，并执行命令。在例子中，我（项目经理）就是这个角色。

**使用场景：**
认为是命令的地方就可以采用命令模式，例如，在GUI开发中，一个按钮的点击是一个命令，可以采用命令模式；模拟DOS命令的时候，当然也要采用命令模式；触发－反馈机制的处理等。

5. State，状态模式：允许对象在其内部状态改变时改变他的行为。对象看起来似乎改变了他的类。

* State——抽象状态角色
接口或抽象类，负责对象状态定义，并且封装环境角色以实现状态切换。

* ConcreteState——具体状态角色
每一个具体状态必须完成两个职责：本状态的行为管理以及趋向状态处理，通俗地说，就是本状态下要做的事情，以及本状态如何过渡到其他状态。

* Context——环境角色
定义客户端需要的接口，并且负责具体状态的切换。
 
*使用场景：*
* 行为随状态改变而改变的场景
这也是状态模式的根本出发点，例如权限设计，人员的状态不同即使执行相同的行为结果也会不同，在这种情况下需要考虑使用状态模式。
* 条件、分支判断语句的替代者
 
**注意：**
状态模式适用于当某个对象在它的状态发生改变时，它的行为也随着发生比较大的变化，也就是说在行为受状态约束的情况下可以使用状态模式，而且使用时对象的状态最好不要超过5个。

6. Strategy，策略模式：定义一系列的算法，把他们一个个封装起来，并使他们可以互相替换，本模式使得算法可以独立于使用它们的客户。

* Context封装角色
它也叫做上下文角色，起承上启下封装作用，屏蔽高层模块对策略、算法的直接访问，封装可能存在的变化。

* Strategy抽象策略角色
策略、算法家族的抽象，通常为接口，定义每个策略或算法必须具有的方法和属性。各位看官可能要问了，类图中的AlgorithmInterface是什么意思，嘿嘿，algorithm是“运算法则”的意思，结合起来意思就明白了吧。

* ConcreteStrategy具体策略角色（多个）
实现抽象策略中的操作，该类含有具体的算法。

**使用场景：**
* 多个类只有在算法或行为上稍有不同的场景。
* 算法需要自由切换的场景。
* 需要屏蔽算法规则的场景。

**注意事项：**具体策略数量超过4个，则需要考虑使用混合模式
```
public enum Calculator {
     //加法运算
     ADD("+"){
             public int exec(int a,int b){
                    return a+b;
             }
     },
     //减法运算
     SUB("-"){
             public int exec(int a,int b){
                    return a - b;
             }
     };
     String value = "";
     //定义成员值类型
     private Calculator(String _value){
             this.value = _value;
     }
     //获得枚举成员的值
     public String getValue(){
             return this.value;
     }
     //声明一个抽象函数
     public abstract int exec(int a,int b);
}
```
**定义：**
* 它是一个枚举。
* 它是一个浓缩了的策略模式的枚举。

**注意：**
受枚举类型的限制，每个枚举项都是public、final、static的，扩展性受到了一定的约束，因此在系统开发中，策略枚举一般担当不经常发生变化的角色。
* 致命缺陷：
所有的策略都需要暴露出去，由客户端决定使用哪一个策略。

7. China of Responsibility，职责链模式：使多个对象都有机会处理请求，从而避免请求的送发者和接收者之间的耦合关系
```
public abstract class Handler {
     private Handler nextHandler;
     //每个处理者都必须对请求做出处理
     public final Response handleMessage(Request request){
             Response response = null;  
             //判断是否是自己的处理级别
             if(this.getHandlerLevel().equals(request.getRequestLevel())){
                    response = this.echo(request);
             }else{  //不属于自己的处理级别
                    //判断是否有下一个处理者
                    if(this.nextHandler != null){
                            response = this.nextHandler.handleMessage(request);
                    }else{
                            //没有适当的处理者，业务自行处理
                    }
             }
             return response;
     }
     //设置下一个处理者是谁
     public void setNext(Handler _handler){
             this.nextHandler = _handler;
     }
     //每个处理者都有一个处理级别
     protected abstract Level getHandlerLevel();
     //每个处理者都必须实现处理任务
     protected abstract Response echo(Request request);
}
```
**抽象的处理者实现三个职责：**
1. 定义一个请求的处理方法handleMessage，唯一对外开放的方法；
2. 定义一个链的编排方法setNext，设置下一个处理者；
3. 定义了具体的请求者必须实现的两个方法：定义自己能够处理的级别getHandlerLevel和具体的处理任务echo。
**注意事项：**
链中节点数量需要控制，避免出现超长链的情况，一般的做法是在Handler中设置一个最大节点数量，在setNext方法中判断是否已经是超过其阈值，超过则不允许该链建立，避免无意识地破坏系统性能。

8. Mediator，中介者模式：用一个中介对象封装一些列的对象交互。

* Mediator 抽象中介者角色
抽象中介者角色定义统一的接口，用于各同事角色之间的通信。 

* Concrete Mediator 具体中介者角色
具体中介者角色通过协调各同事角色实现协作行为，因此它必须依赖于各个同事角色。

* Colleague 同事角色
每一个同事角色都知道中介者角色，而且与其他的同事角色通信的时候，一定要通过中介者角色协作。每个同事类的行为分为两种：一种是同事本身的行为，比如改变对象本身的状态，处理自己的行为等，这种行为叫做自发行为（Self-Method），与其他的同事类或中介者没有任何的依赖；第二种是必须依赖中介者才能完成的行为，叫做依赖方法（Dep-Method）。
 
**通用抽象中介者代码：**
```
public abstract class Mediator {
     //定义同事类
     protected ConcreteColleague1 c1;
     protected ConcreteColleague2 c2;
     //通过getter/setter方法把同事类注入进来
     public ConcreteColleague1 getC1() {
             return c1;
     }
     public void setC1(ConcreteColleague1 c1) {
             this.c1 = c1;
     }
     public ConcreteColleague2 getC2() {
             return c2;
}
     public void setC2(ConcreteColleague2 c2) {
             this.c2 = c2;
     }
     //中介者模式的业务逻辑
     public abstract void doSomething1();
     public abstract void doSomething2();
}
```
**ps：**使用同事类注入而不使用抽象注入的原因是因为抽象类中不具有每个同事类必须要完成的方法。即每个同事类中的方法各不相同。
 
**问：**为什么同事类要使用构造函数注入中介者，而中介者使用getter/setter方式注入同事类呢？
这是因为同事类必须有中介者，而中介者却可以只有部分同事类。
 
**使用场景：**
中介者模式适用于多个对象之间紧密耦合的情况，紧密耦合的标准是：在类图中出现了蜘蛛网状结构，即每个类都与其他的类有直接的联系。

9. Visitor，访问者模式：表示一个作用于某对象结构中的各元素的操作，它使你可以在不改变各元素类的前提下定义作用于这个元素的新操作。

* Visitor——抽象访问者
抽象类或者接口，声明访问者可以访问哪些元素，具体到程序中就是visit方法的参数定义哪些对象是可以被访问的。

* ConcreteVisitor——具体访问者
它影响访问者访问到一个类后该怎么干，要做什么事情。

* Element——抽象元素
接口或者抽象类，声明接受哪一类访问者访问，程序上是通过accept方法中的参数来定义的。

* ConcreteElement——具体元素
实现accept方法，通常是visitor.visit(this)，基本上都形成了一种模式了。

* ObjectStruture——结构对象
元素产生者，一般容纳在多个不同类、不同接口的容器，如List、Set、Map等，在项目中，一般很少抽象出这个角色。
 
**使用场景：**
* 一个对象结构包含很多类对象，它们有不同的接口，而你想对这些对象实施一些依赖于其具体类的操作，也就说是用迭代器模式已经不能胜任的情景。
* 需要对一个对象结构中的对象进行很多不同并且不相关的操作，而你想避免让这些操作“污染”这些对象的类。

10. Interpreter，解释器模式：给定一个语言，定义他的文法的一个表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。

* AbstractExpression——抽象解释器
具体的解释任务由各个实现类完成，具体的解释器分别由TerminalExpression和Non-terminalExpression完成。

* TerminalExpression——终结符表达式
实现与文法中的元素相关联的解释操作，通常一个解释器模式中只有一个终结符表达式，但有多个实例，对应不同的终结符。具体到我们例子就是VarExpression类，表达式中的每个终结符都在栈中产生了一个VarExpression对象。

* NonterminalExpression——非终结符表达式
文法中的每条规则对应于一个非终结表达式，具体到我们的例子就是加减法规则分别对应到AddExpression和SubExpression两个类。非终结符表达式根据逻辑的复杂程度而增加，原则上每个文法规则都对应一个非终结符表达式。

* Context——环境角色
具体到我们的例子中是采用HashMap代替。
 
**使用场景：**
* 重复发生的问题可以使用解释器模式
* 一个简单语法需要解释的场景
 
**注意：**
尽量不要在重要的模块中使用解释器模式，否则维护会是一个很大的问题。在项目中可以使用shell、JRuby、Groovy等脚本语言来代替解释器模式，弥补Java编译型语言的不足。

11. Memento，备忘录模式：在不破坏对象的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态。

* Originator发起人角色
记录当前时刻的内部状态，负责定义哪些属于备份范围的状态，负责创建和恢复备忘录数据。
* Memento备忘录角色（简单的javabean）
负责存储Originator发起人对象的内部状态，在需要的时候提供发起人需要的内部状态。
* Caretaker备忘录管理员角色（简单的javabean）
对备忘录进行管理、保存和提供备忘录。
 
**使用场景：**
* 需要保存和恢复数据的相关状态场景。
* 提供一个可回滚（rollback）的操作。
* 需要监控的副本场景中。
* 数据库连接的事务管理就是用的备忘录模式。

**注意：**
* 备忘录的生命期
* 备忘录的性能
   不要在频繁建立备份的场景中使用备忘录模式（比如一个for循环中）。    
 
**clone方式备忘录：**

* 发起人角色融合了发起人角色和备忘录角色，具有双重功效
 
**多状态的备忘录模式:**
* 增加了一个BeanUtils类，其中backupProp是把发起人的所有属性值转换到HashMap中，方便备忘录角色存储。restoreProp方法则是把HashMap中的值返回到发起人角色中。
 
**BeanUtil工具类代码：**
```
public class BeanUtils {
     //把bean的所有属性及数值放入到Hashmap中
     public static HashMap<String,Object> backupProp(Object bean){
             HashMap<String,Object> result = new HashMap<String,Object>();
             try {
                     //获得Bean描述
                     BeanInfo beanInfo=Introspector.getBeanInfo(bean.getClass());
                     //获得属性描述
                     PropertyDescriptor[] descriptors=beanInfo.getPropertyDescriptors();
                     //遍历所有属性
                     for(PropertyDescriptor des:descriptors){
                             //属性名称
                             String fieldName = des.getName();
                             //读取属性的方法
                             Method getter = des.getReadMethod();
                             //读取属性值
                             Object fieldValue=getter.invoke(bean,new Object[]{});
                    if(!fieldName.equalsIgnoreCase("class")){
                             result.put(fieldName, fieldValue);
                    }
               }
          } catch (Exception e) {
               //异常处理
          }
          return result;
     }
     //把HashMap的值返回到bean中
     public static void restoreProp(Object bean,HashMap<String,Object> propMap){
try {
               //获得Bean描述
               BeanInfo beanInfo = Introspector.getBeanInfo(bean.getClass());
               //获得属性描述
               PropertyDescriptor[] descriptors = beanInfo.getPropertyDescriptors();
               //遍历所有属性
               for(PropertyDescriptor des:descriptors){
                    //属性名称
                    String fieldName = des.getName();
                    //如果有这个属性
                    if(propMap.containsKey(fieldName)){
                         //写属性的方法
                         Method setter = des.getWriteMethod();
                         setter.invoke(bean, new Object[]{propMap.get(fieldName)});
                    }
               }
          } catch (Exception e) {
               //异常处理
               System.out.println("shit");
               e.printStackTrace();
          }
     }
}
```
**封装得更好一点：保证只能对发起人可读**

* 建立一个空接口IMemento——什么方法属性都没有的接口，然后在发起人Originator类中建立一个内置类（也叫做类中类）Memento实现IMemento接口，同时也实现自己的业务逻辑。

#### 结构型
1. Composite，组合模式：将对象组合成树形结构以表示部分整体的关系，Composite使得用户对单个对象和组合对象的使用具有一致性。

* Component抽象构件角色
定义参加组合对象的共有方法和属性，可以定义一些默认的行为或属性，比如我们例子中的getInfo就封装到了抽象类中。
* Leaf叶子构件
叶子对象，其下再也没有其他的分支，也就是遍历的最小单位。
* Composite树枝构件
树枝对象，它的作用是组合树枝节点和叶子节点形成一个树形结构。
 
**树枝构件的通用代码：**
```
public class Composite extends Component {
     //构件容器
     private ArrayList<Component> componentArrayList = new ArrayList<Component>();
     //增加一个叶子构件或树枝构件
     public void add(Component component){
             this.componentArrayList.add(component);
     }
     //删除一个叶子构件或树枝构件
     public void remove(Component component){
this.componentArrayList.remove(component);
     }
     //获得分支下的所有叶子构件和树枝构件
     public ArrayList<Component> getChildren(){
             return this.componentArrayList;
     }
}
```
**使用场景：**
* 维护和展示部分-整体关系的场景，如树形菜单、文件和文件夹管理。
* 从一个整体中能够独立出部分模块或功能的场景。

**注意：**
只要是树形结构，就考虑使用组合模式。

2. Facade，门面模式：为子系统中的一组接口提供一致的界面，facade提供了一高层接口，这个接口使得子系统更容易使用。

* Facade门面角色
客户端可以调用这个角色的方法。此角色知晓子系统的所有功能和责任。一般情况下，本角色会将所有从客户端发来的请求委派到相应的子系统去，也就说该角色没有实际的业务逻辑，只是一个委托类。
* subsystem子系统角色
可以同时有一个或者多个子系统。每一个子系统都不是一个单独的类，而是一个类的集合。子系统并不知道门面的存在。对于子系统而言，门面仅仅是另外一个客户端而已。
 
**使用场景：**
* 为一个复杂的模块或子系统提供一个供外界访问的接口
* 子系统相对独立——外界对子系统的访问只要黑箱操作即可
* 预防低水平人员带来的风险扩散

**注意：**
* 一个子系统可以有多个门面
* 门面不参与子系统内的业务逻辑

3. Proxy，代理模式：为其他对象提供一种代理以控制对这个对象的访问

* Subject抽象主题角色
抽象主题类可以是抽象类也可以是接口，是一个最普通的业务类型定义，无特殊要求。
* RealSubject具体主题角色
也叫做被委托角色、被代理角色。它才是冤大头，是业务逻辑的具体执行者。
* Proxy代理主题角色
也叫做委托类、代理类。它负责对真实角色的应用，把所有抽象主题类定义的方法限制委托给真实主题角色实现，并且在真实主题角色处理完毕前后做预处理和善后处理工作。
 
**普通代理和强制代理：**
普通代理就是我们要知道代理的存在，也就是类似的GamePlayerProxy这个类的存在，然后才能访问；
强制代理则是调用者直接调用真实角色，而不用关心代理是否存在，其代理的产生是由真实角色决定的。

* 普通代理：
在该模式下，调用者只知代理而不用知道真实的角色是谁，屏蔽了真实角色的变更对高层模块的影响，真实的主题角色想怎么修改就怎么修改，对高层次的模块没有任何的影响，只要你实现了接口所对应的方法，该模式非常适合对扩展性要求较高的场合。
 
* 强制代理：
强制代理的概念就是要从真实角色查找到代理角色，不允许直接访问真实角色。高层模块只要调用getProxy就可以访问真实角色的所有方法，它根本就不需要产生一个代理出来，代理的管理已经由真实角色自己完成。
 
* 动态代理：
根据被代理的接口生成所有的方法，也就是说给定一个接口，动态代理会宣称“我已经实现该接口下的所有方法了”。

两条独立发展的线路。动态代理实现代理的职责，业务逻辑Subject实现相关的逻辑功能，两者之间没有必然的相互耦合的关系。通知Advice从另一个切面切入，最终在高层模块也就是Client进行耦合，完成逻辑的封装任务。
 
4. Adapter,适配器模式：将一类的接口转换成客户希望的另外一个接口，Adapter模式使得原本由于接口不兼容而不能一起工作那些类可以一起工作。

* Target目标角色
该角色定义把其他类转换为何种接口，也就是我们的期望接口，例子中的IUserInfo接口就是目标角色。
* Adaptee源角色
你想把谁转换成目标角色，这个“谁”就是源角色，它是已经存在的、运行良好的类或对象，经过适配器角色的包装，它会成为一个崭新、靓丽的角色。
* Adapter适配器角色
适配器模式的核心角色，其他两个角色都是已经存在的角色，而适配器角色是需要新建立的，它的职责非常简单：把源角色转换为目标角色，怎么转换？通过继承或是类关联的方式。

**使用场景：**
你有动机修改一个已经投产中的接口时，适配器模式可能是最适合你的模式。比如系统扩展了，需要使用一个已有或新建立的类，但这个类又不符合系统的接口，怎么办？使用适配器模式，这也是我们例子中提到的。

**注意事项：**
详细设计阶段不要考虑使用适配器模式，使用主要场景为扩展应用中。
 
**对象适配器：**
 对象适配器和类适配器的区别：
类适配器是类间继承，对象适配器是对象的合成关系，也可以说是类的关联关系，这是两者的根本区别。（实际项目中对象适配器使用到的场景相对比较多）。

5. Decrator，装饰模式：动态地给一个对象增加一些额外的职责，就增加的功能来说，Decorator模式相比生成子类更加灵活。

* Component抽象构件
Component是一个接口或者是抽象类，就是定义我们最核心的对象，也就是最原始的对象，如上面的成绩单。
注意：在装饰模式中，必然有一个最基本、最核心、最原始的接口或抽象类充当Component抽象构件。
* ConcreteComponent 具体构件
ConcreteComponent是最核心、最原始、最基本的接口或抽象类的实现，你要装饰的就是它。
* Decorator装饰角色
一般是一个抽象类，做什么用呢？实现接口或者抽象方法，它里面可不一定有抽象的方法呀，在它的属性里必然有一个private变量指向Component抽象构件。
* 具体装饰角色
ConcreteDecoratorA和ConcreteDecoratorB是两个具体的装饰类，你要把你最核心的、最原始的、最基本的东西装饰成其他东西，上面的例子就是把一个比较平庸的成绩单装饰成家长认可的成绩单。

**使用场景：**
* 需要扩展一个类的功能，或给一个类增加附加功能。
* 需要动态地给一个对象增加功能，这些功能可以再动态地撤销。
* 需要为一批的兄弟类进行改装或加装功能，当然是首选装饰模式。

6. Bridge，桥模式：将抽象部分与它的实现部分相分离，使他们可以独立的变化。

* Abstraction——抽象化角色
它的主要职责是定义出该角色的行为，同时保存一个对实现化角色的引用，该角色一般是抽象类。
* Implementor——实现化角色
它是接口或者抽象类，定义角色必需的行为和属性。
* RefinedAbstraction——修正抽象化角色
它引用实现化角色对抽象化角色进行修正。
* ConcreteImplementor——具体实现化角色
它实现接口或抽象类定义的方法和属性。
 
**使用场景：**
* 不希望或不适用使用继承的场景
* 接口或抽象类不稳定的场景
*  重用性要求较高的场景
 
**注意：**
发现类的继承有N层时，可以考虑使用桥梁模式。桥梁模式主要考虑如何拆分抽象和实现。

7. Flyweight，享元模式 （使用共享对象可有效地支持大量的细粒度的对象。）对象的信息分为两个部分：内部状态（intrinsic）与外部状态（extrinsic）。

* 内部状态
内部状态是对象可共享出来的信息，存储在享元对象内部并且不会随环境改变而改变。
* 外部状态
外部状态是对象得以依赖的一个标记，是随环境改变而改变的、不可以共享的状态。

* Flyweight——抽象享元角色
它简单地说就是一个产品的抽象类，同时定义出对象的外部状态和内部状态的接口或实现。
* ConcreteFlyweight——具体享元角色
具体的一个产品类，实现抽象角色定义的业务。该角色中需要注意的是内部状态处理应该与环境无关，不应该出现一个操作改变了内部状态，同时修改了外部状态，这是绝对不允许的。
* unsharedConcreteFlyweight——不可共享的享元角色
不存在外部状态或者安全要求（如线程安全）不能够使用共享技术的对象，该对象一般不会出现在享元工厂中。
* FlyweightFactory——享元工厂
职责非常简单，就是构造一个池容器，同时提供从池中获得对象的方法。
 
**享元工厂的代码：**
```
public class FlyweightFactory {
     //定义一个池容器
     private static  HashMap<String,Flyweight> pool= new HashMap<String,Flyweight>();
     //享元工厂
     public static Flyweight getFlyweight(String Extrinsic){
             //需要返回的对象
             Flyweight flyweight = null;
             //在池中没有该对象
             if(pool.containsKey(Extrinsic)){
                     flyweight = pool.get(Extrinsic);
             }else{
                     //根据外部状态创建享元对象
                     flyweight = new ConcreteFlyweight1(Extrinsic);
                     //放置到池中
                     pool.put(Extrinsic, flyweight);
             }
             return flyweight;
     }
}
```
 **使用场景：**
* 系统中存在大量的相似对象。
* 细粒度的对象都具备较接近的外部状态，而且内部状态与环境无关，也就是说对象没有特定身份。
* 需要缓冲池的场景。

**注意：**
* 享元模式是线程不安全的，只有依靠经验，在需要的地方考虑一下线程安全，在大部分场景下不用考虑。对象池中的享元对象尽量多，多到足够满足为止。
* 性能安全：外部状态最好以java的基本类型作为标志，如String，int，可以提高效率。


