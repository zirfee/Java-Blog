
object类是所有类的超类,里面定义了一些java对象共有的方法,可由所有子类调用

#### clone()方法 protect

   clone()为浅拷贝,如果克隆的对象里包含了其他对象的引用,新对象包含的也是对象的引用,所以克隆的对象和源对象相比还是会共享一些信息,
   这是clone()为浅拷贝的原因

   一个类想要使用clone()方法复制一个对象,这个类首先要实现cloneable接口,cloneable是一个标记接口,所以实现该接口不需要重写任何方法
    
#### getClass()方法
   
   该方法是一个native方法,native方法就是不由java实现的方法，一般这些方法都是很底层，跟平台结合紧密，或者使用java实现性能很差,
   简而言就是Java中声明的可调用的使用C/C++实现的方法。
     
#### equals()方法
    
   object的equals方法比较内存地址:
             
     public boolean equals(Object obj) {
         return (this == obj);
     }
       //如果子类有需要,可重写equals方法,比如String类就重写了equals方法,让其比较字符的值而不是内存地址.重写equals必须重写hashCode
          
          
           == 和equals
            ==比较的永远是指针的值:
             对于基础类型,引用指针中直接存基础类型的值:
              int a=2;  //a指针的值10
              int b=2;  //b指针的值10
              a==b; //true
             对于引用类型,地址中存实际对象的内存地址:
               demo a= new demo();
               demo b= new demo();
               a==b;//false
     
#### hashCode()方法

   返回对象hash值
   
   所有对象都有一个hashCode方法,该方法默认返回对象内存地址(因为不同对象内存地址不可能相同)
   
   可以自己定义hashCode方法,如String类重写了hashCode()方法,根据字面值计算hashCode,如:
                       String a = new String("张三");
                       String b = new String("张三");
                   System.out.println( a.hashCode());//774889
                   System.out.println( b.hashCode());//774889
             String类重写hashCode是必须的,因为String类重写了equals()方法
             
             
             
             
             
             
             
             
             
             
             
             
             
             
             
             
             
             
             
             
