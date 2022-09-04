# 实现Comparable接口
```
class name implements Comparable // 创建类实现Comparable接口
重写compareTo(obj)方法
    如果对象this大于形参obj，则返回正整数
    如果对象this小于形参obj，返回负整数
    如果相等，返回0
```

# 创建comparable对象，重写compare(obj1,obj2)方法
```
new Comparator(){
    @Override
    public int compare(Object o1, Object o2){};
}
```