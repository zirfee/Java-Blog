    该动作 String a= new String("张三");
     实际有两步:
     String a="张三";  //常量池中声明
     new String("张三");  //队中开辟内存
     
     观察以下代码:
        String a = new String("张三");
        System.out.println(a.intern()==a); //false
       以及
          String b="张三";
          String a = new String("张三");
        System.out.println(a.intern()==b); //true
     可验证
