#  java8新特性简介

+ 速度更快

+ 代码更少（增加了新的语法lambda表达式）

- 强大的stream API
- 便于并行
- 最大化减少空指针异常optional

**其中最核心的为lambda表达式和Stream API**

# lambda表达式

## 1. 为什么使用lambda表达式

lambda表达式是一个匿名函数，我们可以把lambda表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。可以写出更简洁、更灵活的代码 。

## 2. lambda表达式

- 从匿名类到lambda的转换

```
//匿名内部类
Runnable r1 = new Runnable(){
	@Override
	public void run(){
		system.out.println("Hello World");
	}
}

//lambda 表达式
Runnable r1 = ()->System.out.println("Hello Lambda!");
```

##  3. lambda表达式语法

lambda表达式在java语言中引入了一个新的语法元素和操作符。这个操作符为“->”,该操作符被称为lambda操作符或箭头操作符。它将lambda分为两部分：

**左侧：**指定了lambda表达式需要的所有参数

**右侧：**指定了lambda体，即lambda表达式要执行的功能。

语法格式一 ： 无参，无返回值，Lambda 体只需一条语句

```
Runnable r1 = ()->System.out.println("Hello Lambda!");
```

语法格式二 ：Lambda 需要一个参数

```
Consumer<String> fun = (args) -> System.out.println(args);
```

语法格式三 ：Lambda 只 需 要一个参数时，参数的小括号可以省略

```
Consumer<String> fun = args -> System.out.println(args);
```

语法格式四 ：Lambda 需要两个参数，并且有返回值

```
BinaryOperator<Long> bo = (x,y) -> {	

​	System.out.println("实现函数接口方法！");

​	return x+y;

}
```

语法格式五 ： 当 Lambda 体只有 一条 语句时，return 与大括号 可以省略

```
BinaryOperator<Long> bo = (x,y) -> x+y;
```

**类型推断：**

上述 Lambda 表达式中的参数类型都是由编译器推断得出的。Lambda 表达式中无需指定类型，程序依然可
以编译，这是因为 javac 根据程序的上下文，在后台推断出了参数的类型。Lambda 表达式的类型依赖于上
下文环境，是由编译器推断出来的。这就是所谓的“类型推断”。

# 函数式接口

## 1. 什么是函数式接口

- 只包含一个抽象方法的接口，称为 函数式接口。

- 你可以通过 Lambda 表达式来创建该接口的对象。（若 Lambda表达式抛出一个受检异常，那么该异常需要在目标接口的抽象方法上进行声明）。

- 我们可以在任意函数式接口上使用  @ FunctionalInterface 注解，这样做可以检查它是否是一个函数式接口，同时 javadoc 也会包含一条声明，说明这个接口是一个函数式接口。

## 2. 自定义函数式接口

![1611581932878](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1611581932878.png)

函数式接口中使用泛型：

![1611581961099](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1611581961099.png)

## 3. 作为递 参数传递 Lambda 表达式

![1611582026374](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1611582026374.png)



![1611582052825](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1611582052825.png)



<font color='red'>作为参数传递 Lambda 将 表达式：为了将 Lambda 表达式作为参数传递，接收 Lambda 表达式的参数类型必须是与该 Lambda 表达式兼容的函数式接口的类型。</font>

## 4. Java 内置四大核心函数式接口

| 函数式接口                | 参数类型 | 返回类型 | 用途                                                         |
| ------------------------- | -------- | -------- | ------------------------------------------------------------ |
| Consumer<T> 消费型接口    | T        | void     | 对类型为T的对象应用操作，包含方法：void accept(T t)          |
| Supplier<T> 供给型接口    | 无       | T        | 返回类型为T的对象，包含方法：T get();                        |
| Function<T, R> 函数型接口 | T        | R        | 对类型为T的对象应用操作，并返回结果。结果是R类型的对象。包含方法：R apply(T t); |
| Predicate<T> 断定型接口   | T        | Boolean  | 确定类型为T的对象是否满足某约束，并返回boolean 值。包含方法boolean test(T t); |

# 方法引用与构造器引用

## 1. 方法引用

当要传递给Lambda体的操作，已经有实现的方法了，可以使用方法引用!(实现抽象方法的参数列表，必须与方法引用方法的参数列表保持一致！）
方法引用：使用操作符 “ ::” 将方法名和对象或类的名字分隔开来。
如下三种主要使用情况 ：

-  对象 :: 实例方法

-  类 :: 静态方法

-  类 :: 实例方法

	

`

```
@Test
    public void poiTest() throws IOException {
        List<String> strings = Arrays.asList("1", "2");
        List<String> strings1 = Arrays.asList("3", "4","4","5","5","6","7","7","7");
        ArrayList<List<String>> objects = new ArrayList<>();
        objects.add(strings1);

        //获得一个工作薄
        HSSFWorkbook  wb = new HSSFWorkbook();
        Sheet sheet = wb.createSheet();

        HSSFCellStyle cellStyle =  wb.createCellStyle();

        //添加常用样式
        cellStyle.setWrapText(true);//设置自动换行
        //垂直居中
        cellStyle.setVerticalAlignment(cellStyle.getVerticalAlignmentEnum().CENTER);
        //水平居中
        cellStyle.setAlignment(HorizontalAlignment.CENTER);
//        cellStyle.setBorderBottom(HSSFCellStyle.BORDER_THIN);//下边框
//        cellStyle.setBorderLeft(HSSFCellStyle.BORDER_THIN);//左边框
//        cellStyle.setBorderTop(HSSFCellStyle.BORDER_THIN);//上边框
//        cellStyle.setBorderRight(HSSFCellStyle.BORDER_THIN);//右边框

     //   cellStyle.setBorderBottom(BorderStyle.THICK);
//        cellStyle.setBottomBorderColor(IndexedColors.BLUE.getIndex());
       cellStyle.setBorderLeft(BorderStyle.MEDIUM);
//        cellStyle.setLeftBorderColor(IndexedColors.GREEN.getIndex());
//        cellStyle.setBorderRight(BorderStyle.DASH_DOT);
//        cellStyle.setRightBorderColor(IndexedColors.RED.getIndex());
//        cellStyle.setBorderTop(BorderStyle.MEDIUM);
//        cellStyle.setTopBorderColor(IndexedColors.ORANGE.getIndex());
        //设置单元格背景色
       // cellStyle.setFillForegroundColor(IndexedColors.BLUE1.getIndex());
       // cellStyle.setFillPattern(FillPatternType.SOLID_FOREGROUND);


        for (int i =0 ;i<strings.size();i++) {
            Row row = sheet.createRow(i);
            Cell cell = row.createCell(0);

            cell.setCellValue(strings.get(i));
            cell.setCellStyle(cellStyle);
        }
        int x = 0;
        int y = 0;
        for (int i =0;i< strings1.size();i++) {
            Row row = sheet.getRow(i);
            if (row == null) {
                row = sheet.createRow(i);
            }
            Cell cell = row.createCell(3);
            cell.setCellValue(strings1.get(i));

//            if (i>0 && strings1.get(i).equals(strings1.get(i-1))){
//                x=x+1;
//                //sheet.addMergedRegion(new CellRangeAddress(i-1,i,3,3));
//            } else {
//               Arrays.asList(i,)
//            }
            cell.setCellStyle(cellStyle);
        }


        FileOutputStream fileOutputStream = new FileOutputStream("D:\\test.xls");
        wb.write(fileOutputStream);
        wb.close();
    }
```

`