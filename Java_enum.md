枚举类的目的就是为了定义不可变的全局对象
传统定义方式:

	class Day{
	public static final int MONDAY =1;
        public static final int TUESDAY=2;
	public static final int WEDNESDAY=3;
	public static final int THURSDAY=4;
	public static final int FRIDAY=5;
	public static final int SATURDAY=6;   
	public static final int SUNDAY=7;
	}

使用day.MONDAY访问值

枚举定义方式 
enum Day {
    MONDAY(1), TUESDAY(2), WEDNESDAY(3),
    THURSDAY(4), FRIDAY(5), SATURDAY(6), SUNDAY(7)
    
     private int dayValue;  //描述1,2,3,4,
     
    private Day(int dayValue){   //private 构造,编译器自动
        this.dayValue=dayValue;
    }
    
    public String toStirng(){
         return dayValue;
    }
}
   同样用Day.MONDAY访问,编译器为枚举类自动插入静态方法values()和valuesOf(String),
   Day.values()返回所有类实例数组,用Day[]接受;Day.valuesOf(MONDAY)返回单个Day实例,不能向上转型访问
   
   
   

 可以用enum关键字定义一个枚举类,同泛型类一样,编译器编译时会把枚举类转化为普通类
       enum class day--->final class Day extends Enum
       
   所有枚举类都是enum类的子类,enum类实现了comparable接口,主要有以下方法:
       ordinal()--返回枚举类对象的声明顺序值,如MONDAY返回0,TUESDAY返回1
       comparaTo()--根据上面的ordidary()方法返回值排序
       name(),toString()--返回枚举对象名

枚举类定义抽象方法,必须在本类重写,因为enum类final:
 public enum enumDemo {
    MONDAY(1){
        @Override
        public void say() {
            System.out.println("my value is "+enumDemo.MONDAY.details);
        }
    };

    private int details;
    private enumDemo(int details){
        this.details=details;
    }

    @Override
    public String toString() {
        return String.valueOf(this.details);
    }

    public abstract void say();

    public static void main(String[] args){
        enumDemo.MONDAY.say();
    }
}

待续......
