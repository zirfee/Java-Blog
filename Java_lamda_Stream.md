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
 ### 函数式接口
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
 java8中Stream主要简化对collection对象的操作,生成一个元素队列
 
 -  list.Stream() //创建串行流,单线程
 -  list.ParallelStream()  //创建并行流,多线程执行,效率高
 
  
### forEach
Stream 提供了新的方法 'forEach' 来迭代流中的每个数据。以下代码片段使用 forEach 输出了10个随机数：

    Random random = new Random();
    random.ints().limit(10).forEach(System.out::println);
    
### map
map 方法用于映射每个元素到对应的结果，以下代码片段使用 map 输出了元素对应的平方数：

    List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
    // 获取对应的平方数
    List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
### filter
filter 方法用于通过设置的条件过滤出元素。以下代码片段使用 filter 方法过滤出空字符串：

    List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
    // 获取空字符串的数量
    int count = strings.stream().filter(string -> string.isEmpty()).count();
### limit
 limit 方法用于获取指定数量的流。 以下代码片段使用 limit 方法打印出 10 条数据：

    Random random = new Random();
    random.ints().limit(10).forEach(System.out::println);
### sorted
sorted 方法用于对流进行排序。以下代码片段使用 sorted 方法对输出的 10 个随机数进行排序：

    Random random = new Random();
    random.ints().limit(10).sorted().forEach(System.out::println);
### 并行（parallel）程序
parallelStream 是流并行处理程序的代替方法。以下实例我们使用 parallelStream 来输出空字符串的数量：

    List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
    // 获取空字符串的数量
    int count = strings.parallelStream().filter(string -> string.isEmpty()).count();
我们可以很容易的在顺序运行和并行直接切换。

### Collectors
Collectors 类实现了很多归约操作，例如将流转换成集合和聚合元素。Collectors 可用于返回列表或字符串：

    List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
    List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
 
    System.out.println("筛选列表: " + filtered);
    String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
    System.out.println("合并字符串: " + mergedString);
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
 
 
