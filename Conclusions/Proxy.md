# Proxy Pattern
> 代理模式就是多一个代理类出来，替原对象进行一些操作。
> 在我们使用代理模式的时候需要生成一个新的对象。

### 为什么需要使用代理
1. 调用对象本身是一件很耗时的事情，因为对象可能正在被占用，需要等很长时间才能对象才能被业务释放。所以我们可以在本地通过代理工厂生成一个代理，代为接收信息，做出相应。
2. 实现横向增强。

### 通过new生成新的对象进行加强
1. 首先定义一个接口，并实现这个接口。
```Java
public interface Sourcable {
	public void method();
}
public class Source implements Sourcable {
	@Override
	public void method() {
		System.out.println("This is a method...");
	}
}
```

2. 定义一个接口类实现要增强的方法，在接口的内部新建一个对象的实例，并通过对该实例方法的重写，在重写中对实例的方法进行调用，并在调用前后进行增强。
```Java
public class SourceProxy implements Sourcable{	//继承要实现的接口
	private final Sourcable source;
	public SourceProxy(){
		source = new Source();	//在类的内部重新生成一个对象作为代理。
	}
	public void before(){
		System.out.println("Advice before...");
	}
	public void after(){
		System.out.println("Advice after...");
	}
	@Override
	public void method() {	//重写要增强的方法
		before();
		source.method();	//调用方法，在方法的前后进行织入
		after();
	}
	public static void main(String[] args) {
		Sourcable s = new SourceProxy();
		s.method();
	}
}
```

### 通过JDK中的Proxy类进行增强
要求：该类必须实现接口
```Java
public class ProxyFactory<T> {
	private T target;	//传入一个实例，用于生成该实例的对象。
	public ProxyFactory(T target){
		this.target = target;
	}
	@SuppressWarnings("unchecked")
	public T getProxyInstance(){	//生成一个对象的代理
		return (T) Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), new InvocationHandler() {
			@Override
			public Object invoke(Object proxy, Method method, Object[] args)
					throws Throwable {
				beforeAdvice();	//前置增强
				Object ret = method.invoke(target, args);
				afterAdvice();	//后置增强
				return ret;
			}
		});
	}
	private void beforeAdvice(){
		System.out.println("Advice before...");
	}
	private void afterAdvice(){
		System.out.println("Advice after...");
	}
	public static void main(String[] args) {
		ProxyFactory<Sourcable> factory = new ProxyFactory<Sourcable>(new Source());
		Sourcable instance = factory.getProxyInstance();
		instance.method();
	}
}
```

### 通过CgLib实现代理
* 要代理的类不需要实现接口。
* Cglib是一个强大的高性能的代码生成包,它可以在运行期扩展java类与实现java接口.它广泛的被许多AOP的框架使用,例如Spring AOP和synaop,为他们提供方法的interception(拦截)
* 实际上是一个class文件修改器（ASM）
```Java
public class CglibProxyFactory<T> implements MethodInterceptor{
	private final T target;
	public CglibProxyFactory(T target){
		this.target = target;
	}
	@SuppressWarnings("unchecked")
	public T getProxyInstance(){
		Enhancer enhancer = new Enhancer();
		enhancer.setSuperclass(target.getClass());
		enhancer.setCallback(this);	//会调用intercept方法，MethodInterceptor实现了Callback方法，enhancer会生成一个代理对象，在对方法进行调用的时候，会使用intercept方法对方法进行横向增强。
		return (T) enhancer.create();
	}
	@Override
	public Object intercept(Object arg0, Method method, Object[] arg2,
			MethodProxy arg3) throws Throwable {	//增强的方法
		System.out.println("Advice before...");
		Object ret = method.invoke(target, arg2);
		System.out.println("Advice after...");
		return ret;
	}
	public static void main(String[] args) {
		CglibProxyFactory<Bird> factory = new CglibProxyFactory<>(new Bird());
		Bird instance = factory.getProxyInstance();
		instance.shout();
	}
}
```