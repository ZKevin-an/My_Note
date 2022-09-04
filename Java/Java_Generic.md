# 泛型(Generic)
```
集合容器类在设计阶段不确定存放的类型，所以在JDK1.5之前都将元素设计为Object，JDK1.5之后通过泛型解决，因为这个时候只有元素类型不确定，如何管理使用都是确定的，因此把元素的类型设计成一个参数，这个类型参数叫做泛型。

概念：允许在定义类、接口时通过一个标识标识类中某个属性的类型或者某个方法的返回值即参数类型。这个类型参数将在使用时确定。
从JDK1.5之后，Java引入了“参数化类型(Parameterized type)”的概念，允许创建集合时再确定集合元素的类型，JDK1.5改写了集合框架中的全部接口和类。
```

# 为什么要用泛型而不用Object
```
安全性，获取元素时不再需要解决强转问题
```

# 泛型使用
```
1.ArrayList<Integer> list = new ArrayList<>(); //类型推断
2.Iterator<Integer> iterator = list.iterator();
3.Map<String,Integer> map = new HashMap<>();
  Set<Entry<String,Integer>> entry = map.entrySet();
  Iterator<Entry<String,Integer>> iterator = entry.iterator();
```

# 自定义泛型类
```
声明：interface List<T> 和 class GenTest<K,V> 其中T,K,V表示类型，可以使用任意字母表示
实例化：List<String> strList = new ArrayList<String>();其中T只能是类，不能是基本数据类型
编译：尽管编译时ArrayList<String>和ArrayList<Integer>是两种类型，但是运行时只有一个ArrayList被加载到JVM中
不指定情况：将被擦除，泛型对应的类型均按照Object处理
对象：不可以创建泛型类的对象
简化操作：JDK1.7之后 //类型推断
静态方法：不能使用类的泛型，因为需要导入时创建了实例？（但是泛型方法中能够使用泛型作为返回值和传入参数）
数组实例：不可以使用new E[]，但是可以E[] element = (E[])new Object[capacity];
继承：父类有泛型，子类可以选择保留或者指定泛型类型
    1.不保留：
    擦除：class Son1 extends Father
    具体类型：class Son2 extends Father<Integer,String>
    2.保留
    全部保留：class Son3<T1,T2> extends Father<T1,T2>
    部分保留：class Son4<T2> extends Father<T1,T2>
异常：不能在try-catch中定义泛型类
```

# 泛型方法
```
泛型方法的格式：
访问权限 <泛型> 返回类型 方法名(泛型标志 参数名称){}
```

# 泛型继承
```
B为A的一个子类，但是G为泛型声明的类或接口，但是G<A>与G<B>之间没有父子类关系
```

# 通配符?
```
List<?>是List<String>、List<Object>等各种泛型List的父类
读取List<?>对象中的元素时，很安全，因为所有类型都是Object的子类
写入时不行，因为我们不知道具体元素类型，不能向其中添加元素，但是可以添加null
注意：通配符不能用在泛型类，泛型方法的声明上，不能用在创建对象上
```

# 限制通配符
```
<? extends Number>:只允许泛型为Number以及Number子类的引用调用
<? super Number>:只允许泛型为Number以及Number父类的引用调用
<? extends Comparable>:只允许泛型为实现Comparable接口的实现类引用调用
```