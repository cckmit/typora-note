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

