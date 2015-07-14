# HelloWorld
>总算成功了，哈哈，完成了github
>怎么又出问题了
终于好了
shenmegui
#Git has a mutable index called stage.
#add content
#单件模式
单件模式主要是保证了在一个类只有一个实例，并提供了全局访问。
###组成：
1. 定义一个该类的private static变量，确保是创建一次
2. 设置其无参构造器为private
3. 提供一个全局访问点，即public static 返回该类的实例化。

当然以上只是基本的组成，在面对不同的场景下（比如多线程）就需要采用不同的做法，一般，单件模式分为两种，一种是延迟实例化，一种是急切实例化。
###延迟实例化：
```
public class Singleton {
	private static Singleton singleton ;
	private Singleton(){ }
	public static synchronized Singleton getInstance(){
		if(singleton == null){
		    singleton = new Singleton();
		}
		return singleton;
	}
}
```
###双重检查加锁:
```
public class Singleton {
	private volatile static Singleton singleton ;
	private Singleton(){ }
	public static Singleton getInstance(){
		if(singleton == null){
			synchronized(Singleton.class){
				if(singleton == null){
					singleton = new Singleton();
				}
			}
		}
		return singleton;
	}
}
```
###急切实例化：
```
public class Singleton {
	private static Singleton singleton = new Singleton();
	private Singleton(){ }
	public static Singleton getInstance(){
		return singleton;
	}
}
```
当然，有些人认为双重检查加锁要比急切实例化好，因为可以在使用到的时候在创建对象，这样可以节省一部分资源。但实际上，建议使用急切实例化，我们从上面可以看到双重检查加锁已经比急切实例化复杂的多，这个方法，在《Java并发编程实践》中就专门强调了延迟实例化的问题，建议急切实例化。
>注意：通过Java反射机制是能够实例化构造方法为private的类的，那基本上会使所有的Java单例实现失效。

####优点：
1. 可以保证一个类可以只被实例化一次，并提供全局的访问。
2. 灵活性：由类自己控制实例化过程。

####缺点：
1. 使用反射机制时会让单件模式失效。
2. 增加开销，因为会使用同步机制等。
3. 对象的生命周期不明确，没有指定何时注销。

###应用场景：
在开发过程中，有些时候只需要一个对象，如缓存，对话框，注册表，数据库连接或线程池等对象，为了确保在整个应用程序中能够保证该对象是唯一的，引入了单件模式。
###具体应用：
#####singleton在runtime中的应用：

```
public class Runtime {
    private static Runtime currentRuntime = new Runtime();
    public static Runtime getRuntime() {
        return currentRuntime;
    }
    private Runtime() {}

    public void exit(int status) {
        SecurityManager security = System.getSecurityManager();
        if (security != null) {
            security.checkExit(status);
        }
        Shutdown.exit(status);
    }
    ......
}
```
我们可以看到在Java Runtime类中，采用的是急切实例化方法来完成，首先定义了一个私有的静态变量，并进行了初始化。然后定义了静态的getRuntime（）方法来提供全局的访问。最后将构造方法定义为私有访问权限。