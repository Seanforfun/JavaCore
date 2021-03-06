# Command Pattern 命令模式
> 命令模式是为了解决命令的请求者和命令的实现者之间的耦合关系。
> 易于扩展新的命令。

![Command Pattern 命令模式](https://i.imgur.com/yYJUjzu.png)

### Command Pattern 的实现流程
1. Command：定义命令的统一接口
```Java
public interface ICommand {
	public void exec();	//定义了命令的行为
}
```

2. ConcreteCommand：Command接口的实现者，用来执行具体的命令，某些情况下可以直接用来充当Receiver。
```Java
public class MyCommand implements ICommand {	//在命令中通过接收者执行命令。
	private Receiver receiver;	//其中包含了一个接收者，通过构造器接收一个接收者。
	public MyCommand(Receiver receiver) {
		this.receiver = receiver;
	}
	@Override
	public void exec() {
		this.receiver.action();	//调用接收者，执行当前的命令。
	}
}
```

3. Receiver：命令的实际执行者
```Java
public class Receiver {
	public void action(){	//接收者是实际上的命令执行者，在命令中被调用。
		System.out.println("Command Received!");
	}
}
```

4. Invoker：命令的请求者，是命令模式中最重要的角色。这个角色用来对各个命令进行控制。
```Java
public class Invoker {
	private ICommand command;
	public Invoker(ICommand command){	//命令被接收并且实际调用
		this.command = command;
	}
	public void invoke(){	//命令执行
		command.exec();
	}
	public static void main(String[] args) {
		Receiver receiver = new Receiver();	//定义一个对于命令的处理类，在类中定义命令执行的行为。
		ICommand command = new MyCommand(receiver);	//定义一个命令，该命令将会接收一个接收者，用于对命令的实际调用。
		Invoker invoker = new Invoker(command);	//定义一个最上层的命令接收者，接收命令。这个命令的实施者，他会对命令进行解析执行，并隐藏底层的实现，进行解耦。
		invoker.invoke();
	}
}
Command Received!
```

### Reference
1. [理解设计模式之----命令模式](https://www.cnblogs.com/konck/p/4199907.html)