泛型在逻辑上可以接受任何对象,在编译完成后固化了类型:


    public class heap<T> {
    private T key=null;

    heap(T t){
        this.key=t;
    }

    public static void main(String[] args){
    
       heap<String>  demo = new heap<String>("测试");
         demo.key; //String字符串
     
    }
    }

一旦以泛型方式实例化对象,编译时就会按照给定的实例化参数(代码中为Stirng)来确定"T"的类型,编译完成转化为下面的样子:
    
    public class heap {
    private String key=null;

    heap(String t){
        this.key=t;
    }

    public static void main(String[] args){

       heap  demo = new heap("测试");
         demo.key;//String字符串
    }
    }

之所以要把泛型类转化为普通类,是因为java虚拟机并不认识泛型类,即泛型类是无法直接执行的,
所以编译器遇到泛型类时就会把泛型类根据实例化时指定的T类型转化为上面的普通类,虚拟机就可以执行

一旦实例化时指定T的类型,T的类型就确定了(泛型类转化为了普通类),给T赋其他类型的值就会报错,就像上面的代码只能给key赋值String了
比如rrrayList<String>就只能add(String)而不能添加其他对象

泛型类总是需要编译器转化为普通类,但如果实例化时不指定T的类型呢?我们用普通方式实例化:

    public class heap<T> {
    private T key=null;

    heap(T t){
        this.key=t;
    }

    public static void main(String[] args){
    
        heap  demo = new heap("测试"); //普通实例化,不给T指定类型
         demo.key;//object对象
     
    }
    }

运行后发现,key变成了object对象,事实上,普通实例化也可以实例化泛型类,只是如果不指定泛型参数T的类型的话,编译器默认把T当成object对象,等价于:

    public class heap {
    private Object key=null;

    heap(Object  t){
        this.key=t;
    }

    public static void main(String[] args){
    
        heap  demo = new heap("测试"); //普通实例化,不给T指定类型
         demo.key;//object对象
     
    }
    }

执行后key自然就是object了

泛型通配符"?"

值得一提的是,形如List<father>和List<son>这样的参数存在继承关系的泛型类,泛型之间本身并没有任何关系.比如定义以下类:
     
     class father{
        public void say(){
           System.out.print("Im father")
        }
     }
     
      class son extends father{
        public void say(){
           System.out.print("Im son")
        }
     }
    
  生成类对象:
 
    father fa1  = new father();
    father fa2  = new father();
    son  son1 = new son();
    son  son2 = new son();
     
 我们用两种ArrayList 放入上面类的对象
   
    List<father> faList = new ArraList();
            falist.add(fa1);
            falist.add(fa2);
    List<son> sonList = new ArrayList();
            sonList.add(son1);
            sonList.add(son2);
     
现在假定某个类有一个test方法
   
    pubiic void  test(List<father> fa)  //该方法接受List<father>参数
        father f =fa.get(0);
              f.say()  
    }
 
   自然可以传入faList
        
        test(falist); //编译成功
        
   进一步的,是否可以传入sonList呢?把List<son> 当作List<father>的子类,多态实现,就像下面:
       
       test(sonList);
    
   似乎没有什么不妥,而且运行时还可以向上转型运用多态的特性,遗憾的是,这样的编译并不能通过
       
       test(sonList);//编译错误
        
  也就是说,List<son> 并不是List<father>的子类,实际上两者并没有任何关系
  泛型类和方法编译时会去泛型化,编译成功后方法test()就已经固定了,只能接受List<father>,传入List<son>会报错
   这就有了一个问题,如果我想接受List<son>参数,必须另写一个方法或者重载test()方法,如:
    
        pubiic void  test(List<son> son)  //重载的test方法,参数不同
            son s =son.get(0);
              s.say()  
    }
   
   这显然会造成大量重复代码,不利于维护,通配符"?"的出现就是为了解决这个问题,可以这样写test方法:
   
        pubiic void  test(List<? extends father> demo)  {
               father fa = demo.get(0);
                   fa.say();
        }
        
   现在,你可以在test方法里传入father或father的任何子类,类似于:
   
         test( List<father>  fa) 
   或
   
        test( List<son> son)
    
  不会再有任何的问题,能够向上转型和使用多态,相当于List<? extends father> 是List<son>的父类,List<fahter> 与List<son>无关
         
  为什么会有这种区别?   
  猜测 void test(List<? extends father>) 和 void test(List<fahter>)方法编译时的处理不同
  void test(List<fahter>)编译时直接固定参数类型为List<father>,除此之外不再接受其他任何类型的参数
   而void test(List<? extends father>)编译时会根据调用时传入的参数动态调整,传入List<father>方法编译为void test(List<fahter> fa),
   传入List<son>时方法编译为void test(List<fahter> son)
   两种方法本质是一样的,最终都是要固定一种类,后者可能更灵活一些,可以从一些类中选择一种类固定,增加灵活性的后果是
   前者可以add(),后者由于不能确定传入什么类,所以不能add()
         
    
### ? extends 和 ? super

  ? extends   传入子类,由于不能确定到底是哪个子类,所以不能有add()方法:
  
             public  void test(List<? extends father> demo){
                    
                  demo.add()//编译出错,如果传入List<son>,add(father)的话报错2
            }
            
  ? extends father  的目的就是为了增加灵活性,方法test()可以复用,利用多态,无论传入什么对象;如果有add()方法,必须根据传入对象的不同
   重写add()方法(避免子类list添加父类对象),就会生成多个test方法,这样就没有用通配符的意义了,没法写add()方法
       
  ? extends super 相反,不再赘述
  
    
    











































