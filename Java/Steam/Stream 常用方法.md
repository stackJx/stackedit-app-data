## Stream API

> java.util.Collection 下的接口加入了默认方法 stream 所有实现都可以通过 stream 方法来获取流

```
List<String> list = new ArrayList<>();
        list.stream();
        Set<String> set = new HashSet<>();
        set.stream();
        Vector vector = new Vector();
        vector.stream();

// map 等
        Map<String,Object> map = new HashMap<>();
        Stream<String> stream = map.keySet().stream(); // key
        Stream<Object> stream1 = map.values().stream(); // value
        Stream<Map.Entry<String, Object>> stream2 = map.entrySet().stream(); // entry

       // steam of 方法 转化数组为 steam 流
       Stream<String> a1 = Stream.of("a1", "a2", "a3");
        String[] arr1 = {"aa","bb","cc"};
        Stream<String> arr11 = Stream.of(arr1);
        Integer[] arr2 = {1,2,3,4};
        Stream<Integer> arr21 = Stream.of(arr2);
        arr21.forEach(System.out::println);
        // 注意：基本数据类型的数组是不行的
        int[] arr3 = {1,2,3,4};
        Stream.of(arr3).forEach(System.out::println);
```

### forEach

```
void forEach(Consumer<? super T> action);
public static void main(String[] args) {
        Stream.of("a1", "a2", "a3").forEach(System.out::println);;
    }
```

#### 该方法接受一个Consumer接口，会将每一个流元素交给函数处理

### count

```
// 统计元素个数
public static void main(String[] args) {
        long count = Stream.of("a1", "a2", "a3").count();
        System.out.println(count);
    }
```

### filter

```
// 过滤数据
Stream<T> filter(Predicate<? super T> predicate);

  public static void main(String[] args) {
         Stream.of("a1", "a2", "a3","bb","cc","aa","dd")
                 .filter((s)->s.contains("a"))
                 .forEach(System.out::println);

    }
```

### limit

```
// limit方法可以对流进行截取处理，支取前n个数据，
Stream<T> limit(long maxSize);

    public static void main(String[] args) {
         Stream.of("a1", "a2", "a3","bb","cc","aa","dd")
                 .limit(3)
                 .forEach(System.out::println);

    }
```

### skip

```
// 跳过前面的几个元素
Stream<T> skip(long n);
    public static void main(String[] args) {
         Stream.of("a1", "a2", "a3","bb","cc","aa","dd")
                 .skip(3)
                 .forEach(System.out::println);

    }
```

### map

```
// 将流中的元素映射到另一个流
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
    public static void main(String[] args) {
         Stream.of("1", "2", "3","4","5","6","7")
                 //.map(msg->Integer.parseInt(msg))
                 .map(Integer::parseInt)
                 .forEach(System.out::println);

    }
```

### sorted

```
// 将数据排序
Stream<T> sorted();

    public static void main(String[] args) {
         Stream.of("1", "3", "2","4","0","9","7")
                 //.map(msg->Integer.parseInt(msg))
                 .map(Integer::parseInt)
                 //.sorted() // 根据数据的自然顺序排序
                 .sorted((o1,o2)->o2-o1) // 根据比较强指定排序规则
                 .forEach(System.out::println);

    }
```

### distinct

```
// 数据去重
Stream<T> distinct();
    public static void main(String[] args) {
         Stream.of("1", "3", "3","4","0","1","7")
                 //.map(msg->Integer.parseInt(msg))
                 .map(Integer::parseInt)
                 //.sorted() // 根据数据的自然顺序排序
                 .sorted((o1,o2)->o2-o1) // 根据比较强指定排序规则
                 .distinct() // 去掉重复的记录
                 .forEach(System.out::println);
        System.out.println("--------");
        Stream.of(
                new Person("张三",18)
                ,new Person("李四",22)
                ,new Person("张三",18)
        ).distinct()
                .forEach(System.out::println);

    }
```

### match

```
boolean anyMatch(Predicate<? super T> predicate); // 元素是否有任意一个满足条件
boolean allMatch(Predicate<? super T> predicate); // 元素是否都满足条件
boolean noneMatch(Predicate<? super T> predicate); // 元素是否都不满足条件

    public static void main(String[] args) {
        boolean b = Stream.of("1", "3", "3", "4", "5", "1", "7")
                .map(Integer::parseInt)
                //.allMatch(s -> s > 0)
                //.anyMatch(s -> s >4)
                .noneMatch(s -> s > 4)
                ;
        System.out.println(b);
    }
```

### max和min

```
// 获取最大值和最小值，那么可以使用max和min方法
    public static void main(String[] args) {

        Optional<Integer> max = Stream.of("1", "3", "3", "4", "5", "1", "7")
                .map(Integer::parseInt)
                .max((o1,o2)->o1-o2);
        System.out.println(max.get());

        Optional<Integer> min = Stream.of("1", "3", "3", "4", "5", "1", "7")
                .map(Integer::parseInt)
                .min((o1,o2)->o1-o2);
        System.out.println(min.get());
    }
```

### reduce

