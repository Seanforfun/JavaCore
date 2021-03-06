# Builder 建造者模式
> Builder模式的意义在于生成复杂的实例化对象之前对对象进行设置和初始化。
> 分离了每一个设置的步骤，让对每一个参数设定的顺序可以得到宏观调控。
> 是将一个复杂的对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

![Builder](https://i.imgur.com/JSPMobP.png)

### 建造者模式中的对象
1. builder（抽象建造者）：给出一个抽象结论，以规范产品对象的各个组成成分的建造。这个接口规定要实现复杂对象的那些部分的创建，并不涉及具体的对象部件的创建。
2. ConcreteBuilder（具体建造者）：实现Builder接口，针对不同的商业逻辑，具体化复杂对象的各部分的创建。在构造过程完成后，提供产品的实例。
3. Director（指导者）：调用具体建造者来创建复杂对象的各个部分，在指导者中不涉及具体产品的信息，只负责保证对象各部分完整创建或按某种顺序创建。
4. Product（产品类）：要创建的复杂对象。

### Builder Pattern
1. 要生成的复杂对象
```Java
/**
 * @author SeanForFun
 * @date  Jun 22, 2018 3:25:33 PM
 * @Description 要生成实例的类。
 * 类的组成相对复杂，需要详细的流程指导类的初始化。
 * @version 1.0
 */
public class Car {
	private String wheel;
	private String engine;
	private String skeleton;
	public String getWheel() {
		return wheel;
	}
	public void setWheel(String wheel) {
		this.wheel = wheel;
	}
	public String getEngine() {
		return engine;
	}
	public void setEngine(String engine) {
		this.engine = engine;
	}
	public String getSkeleton() {
		return skeleton;
	}
	public void setSkeleton(String skeleton) {
		this.skeleton = skeleton;
	}
}
```

2. 对象的创建者的分步创建,每一个部件之间都有先后的顺序但是是相对独立的过程。
```Java
public interface CarBuilder {
	public void buildEngine();
	public void buildSkeleton();
	public void buildWheel();
	public Car buildCar();
}
```

3. 对象创建者的具体实现
```Java
/**
 * @author SeanForFun
 * @date  Jun 22, 2018 3:28:56 PM
 * @Description 具体的builder类，对每一个实例对象的每个部件都有具体的装配过程。
 * 但是每个部分是独立的。
 * @version 1.0
 */
public class ConcreteBuilder implements CarBuilder {
	private Car car;
	public ConcreteBuilder(){//实例在builder中被实例化了。
		car = new Car();
	}
	@Override
	public void buildEngine() {
		car.setEngine("set engine...");
	}
	@Override
	public void buildSkeleton() {
		car.setSkeleton("set skeleton...");
	}
	@Override
	public void buildWheel() {
		car.setWheel("set wheel...");
	}
	@Override
	public Car buildCar() {
		return this.car;
	}
}
```

4. 创建一个监督者，在监督者内部确定了装配的顺序，并返回实例。
```Java
public class CarDirector {	//在监督者中对实例化的设置顺序进行了设定。
	public Car constructCar(CarBuilder builder){
		builder.buildWheel();
		builder.buildSkeleton();
		builder.buildEngine();
		return builder.buildCar();
	}
	public static void main(String[] args) {
		CarBuilder builder = new ConcreteBuilder();
		Car car = new CarDirector().constructCar(builder);	//通过创建者实例化了一个复杂的对象，并隐藏了复杂的初始化设置工作。
		System.out.println(car.getEngine());
		System.out.println(car.getSkeleton());
		System.out.println(car.getWheel());
	}
}
```

### Reference
1. [设计模式：建造者模式（Builder）](https://blog.csdn.net/u013256816/article/details/50978024)