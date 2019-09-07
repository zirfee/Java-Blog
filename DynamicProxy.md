实现动态代理,代理类与被代理类必须存在关系,即:

a 代理类继承被代理类
b 两类共同实现某个接口

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
              //     newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(),invocationHandler类)
              // 参数一:获得被代理对象的类加载器,因为代理对象动态生成,没有被虚拟机加载,必须指定适合的类加载器来解析注册代理类以使用
              //参数二:获得被代理类的接口
              //参数三:获得invocationHandler对象,类似于new InvocayionHandler()
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(),
                new InvocationHandler() {
                      //由代理类调用方法时会自动传到invocationhandler对象执行invoke方法,invoke方法写提供织入逻辑
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("开启事务"); //方法前织入的代码

                        // 执行目标对象方法
                        Object returnValue = method.invoke(target, args); //原本的方法执行

                        System.out.println("提交事务");方法执行后植入
                        return null;
                    }
                });
    }
    } 



















