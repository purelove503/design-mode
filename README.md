# design-mode-
设计模式

#### 定义
- 表示作用于某对象结构中的各元素的操作。它可以使你在不改变各元素的类的前提下定义作用于这些元素的新操作  [引用大话设计模式概念]
-  维护了开放-封闭原则（拓展开放，更改封闭）
#### 应用场景
适用于数据结构相对稳定的系统
例：1. apt（注解处理工具）通过访问者模式解析注解（参照Think in Java P632）；2.大话设计模式中，男人和女人对待不同状态做出不同相应的例子
 #### 优缺点
- 优点
  - 处理与数据结构分离（解耦合） 
    ps: 这里的数据结构举例：人类中只有男人女人，男人和女人就是人类的数据结构
  - 使用双分派
    增加新的操作时，只需要增加新的访问者，无需改动其他代码（可参照下例代码）
    1. 客户端调用时，调用元素的`accept`方法，具体访问者作为参数
    2. `accept`方法中，调用访问者的方法，并将自己（具体元素）作为参数传入
  - 增加新的访问者（新的操作）很容易，
- 缺点
  - 增加新的数据结构会很困难
    例如，人类中增加一个种类，那么所有的访问者都要增加一个对此种类的处理

#### 类图
![访问者模式类图.jpg](https://upload-images.jianshu.io/upload_images/7392673-315b02cf8ea57ee8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 类图中有四个重要元素：`Visitor`、`ConcreteVisitor`、`Element`、`ConcreteElement`
  1. 抽象访问者（Visitor）：为每个`Element`的实现声明一个`Visit`（访问）操作，可为接口或抽象类
  2. 具体访问者（ConcreteVisitor）：实现Visitor的操作
      实现具体的访问行为
  3. 抽象元素（Element）：具体数据结构的抽象
      声明一个`accept`（接受操作），接受一个访问者对象作为参数
  4. 具体元素（ConcreteVisitor）：实现了抽象元素的`accept`
      一般会在`accept`方法的实现中，直接做一次分派

#### 男人与女人的例子
- 引用大话设计模式中男人和女人的例子
- 男人和女人面对不同的状态，有不同的反应
  1. 人类（Element），抽象访问者，拥有一个`accept`（接受状态）的方法，会面临多种状态：成功、失败、恋爱、结婚
  2. 男人、女人（ConcreteVisitor1、ConcreteVisitor2），实现自己的`accept`（接受状态时的反应）。面对状态，都会做出自己的反应
  3. 状态（Visitor），定义两个方法：男人对状态的反应、女人对状态的反应
  4. 具体状态（恋爱、失败等）（ConcreteVisitor1、ConcreteVisitor2、ConcreteVisitor3、ConcreteVisitor4...）
##### 访问者模式代码：
- 抽象元素：人类
```
/**
 * Element（抽象元素）：人类
 */
public interface Person {
    /**
     * 接受状态
     * @param action
     */
    String accept(Action action);
}
```
- 抽象访问者：
```
/**
 * Action（抽象访问者）：状态
 */
public interface Action {
    /**
     * 获取男人的反应
     * @param person
     * @return
     */
    String GetManConclusion(Man person);
    /**
     * 获取女人反应
     * @param person
     * @return
     */
    String GetManConclusion(Woman person);
}
```
- 具体元素：男人、女人
男人
```
/**
 * ConcreteElement（抽象访问者）： 男人
 */
public class Man implements Person {
    @Override
    public String accept(Action action) {
        // 第二次分派，调用action（访问者）的方法，把自己（元素本身）作为参数传入
        return action.GetManConclusion(this);
    }
}
```
女人
```
/**
 * ConcreteElement（抽象访问者）： 女人
 */
public class Woman implements Person {
    @Override
    public String accept(Action action) {
        // 第二次分派，调用action（访问者）的方法，把自己（元素本身）作为参数传入
        return action.GetManConclusion(this);
    }
}
```
- 具体状态：成功、失败、恋爱等等
成功
```
/**
 * ConcreteVisitor(具体访问者)： 成功状态的响应
 */
public class Success implements Action {
    @Override
    public String GetManConclusion(Man person) {
        return "男人成功时，背后总有一个伟大的女人";
    }

    @Override
    public String GetManConclusion(Woman person) {
        return "女人成功时，背后大多有一个不成功的男人";
    }
}
```
失败
```
/**
 * ConcreteVisitor(具体访问者)： 失败状态的响应
 */
public class Failure implements Action {
    @Override
    public String GetManConclusion(Man person) {
        return "男人失败时，闷头喝酒，谁也不用劝";
    }

    @Override
    public String GetManConclusion(Woman person) {
        return "女人失败时，眼泪汪汪，谁也劝不了";
    }
}
```
恋爱
```
/**
 * ConcreteVisitor(具体访问者)： 恋爱状态的响应
 */
public class Love implements Action{
    @Override
    public String GetManConclusion(Man person) {
        return "男人恋爱是，凡事不懂也要装懂";
    }

    @Override
    public String GetManConclusion(Woman person) {
        return "女人恋爱时，遇事懂也装作不懂";
    }
}
```
- 枚举元素
```
/**
 * 用来枚举元素，包含多个元素
 */
public class ObjectStructure {
    private List<Person> elements = new ArrayList<>();

    public void add(Person person) {
        if (null != person) {
            this.elements.add(person);
        }
    }

    public void del(Person person) {
        if (null != person) {
            this.elements.remove(person);
        }
    }

    /**
     * 获取并展示元素对指定状态的响应
     * @param action 指定状态
     */
    public void accept(Action action) {
        if (null == this.elements || 0 >= this.elements.size()) {
            return;
        }
        this.elements.forEach(element -> {
            // 第一次分派，调用元素的接受状态方法，将action（访问者）作为参入
            String result = element.accept(action);
            System.out.println("person: " + element.getClass().getName() + "action: " + action.getClass().getName() +
                    "conclusion: " + result);
        });
    }
}
```
