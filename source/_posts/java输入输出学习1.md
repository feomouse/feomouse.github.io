---
title: java输入输出学习1
categories: java
---

### 前言

输入输出，就是将外设，内存，和输入输出数据进行抽象表示与管理等的操作。

### java.io的词语

* Bits
* Buffered
* Input
* Output
* Stream
* Reader
* Writer
* Byte
* Array
* Char
* Closeable
* Console
* Data
* File
* System
* Cache
* Expiring
* Externalizable
* Descriptor
* Permission
* Filter
* Flushable
* IO
* Interrupted
* Invalid
* Line
* Number
* Serializable
* Validation
* Class
* Constants
* Field
* Piped
* print
* Pushback
* Random
* Access
* Tokenizer

以上是我从java.io包下的类的类名给提取出来的列表。

那其实，从本意上来讲，输入输出无非就是个：

* 输入端抽象（Reader，Console）
* 输出端抽象（Writer，Console）
* 中间数据传输过程中的数据抽象（Bits，Stream，Byte，Array，Char，File，Line，Piped，print，Cache，Input，Output）
* 对数据的描述词汇（Buffered，Closeable，Expiring，Externalizable，Descriptor，Permission，Flushable，Filter，Interrupted，Invalid，Number，Serializable，Validation，Random，Access等）

以上便会组成基本的包下的类名的组成了。

而且以上是原子化的。对包下类名的描述，也就是说，其实真实的类名，是以上一些元素的组合形式，而不仅仅是单个元素便构成了每个类。

##### 原子数据描述接口

* Closeable
* Flushable
* Serializable

##### 原子数据抽象

* Bits
* File

##### 数据终端

* Reader
* Writer
* Console

### 元素组合

##### 组合而成的数据抽象

* BufferedInputStream
* BufferedOutputStream
* ByteArrayInputStream
* ByteArrayOutputStream
* DataInputStream
* DataOutputStream
* ExpiringCache
* FileInputStream
* FileOutputStream
* FilterInputStream
* FilterOutputStream
* InputStream
* LineNumberInputStream
* ObjectInputStream
* ObjectOutputStream
* ObjectStream
* OutputStream
* PipedInputStream
* PipedOutputStream
* PrintStream
* PushbackInputStream
* RandomAccessFile
* SequenceInputStream
* StringBufferInputStream
* DataInput
* DataOutput
* ObjectInput
* ObjectOutput

##### 组合而成的数据终端

* BufferedReader
* BufferedWriter
* CharArrayReader
* CharArrayWriter
* FileReader
* FileWriter
* FilterReader
* FilterWriter
* InputStreamReader
* LineNumberReader
* OutputStreamWriter
* PipedReader
* PipedWriter
* PrintWriter
* PushbackReader
* StringReader
* StringWriter

### 总结

通过观察以上的类名，我们可以总结出：

对于输入输出数据，既可以抽象成各种形式的流，比如：

* 缓冲输入输出
* 流字节数组输入输出流
* 数据输入输出流
* 文件输入输出流
* 过滤器输入输出流
* 对象流
* 对象输入输出流
* 管道输入输出流
* 序列输入流
* 字符串缓冲输入流

等；

又可以抽象成：

* 文件
* 随机访问文件

等。

对于输入输出终端，可以抽象成各种Reader和Writer，比如

* 缓冲
* 字符数组
* 文件
* 过滤器
* 输入输出流
* 行
* 管道
* 打印
* 字符串

等。