```
// 将所有数据归纳得到一个数据，可以使用reduce方法
    public static void main(String[] args) {
        Integer sum = Stream.of(4, 5, 3, 9)
                // identity默认值
                // 第一次的时候会将默认值赋值给x
                // 之后每次会将 上一次的操作结果赋值给x y就是每次从数据中获取的元素
                .reduce(0, (x, y) -> {
                    System.out.println("x="+x+",y="+y);
                    return x + y;
                });
        System.out.println(sum);
        // 获取 最大值
        Integer max = Stream.of(4, 5, 3, 9)
                .reduce(0, (x, y) -> {
                    return x > y ? x : y;
                });
        System.out.println(max);
    }
```

### map和reduce的组合

```
public static void main(String[] args) {
        // 1.求出所有年龄的总和
        Integer sumAge = Stream.of(
                new Person("张三", 18)
                , new Person("李四", 22)
                , new Person("张三", 13)
                , new Person("王五", 15)
                , new Person("张三", 19)
        ).map(Person::getAge) // 实现数据类型的转换
                .reduce(0, Integer::sum);
        System.out.println(sumAge);

        // 2.求出所有年龄中的最大值
        Integer maxAge = Stream.of(
                new Person("张三", 18)
                , new Person("李四", 22)
                , new Person("张三", 13)
                , new Person("王五", 15)
                , new Person("张三", 19)
        ).map(Person::getAge) // 实现数据类型的转换，符合reduce对数据的要求
                .reduce(0, Math::max); // reduce实现数据的处理
        System.out.println(maxAge);
        // 3.统计 字符 a 出现的次数
        Integer count = Stream.of("a", "b", "c", "d", "a", "c", "a")
                .map(ch -> "a".equals(ch) ? 1 : 0)
                .reduce(0, Integer::sum);
        System.out.println(count);
    }
```

### mapToInt

```
// 将Stream中的Integer类型转换成int类型，可以使用mapToInt方法来实现
public static void main(String[] args) {
        // Integer占用的内存比int多很多，在Stream流操作中会自动装修和拆箱操作
        Integer arr[] = {1,2,3,5,6,8};
        Stream.of(arr)
                .filter(i->i>0)
                .forEach(System.out::println);
        System.out.println("---------");
        // 为了提高程序代码的效率，我们可以先将流中Integer数据转换为int数据，然后再操作
        IntStream intStream = Stream.of(arr)
                .mapToInt(Integer::intValue);
        intStream.filter(i->i>3)
                .forEach(System.out::println);

    }
```

### concat

```
// 有两个流，希望合并成为一个流，那么可以使用Stream接口的静态方法concat
    public static void main(String[] args) {
        Stream<String> stream1 = Stream.of("a","b","c");
        Stream<String> stream2 = Stream.of("x", "y", "z");
        // 通过concat方法将两个流合并为一个新的流
        Stream.concat(stream1,stream2).forEach(System.out::println);
    }
```

## 练习

```
public static void main(String[] args) {
    List<String> list1 = Arrays.asList("迪丽热巴", "宋远桥", "苏星河", "老子", "庄子", "孙子", "洪七 公");
    List<String> list2 = Arrays.asList("古力娜扎", "张无忌", "张三丰", "赵丽颖", "张二狗", "张天爱", "张三");
    // 1. 第一个队伍只保留姓名长度为3的成员
    {
        System.out.println("1. 第一个队伍只保留姓名长度为3的成员");
        List<String> collect = list1.stream().filter(s -> s.length() == 3).collect(Collectors.toList());
        collect.forEach(System.out::println);
    }

    // 2. 第一个队伍筛选之后只要前3个人
    {
        System.out.println("2. 第一个队伍筛选之后只要前3个人");
        List<String> collect = list1.stream().limit(3).collect(Collectors.toList());
        collect.forEach(System.out::println);
    }
    // 3. 第二个队伍只要姓张的成员
    {
        System.out.println("3. 第二个队伍只要姓张的成员");
        List<String> collect = list2.stream().filter(s -> s.startsWith("张")).collect(Collectors.toList());
        collect.forEach(System.out::println);
    }
    // 4. 第二个队伍筛选之后不要前两个人
    {
        System.out.println("4. 第二个队伍筛选之后不要前两个人");
        List<String> collect = list2.stream().skip(2).collect(Collectors.toList());
        collect.forEach(System.out::println);
    }
    // 5. 将两个队伍合并为一个队伍
    {
        System.out.println("5. 将两个队伍合并为一个队伍");
        Stream.concat(list1.stream(), list2.stream()).forEach(System.out::println);
    }
    // 6. 根据姓名创建Person对象
    {
        System.out.println("6. 根据姓名创建Person对象");
        List<Person> collect = Stream.concat(list1.stream(), list2.stream()).map(s -> new Person(s)).collect(Collectors.toList());
        collect.forEach(System.out::println);
    }
    // 7. 打印整个队伍的Person信息
    {
        System.out.println("7. 根据姓名创建Person对象");
        List<Person> collect = Stream.concat(list1.stream(), list2.stream()).map(s -> new Person(s)).collect(Collectors.toList());
        collect.forEach(System.out::println);
    }
}
```



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NDc3NTIyNzJdfQ==
-->