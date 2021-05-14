# JAVA笔记

## File与IO流

```java
	/**	
     * File_demo
     */
    public static void demo1() throws IOException {
        File file  = new File("E:/test/1.jpg");
        System.out.println(file.exists());
        //是否存在
        System.out.println(file.isFile());
        //创建文件
        System.out.println(file.createNewFile());
        //获取绝对路径
        System.out.println(file.getAbsolutePath());
        //获取相对路径
        System.out.println(file.getPath());
        //获取文件名
        System.out.println(file.getName());
    }

    /**
     * FileInputStream_char_demo
     */
    public static void demo2(){
        FileInputStream inputStream = null;
        try {
           inputStream =  new FileInputStream("E:/test/abc.txt");
           //读取第一个字符的ASCII码 y=121
            System.out.println(inputStream.read());
            int temp = 0;
            //读取全部字符的ASCII码
            while ((temp = inputStream.read()) != -1){
                System.out.println(temp);
            }
            //读取全部字符
            inputStream =  new FileInputStream("E:/test/abc.txt");
            temp = 0;
            while ((temp = inputStream.read()) != -1){
                System.out.print((char) temp);
            }
            System.out.println();
        }catch (Exception e){
            System.out.println("系统找不到指定的文件");
            e.printStackTrace();
        }finally {
            try {
                inputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * Reader
     * 不推荐使用BufferedInputStream处理中文字符，容易报错，建议使用Reader或BufferReader
     * 基本字节流一次读取一行
     * 23 ms
     */
    public static void demo3(){
        FileInputStream inputStream = null;
        try {
            inputStream =  new FileInputStream("E:/test/abc.txt");
            int temp = 0;
            //若文本带非英文字符，设置编码格式gbk或者utf-8
            InputStreamReader reader = new InputStreamReader(inputStream,"utf-8");
            BufferedReader bf = new BufferedReader(reader);
            long a  = System.currentTimeMillis();
            //默认读取内容
            String string = null;
            while((string = bf.readLine()) != null){
                System.out.println(string);
            }
            long b  = System.currentTimeMillis();
            System.err.println("共花费："+(b-a)+"毫秒");
        }catch (Exception e){
            System.out.println("系统找不到指定的文件");
            e.printStackTrace();
        }finally {
            try {
                inputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * new byte[1024]处理流
     * 高效字节流一次读取一个字节数组
     *  9ms
     */
    public static void demo4() throws Exception{
        FileInputStream inputStream =  new FileInputStream("E:/test/abc.txt");
            int temp = 0;
            //加入缓冲区
            byte[] bytes = new byte[1024];
            // 用来接收读取的字节数组
            StringBuilder builder = new StringBuilder();
            //把字节转换中文字符
             long a  = System.currentTimeMillis();
            while((temp = inputStream.read(bytes)) != -1){
                builder.append(new String(bytes,0,temp));
            }
            System.out.println(builder.toString());
            long b  = System.currentTimeMillis();
            System.err.println("共花费："+(b-a)+"毫秒");
            inputStream.close();
    }

    /**
     * Write  不使用缓冲读写图片
     * @throws Exception
     * 基本字节流一次读写一个字节
     *  839ms
     */
    public static void demo5() throws  Exception{
        File file = new File("E:/test/1.jpg");
        File file1 = new File("E:/test/2.jpg");
        FileInputStream fileInputStream = new FileInputStream(file);
        FileOutputStream fileOutputStream = new FileOutputStream(file1);
        int temp = 0;
        long a  = System.currentTimeMillis();
        while ((temp = fileInputStream.read()) != -1){
            fileOutputStream.write(temp);
        }
        long b  = System.currentTimeMillis();
        System.err.println("共花费："+(b-a)+"毫秒");
        //关闭流的顺序：先打开的后关闭，后打开的先关闭
        fileOutputStream.close();
        fileInputStream.close();
    }

    /**
     * Write  使用缓冲流读写图片  byte[1024]
     * @throws Exception
     * 基本字节流一次读写一个字节数组
     * 1ms
     */
    public static void demo6() throws  Exception{
        File file = new File("E:/test/1.jpg");
        File file1 = new File("E:/test/2.jpg");
        FileInputStream fileInputStream = new FileInputStream(file);
        FileOutputStream fileOutputStream = new FileOutputStream(file1);
        byte[] bytes = new byte[1024];
        int temp = 0;
        long a  = System.currentTimeMillis();
        while ((temp = fileInputStream.read(bytes)) != -1){
            fileOutputStream.write(bytes,0,temp);
            fileOutputStream.flush();
        }
        long b  = System.currentTimeMillis();
        System.err.println("共花费："+(b-a)+"毫秒");
        //关闭流的顺序：先打开的后关闭，后打开的先关闭
        fileOutputStream.close();
        fileInputStream.close();
    }

    /**
     * Write  使用缓冲流读写图片 BufferedOutputStream
     * @throws Exception
     * 高效字节流一次读写一个字节
     * 480ms
     */
    public static void demo7() throws  Exception{
        File file = new File("E:/test/1.jpg");
        File file1 = new File("E:/test/2.jpg");
        FileInputStream fileInputStream = new FileInputStream(file);
        FileOutputStream fileOutputStream = new FileOutputStream(file1);
        BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
        int temp = 0;
        long a  = System.currentTimeMillis();
        while ((temp = bufferedInputStream.read()) != -1){
            bufferedOutputStream.write(temp);
            bufferedOutputStream.flush();
        }
        long b  = System.currentTimeMillis();
        System.err.println("共花费："+(b-a)+"毫秒");
        //关闭流的顺序：先打开的后关闭，后打开的先关闭
        bufferedInputStream.close();
        bufferedOutputStream.close();
    }

    /**
     * Write  使用缓冲流读写图片 BufferedOutputStream+byte[1024]
     * @throws Exception
     * 高效字节流一次读写一个字节数组
     * 1 ms
     */
    public static void demo8() throws  Exception{
        File file = new File("E:/test/1.jpg");
        File file1 = new File("E:/test/2.jpg");
        FileInputStream fileInputStream = new FileInputStream(file);
        FileOutputStream fileOutputStream = new FileOutputStream(file1);
        BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
        byte[] bytes = new byte[1024];
        int temp = 0;
        long a  = System.currentTimeMillis();
        while ((temp = bufferedInputStream.read(bytes)) != -1){
            bufferedOutputStream.write(bytes,0,temp);
            bufferedOutputStream.flush();
        }
        long b  = System.currentTimeMillis();
        System.err.println("共花费："+(b-a)+"毫秒");
        //关闭流的顺序：先打开的后关闭，后打开的先关闭
        bufferedInputStream.close();
        bufferedOutputStream.close();
    }

```



