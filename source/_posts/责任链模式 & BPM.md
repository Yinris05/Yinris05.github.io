---
title: 责任链模式 & BPM
date: 2025-05-29 23:59:54
tag: [设计模式]
---

# 责任链模式

参考链接：⭐[设计模式之责任链模式（Chain of Responsibility）详解及代码示例 - kosamino - 博客园](https://www.cnblogs.com/jing99/p/12610095.html)

[责任链模式 | 菜鸟教程](https://www.runoob.com/design-pattern/chain-of-responsibility-pattern.html)

[Java设计模式之责任链模式（简单易懂）_责任链模式 java-CSDN博客](https://blog.csdn.net/qq_37913997/article/details/121027621)

## 责任链模式的定义与特点

　　责任链（Chain of Responsibility）模式的定义：责任链模式也叫职责链模式，为了避免请求发送者与多个请求处理者耦合在一起，将所有请求的处理者通过前一对象记住其下一个对象的引用而连成一条链；当有请求发生时，可将请求沿着这条链传递，直到有对象处理它为止。

　　在责任链模式中，客户只需要将请求发送到责任链上即可，无须关心请求的处理细节和请求的传递过程，所以责任链将请求的发送者和请求的处理者解耦了。

## 责任链模式优缺点

　　责任链模式是一种对象行为型模式，其主要优点如下：

- 降低了对象之间的耦合度。该模式使得一个对象无须知道到底是哪一个对象处理其请求以及链的结构，发送者和接收者也无须拥有对方的明确信息。
- 增强了系统的可扩展性。可以根据需要增加新的请求处理类，满足开闭原则。
- 增强了给对象指派职责的灵活性。当工作流程发生变化，可以动态地改变链内的成员或者调动它们的次序，也可动态地新增或者删除责任。
- 责任链简化了对象之间的连接。每个对象只需保持一个指向其后继者的引用，不需保持其他所有处理者的引用，这避免了使用众多的 if 或者 if···else 语句。
- 责任分担。每个类只需要处理自己该处理的工作，不该处理的传递给下一个对象完成，明确各类的责任范围，符合类的单一职责原则。

　　其主要缺点如下：

- 不能保证每个请求一定被处理。由于一个请求没有明确的接收者，所以不能保证它一定会被处理，该请求可能一直传到链的末端都得不到处理。
- 对比较长的职责链，请求的处理可能涉及多个处理对象，系统性能将受到一定影响。
- 职责链建立的合理性要靠客户端来保证，增加了客户端的复杂性，可能会由于职责链的错误设置而导致系统出错，如可能会造成循环调用。

## 责任链模式的实现

　　通常情况下，可以通过数据链表来实现职责链模式的数据结构。

　　职责链模式主要包含以下角色。

- 抽象处理者（Handler）角色：定义一个处理请求的接口，包含抽象处理方法和一个后继连接。
- 具体处理者（Concrete Handler）角色：实现抽象处理者的处理方法，判断能否处理本次请求，如果可以处理请求则处理，否则将该请求转给它的后继者。
- 客户类（Client）角色：创建处理链，并向链头的具体处理者对象提交请求，它不关心处理细节和请求的传递过程。

　　其结构图如图所示：

![img](../images/%E8%B4%A3%E4%BB%BB%E9%93%BE%E6%A8%A1%E5%BC%8F%20&%20BPM/1010726-20200401040830080-1006268081.png)

　　客户端可按如图所示设置责任链：

　　　　　　　　　　 ![img](../images/%E8%B4%A3%E4%BB%BB%E9%93%BE%E6%A8%A1%E5%BC%8F%20&%20BPM/1010726-20200401040857491-145900317.png)

```java
public class ChainOfResponsibilityPattern
{
    public static void main(String[] args)
    {
        //组装责任链 
        Handler handler1=new ConcreteHandler1(); 
        Handler handler2=new ConcreteHandler2(); 
        handler1.setNext(handler2); 
        //提交请求 
        handler1.handleRequest("two");
    }
}
//抽象处理者角色
abstract class Handler
{
    private Handler next;
    public void setNext(Handler next)
    {
        this.next=next; 
    }
    public Handler getNext()
    { 
        return next; 
    }   
    //处理请求的方法
    public abstract void handleRequest(String request);       
}
//具体处理者角色1
class ConcreteHandler1 extends Handler
{
    public void handleRequest(String request)
    {
        if(request.equals("one")) 
        {
            System.out.println("具体处理者1负责处理该请求！");       
        }
        else
        {
            if(getNext()!=null) 
            {
                getNext().handleRequest(request);             
            }
            else
            {
                System.out.println("没有人处理该请求！");
            }
        } 
    } 
}
//具体处理者角色2
class ConcreteHandler2 extends Handler
{
    public void handleRequest(String request)
    {
        if(request.equals("two")) 
        {
            System.out.println("具体处理者2负责处理该请求！");       
        }
        else
        {
            if(getNext()!=null) 
            {
                getNext().handleRequest(request);             
            }
            else
            {
                System.out.println("没有人处理该请求！");
            }
        } 
    }
}
```



## 责任链模式的应用实例

　　用责任链模式设计一个请假条审批模块：假如规定学生请假小于或等于 2 天，班主任可以批准；小于或等于 7 天，系主任可以批准；小于或等于 10 天，院长可以批准；其他情况不予批准。

　　首先，定义一个领导类（Leader），它是抽象处理者，包含了一个指向下一位领导的指针 next 和一个处理假条的抽象处理方法 handleRequest(int LeaveDays)；然后，定义班主任类（ClassAdviser）、系主任类（DepartmentHead）和院长类（Dean），它们是抽象处理者的子类，是具体处理者，必须根据自己的权力去实现父类的 handleRequest(int LeaveDays) 方法，如果无权处理就将假条交给下一位具体处理者，直到最后；客户类负责创建处理链，并将假条交给链头的具体处理者（班主任）。代码如下所示：

```JAVA
public class LeaveApprovalTest
{
    public static void main(String[] args)
    {
        //组装责任链 
        Leader leader1=new ClassAdviser();
        Leader leader2=new DepartmentHead();
        Leader leader3=new Dean();
        leader1.setNext(leader2);
        leader2.setNext(leader3);
        //提交请求 
        leader1.handleRequest(8);
    }
}
//抽象处理者：领导类
abstract class Leader
{
    private Leader next;
    public void setNext(Leader next)
    {
        this.next=next; 
    }
    public Leader getNext()
    { 
        return next; 
    }   
    //处理请求的方法
    public abstract void handleRequest(int LeaveDays);       
}
//具体处理者1：班主任类
class ClassAdviser extends Leader
{
    public void handleRequest(int LeaveDays)
    {
        if(LeaveDays<=2) 
        {
            System.out.println("班主任批准您请假" + LeaveDays + "天。");       
        }
        else
        {
            if(getNext() != null) 
            {
                getNext().handleRequest(LeaveDays);             
            }
            else
            {
                  System.out.println("请假天数太多，没有人批准该假条！");
            }
        } 
    } 
}
//具体处理者2：系主任类
class DepartmentHead extends Leader
{
    public void handleRequest(int LeaveDays)
    {
        if(LeaveDays<=7) 
        {
            System.out.println("系主任批准您请假" + LeaveDays + "天。");       
        }
        else
        {
            if(getNext() != null) 
            {
                  getNext().handleRequest(LeaveDays);             
            }
            else
            {
                System.out.println("请假天数太多，没有人批准该假条！");
           }
        } 
    } 
}
//具体处理者3：院长类
class Dean extends Leader
{
    public void handleRequest(int LeaveDays)
    {
        if(LeaveDays<=10) 
        {
            System.out.println("院长批准您请假" + LeaveDays + "天。");       
        }
        else
        {
              if(getNext() != null) 
            {
                getNext().handleRequest(LeaveDays);             
            }
            else
            {
                  System.out.println("请假天数太多，没有人批准该假条！");
            }
        } 
    } 
}
```

　　如果现在增加一个教务处长类，可以批准学生请假 20 天，也非常简单，代码如下：

```JAVA
//具体处理者4：教务处长类
class DeanOfStudies extends Leader
{
    public void handleRequest(int LeaveDays)
    {
        if(LeaveDays<=20) 
        {
            System.out.println("教务处长批准您请假"+LeaveDays+"天。");       
        }
        else
        {
              if(getNext()!=null) 
            {
                getNext().handleRequest(LeaveDays);          
            }
            else
            {
                  System.out.println("请假天数太多，没有人批准该假条！");
            }
        } 
    } 
}
```

然后在组装责任链上直接添加上教务处长即可：

```
Leader leader4=new DeanOfStudies();
leader3.setNext(leader4);
```

# BPM

# 责任链模式 VS BPM

