## 装饰模式

定义： 

指在不改变现有对象结构的情况下，动态地给该对象增加一些职责（即增加其额外功能）的模式，它属于对象结构型模式。



结构：

装饰模式主要包含以下角色。

1. 抽象构件（Component）角色：定义一个抽象接口以规范准备接收附加责任的对象。
2. 具体构件（Concrete  Component）角色：实现抽象构件，通过装饰角色为其添加一些职责。
3. 抽象装饰（Decorator）角色：继承抽象构件，并包含具体构件的实例，可以通过其子类扩展具体构件的功能。
4. 具体装饰（ConcreteDecorator）角色：实现抽象装饰的相关方法，并给具体构件对象添加附加的责任。



```java
package decorator;
public class DecoratorPattern
{
    public static void main(String[] args)
    {
        Component p=new ConcreteComponent();
        p.operation();
        System.out.println("---------------------------------");
        Component d=new ConcreteDecorator(p);
        d.operation();
    }
}
//抽象构件角色
interface  Component
{
    public void operation();
}
//具体构件角色
class ConcreteComponent implements Component
{
    public ConcreteComponent()
    {
        System.out.println("创建具体构件角色");       
    }   
    public void operation()
    {
        System.out.println("调用具体构件角色的方法operation()");           
    }
}
//抽象装饰角色
class Decorator implements Component
{
    private Component component;   
    public Decorator(Component component)
    {
        this.component=component;
    }   
    public void operation()
    {
        component.operation();
    }
}
//具体装饰角色
class ConcreteDecorator extends Decorator
{
    public ConcreteDecorator(Component component)
    {
        super(component);
    }   
    public void operation()
    {
        super.operation();
        addedFunction();
    }
    public void addedFunction()
    {
        System.out.println("为具体构件角色增加额外的功能addedFunction()");           
    }
}
```



**透过go-kit的midware实现看装饰模式：**

```go
import (
	"errors"
	"strings"
)

//抽象构件角色： 抽象接口
type StringService interface {
	Uppercase(string) (string, error)
	Count(string) int
}

//具体构件： 实现抽象构件（结构体） -- 结构体实现了接口
type stringService struct{}

func (stringService) Uppercase(s string) (string, error) {
	if s == "" {
		return "", ErrEmpty
	}
	return strings.ToUpper(s), nil
}

func (stringService) Count(s string) int {
	return len(s)
}

var ErrEmpty = errors.New("empty string")

//StringService 的 服务中间件
type ServiceMiddleware func(StringService) StringService

```

```go
//返回值为 StringService的服务中间件: StringService的包装函数
func loggingMiddleware(logger log.Logger) ServiceMiddleware {
	return func(next StringService) StringService {
		return logmw{logger, next}
	}
}

//具体装饰：实现抽象装饰？ 
//省略了抽象装饰？接口？
//结构体实现了StringService(抽象接口)
type logmw struct {
    // 附加职责
	logger log.Logger
    // 具体构件的实例
	StringService  
}

func (mw logmw) Uppercase(s string) (output string, err error) {
	//打印日志
	defer func(begin time.Time) {
		_ = mw.logger.Log(
			"method", "uppercase",
			"input", s,
			"output", output,
			"err", err,
			"took", time.Since(begin),
		)
	}(time.Now())

	//执行原生的StringService的 Uppercase方法
	output, err = mw.StringService.Uppercase(s)
	return
}

func (mw logmw) Count(s string) (n int) {
	defer func(begin time.Time) {
		_ = mw.logger.Log(
			"method", "count",
			"input", s,
			"n", n,
			"took", time.Since(begin),
		)
	}(time.Now())

	//执行原生的StringService的 Count方法
	n = mw.StringService.Count(s)
	return
}

```