## 继承、Super 和 This 的区别

继承：

- 子类继承父类，子类拥有父类的所有方法和属性（除了被private修饰的）

- 子类可根据自己需要重写父类的方法（注解@override）；

- 当子类被创建时（new），优先调用父类的无参构造方法

- 如父类只有有参构造而无无参构造，子类创建构造函数必须定义一个有参的super，否则编译报错

  ![1620959325493](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1620959325493.png)

Super：

- 表示引用父类，只能在继承关系上使用
- 必须放在子类构造方法的第一行，否则编译报错
- 子类不管是有参构造还是无参构造默认都会生成一个super()方法，若父类没有无参构造方法则编译报错；若父类没有无参构造方法但有有参构造方法，子类可在第一行定义父类的有参构造方法，如super（构造参数）
- super.属性：引用父类的属性 
- super.方法：引用父类的方法

this：

- 表示引用当前类，任意时刻都可使用
- 使用this()时，必须放在有参构造的第一行，否则编译报错
- this.属性：引用当前类的属性 
- this.方法：引用当前类的方法

## 多态

多态注意事项:

1.多态属于方法的多态，属性没有多态

2.父类和子类，有联系，类型转换异常 !classcastException !

3.存在条件:继承关系，方法需要重写，父类引用指问了类对象。Fother f1 = new Son();

被以下关键字修饰的方法都不能被重写

- static方法，属于类的方法,不属于实例
- final 常量;
- private方法:

## Abstract 抽象类

```java
//abstract抽象类:类extends :单继承，(接口可以多继承>)
//约束~有人帮我们实现~
//abstract ，抽象方法，只有方法名字，没有方法的实现! 
//1.不能new这个抽象类,只能靠子类去实现它;约束!
//2.抽象类中可以写普通的方法~
//3.抽象方法必须在抽象类中~
//抽象的抽象:约束~
public abstract class Person{
    public abstract void doSomething();
}

 //继承抽象类必须实现抽象类中所有的抽象方法，否则编译报错
public class Son extends Person{
    //实现方法
    public void doSomething(){……};
}
```

