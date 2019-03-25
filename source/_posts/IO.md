---
title: IO
tags: java
date: 2019-01-02 18:00:00
---


> 参考：http://ifeve.com/java-io/
### 文件

- 读文件
```
//字节
FileInputStream fileInputStream = new FileInputStream(new File("text.txt"));
byte[] bytes = new byte[fileInputStream.available()];
fileInputStream.read(bytes);
System.out.println(new String(bytes));

//字符
FileReader fileReader = new FileReader(new File("text.txt"));
int b;
while ((b = fileReader.read()) != -1) {
    System.out.println((char) b);
}


//字节转字符
InputStreamReader inputStreamReader =new InputStreamReader(fileInputStream);
```

- 写文件
```
// 第二个参数为（boolean append）
FileOutputStream fileOutputStream = new FileOutputStream(new File("test_FileOutputStream.txt"),false);
FileOutputStream fileOutputStream = new FileOutputStream(new File("test_FileOutputStream.txt"),true);
byte[] bytes = new String("你好").getBytes();
fileOutputStream.write(bytes);
```
- 随机读写
```
# 相当于追加文件
RandomAccessFile randomAccessFile = new RandomAccessFile(new File("text.txt"),"rw");
randomAccessFile.seek(randomAccessFile.length());
randomAccessFile.write("000".getBytes());
```

### 管道

可以作为不同线程之前的通信方式

```
public class PipedTest {


    public static void main(String[] args) throws IOException {
        final PipedInputStream pipedInputStream = new PipedInputStream();
        final PipedOutputStream pipedOutputStream = new PipedOutputStream();

        pipedInputStream.connect(pipedOutputStream);

        Thread thread1 = new Thread(() -> {
            try {
                pipedOutputStream.write("hello".getBytes());
                pipedOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        });

        Thread thread2 = new Thread(() -> {
            try {
                byte[] bytes = new byte[pipedInputStream.available()];
                pipedInputStream.read(bytes);
                System.out.println(new String(bytes));
                pipedInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        });

        thread1.start();
        thread2.start();
    }
}
```