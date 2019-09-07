实现动态代理,代理类与被代理类必须存在关系:

1. 代理类继承被代理类

2. 两类共同实现某个接口

以便于代理类获取必要的信息.

这两种关系各有一种实现方式:

- **基于继承的cglib动态代理**
- **基于接口的jdk动态代理**

**优缺点**:

jdk动态代理动态生成的类类似于这样:

    public final class $Proxy1 extends Proxy implements Subject{
       private InvocationHandler h;
       private $Proxy1(){}
      public $Proxy1(InvocationHandler h){
        this.h = h;
      }
      public int request(int i){
        Method method = Subject.class.getMethod("request", new Class[]{int.class}); //创建method对象
        return (Integer)h.invoke(this, method, new Object[]{new Integer(i)}); //调用了invoke方法
      }
    }

可以看出生成的动态类都继承了Proxy,由于单继承限制并不能再采用关系a的模型.这就要求代理与被代理类必须采用关系b的模式,代理类必须实现某个接口

cglib动态代理的对象不必实现接口,但是生成的代理类默认继承被代理类,这就要求被代理类不能是final类

以上是灵活性方面的差异

性能方面,jdk代理利用的是反射,cglib利用asm(一个小而快的字节码处理框架，来转换字节码并生成新的类),后者稍快


## 代码示例:

**jdk动态代理**:
    
接口IUserDao 

    public interface IUserDao {
    public void save();
    }

被代理类UserDao 

    public class UserDao implements IUserDao{

    @Override
    public void save() {
        System.out.println("保存数据");
     }
    }
    
返回代理类对象的工厂UserProxyFactory

    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;

    public class ProxyFactory {

    private Object target;// 被代理

    public ProxyFactory(Object target) {
        this.target = target;
    }

    // 返回代理对象
    public Object getProxyInstance() {
        //newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(),invocationHandler类对象)
        //参数一:获得被代理对象的类加载器,因为代理对象动态生成,没有被虚拟机加载,必须指定适合的类加载器来解析注册代理类以使用
        //参数二:获得被代理类的接口
        //参数三:类似于new InvocayionHandler(传入被代理类对象),此类对被代理类进行包装,方法的织入,代理对象方法被调用时实际上调用的是此类的invoke方法
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(),
                new InvocationHandler() {
                     
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                     //由代理类调用方法时会自动传到invocationhandler对象执行invoke方法,invoke方法写提供织入逻辑
                      //参数一:代理对象执行方法时,会间接执行invoke()方法,参数一传入的是代理对象本身
                      //参数二:代理对象执行的方法所属的method对象,便于利用反射执行方法
                      //参数三:方法参数
                        System.out.println("开启事务"); //方法前织入的代码

                        // 执行目标对象方法
                        Object returnValue = method.invoke(target, args); //利用反射执行方法,参数一:方法所属对象,参数二:方法参数

                        System.out.println("提交事务");方法执行后植入
                        return null;
                    }
                });
    }
    } 

**cglib动态代理**

使用cglib代理需要导入相应的包

    <dependency>
      <groupId>cglib</groupId>
      <artifactId>cglib</artifactId>
      <version>3.2.5</version>
    </dependency>

目标对象：UserDao

    public class UserDao{

    public void save() {
        System.out.println("保存数据");
    }
    }

代理对象工厂:ProxyFactory

    import java.lang.reflect.Method;
    import net.sf.cglib.proxy.Enhancer;
    import net.sf.cglib.proxy.MethodInterceptor;
    import net.sf.cglib.proxy.MethodProxy;

    public class ProxyFactory implements MethodInterceptor{

    private Object target;//维护一个目标对象
    public ProxyFactory(Object target) {
        this.target = target;
    }
    
    //生成代理对象
    public Object getProxyInstance() {
        //工具类
        Enhancer en = new Enhancer();
        //设置父类
        en.setSuperclass(target.getClass());
        //设置回调函数
        en.setCallback(this); //this代表代理对象实例化时传入了methodInterceptor对象
        //创建子类对象代理
        return en.create();
    }

    @Override    //方法织入,代理对象执行方法时由此方法代替
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("开启事务");
        // 执行目标对象的方法
        Object returnValue = method.invoke(target, args);
        System.out.println("关闭事务");
        return null;
    }
    }














