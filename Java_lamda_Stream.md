# lamda表达式
java中lamda表达式的出现是为了弥补java函数式编程能力的不足

在javascript中,可以很轻易地定义一个函数:

    var fun = function(){
    }
  
java8之前函数封装在对象的方法里面,调用函数需要先实例化:

    button.addActionListener(new ActionListener() { //匿名内部类
    @Override
    public void actionPerformed(ActionEvent e) {
    System.out.println("The button was clicked using old fashion code!");
    }
    });
  
 actionListener是一个**函数式接口**
 ## 函数式接口
 函数式接口(Functional Interface)就是一个有且仅有一个抽象方法，但是可以有多个非抽象方法的接口。
 函数式接口可以被隐式转换为 lambda 表达式。
 可以自定义函数式接口 用@FunctionalInterface标记
 
 定义了一个函数式接口如下：
 
    @FunctionalInterface
    interface fun{
    void say(String s);
    }

给该接口增加处理逻辑:
  
    fun f = message -> System.out.println("Hello " + message);
           f.say("777");//执行
           
函数式接口对像与lambda表达式等价,表达式可以当作接口对象参数接受:

    public void del(fun f){
      f.say("777");
    }
    
    del(el -> System.out.println("Hello " + el));
    
# Stream
 java8中Stream主要简化对collection对象的操作
 
 -  list.Stream() //创建串行流,单线程
 -  list.ParallelStream()  //创建并行流,多线程执行,效率高
 
  

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
 
 
