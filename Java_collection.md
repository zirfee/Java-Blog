## hash值 

　　文件的hash值相当于文件的"指纹",是根据文件内容经过复杂算法计算出的文件的摘要,一般认为hash值是唯一的
为理解原理,设计一个简单的hash算法: 根据文件的值取8的模为hash值 (为了直观用十进制表示)
 
 	108%8=4
	109%8=5
	110%8=6
	111%8=7
	112%8=0
	116%8=4
  
　　如果文件只有108--112,自然可以用计算后的hash值代表数据本身如用4代表"108",但如果文件增多,有了116,就会发现116的hash值和108一样
如果出现这样的情况,就说明该算法不可靠,毕竟hash是指纹,要求"唯一".
该算法的得到的文件hash值最多有8个,即0--7,这表明一共就有8个指纹,最多也就能加密8个文件,如果有九个不同的文件,加密到第九个时必然会出现hash值
同的情况,事实加密到第五个时就有50%的几率hash值一样了
		 
　　现在的sha2-256 hash算法能计算出256位的hash值,也就是有2^256个指纹,这就意味则在这种算法下想要找到两种hash值相同的文件,确保找到就要尝试2^256
+1个不同文件,几乎不可能,之前的MD5,sha1等算法已经被证明可以花费较小的代价破解,所以不建议使用了
		 
## java集合框架
		 
　　顶层为collection接口,该接口实现了iterator迭代器接口
				 
　　collection有很多抽象方法,如果想要自定义一个collection,实现接口就必须实现全部方法,很不方便,
所以有一个抽象类abstractCollection,该类部分实现了一些常用方法,保留了如iterator()这种抽象方法
所以自定义一个集合类就可以继承abstractCollection,不必实现全部方法,但必须实现抽象方法,
jdk1.8之后完全可以在collection接口中定义default方法,该方法可以有方法体,不必再提供伴随类,对collection类的foreach遍历统统默认使用

#### 迭代器

　　Ierator迭代器可以看作集合框架的通用指针,框架本身不能对指针进行操作,想要移动指针实现集合遍历等常用操作
必须由iterator完成,iterator不指向元素,而是指向元素之间的间隔.iterator有三个方法,hasNext(),next(),remove(),
只有这三个方法.hasNext()判断是否有下一对象,next()返回下一个对象,并向下移动一位指针,指针默认指向第一个对象之前.remove()移除当前指针的上一个对象
				 
#### collection下有list set queue

　　list是有序集合,实现了iterator的子接口ListIterator.除next(),hasnext(),remove()功能外,该接口增加了previous,hasPrevious等向前遍历的操作,
还有add()方法(只用在list),位置同remove(). 注意迭代器的add()方法与继承自collection接口的add()方法不同.list接口有get()方法,根据索引找数据
ArrayList没问题,linkedList是链表结构,用get()效率极低,遍历链表用迭代器,随机访问数据不要用链表.
				 
　　java中所有的链表都是双向链表,无论是ArrayList还是LinkedList,都可以用ListIterator向前向后移动指针 ,迭代器除add(),remove()方法外,
还有set()方法用于覆盖当前对象
				  
　　不允许对集合进行并发修改,如果有多个迭代器指向同一个集合,会有冲突的情况:

    List  list = new ArrayList()
	ListIterater iter1 = list.ListIterater();
	ListIterater iter2 = list.ListIterater();
	iter1.next();
	iter1.remove();
	iter2.next(); //报错
    
　　显然,iter1移除了iter2指向的下一个对象,iter2指向的已经不是原来的集合了,造成冲突.
有一个简单的方法能检测出多个迭代器对集合的并发修改并报错:集合自己维护一个被add()和remove()的次数,每个迭代器维护一个次数,迭代器执行时首先
检查两个次数是否一致,不一致说明有对集合的并发修改,会报错
只有对集合的结构性修改,如add()和remove()才会被计入次数,set()方法不会计入修改次数,所以set()方法可以并发,可以多个迭代器同时执行set
		  
　　hashset是数组加链表的数据结构,每个数组结点称为桶,每个桶跟着一组链表.存入数据时,先算出数据的hash值,与桶数取余.比如hash值为108,桶数为8
,余数为4,该数据就存在第四个桶里.如果桶里没有一个元素,数据会直接插入桶里;如果桶里已经存在元素,被插入的数据就会与该桶里所有元素一一进行比较,
如果已经存在相同元素,放弃操作,如果不存在,插入链表末尾.
由于hash值类似于完全随机数,所以插入的数据一般会平均存放在各个桶里.如果对象重写的hashcode()方法不合适,数据就会集中在某个或某几个桶里,
散列表就会退化成链表					
            
　　treeSet与hashSet类似,不同的是treeSet对插入的元素自动进行排序,迭代遍历时也是按照顺序遍历,所以元素必须是能够排序的元素,
也就是插入的对象所在的类必须实现comparable接口.与hashSet相比,treeSet多出了对元素排序的开销.

#### Map
Map(映射)没有继承collection,但map与collection关系密切.hashMap类比hashSet,原来hashSet的值成为了一个指针,或者叫键,该键指向了一个对象,称为值

treeMap相比hashMap,对键进行了排序,比hashMap增加了排序开销

weakHashMap  map中一直保存着对象的引用(键--对象),导致垃圾对象不能回收,使用weakHashMap会自动回收没有受到其他引用的对象
				 
　　map中存值用put(key,value)方法,put相同的key会覆盖原来的键值对,返回原来的值,remove(key)方法用来删除键值对,取值用get(key)
  
**hashMap** hashSet加上叶子,不再赘述

**hashTable** 线程安全的hashMap,利用Synchronized,锁住整个map对象,开销大.

**concurrentMap** 线程安全的hashMap,通过lock()同步,通过给map对象分片,每次只锁map对象的一片,不妨碍其他片的请求,性能比hashTable好
				 
## 映射视图  
　　map的键集可以看作一个set视图,值集可以看作一个collection视图,set<map.entry<>>也是一个set视图,不过保存的是键值对对象map.entry<>

    set--map.keySet()
    collection--map.values() 
	set<map.entry<>>--map.entrySet()
	for(map.enntry<> entry: map.entrySet()){
	entry.getKey();//输出键
	entry,getValue();//输出值
	}

　　如上,可以用foreach 迭代器遍历这三种视图,输出所有键值对时不要通过get(key)来输出值,效率低,用上面的方法
通过得到的keySet  remove()某个键,会删掉map中对应的键值对,不能add(),只增加键不增加值没有意义
		 
## linkedHashSet 与linkedHashMap
　　现在知道list,treeSet,queue,treeMap都是有顺序的集合,hashSet,hashMap是无序集合,linkedHashSet 与linkedHashMap给这两种无序
集合加上了顺序,访问顺序.
这两种有序集合根据元素的访问顺序在元素之间添加指针,形成了逻辑上的链表结构先被访问的元素添加到链表末尾,添加元素同样被看作访问对象
如
    map.put(a,0)
	map.put(b,1)
    map.put(c,2)
    
　　先把a键值对添加到链表尾,再b,再c,迭代器遍历时keySet顺序是a,b,c,values是0,1,2   ;用get()访问的操作同样会把访问对象置于链表尾
使用这种数据结构的意义在于,迭代器先访问到的永远都是不经常被使用的对象,可以用于对象回收
						
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
		 
