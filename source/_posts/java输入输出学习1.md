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

* 输入端抽象（Reader，Console，InputStream）
* 输出端抽象（Writer，Console，OutputStream）
* 中间数据传输过程中的数据抽象（Bits，Byte，Array，Char，File，Line，Piped，print，Cache）
* 对数据的描述词汇（Buffered，Closeable，Expiring，Externalizable，Descriptor，Permission，Flushable，Filter，Interrupted，Invalid，Number，Serializable，Validation，Random，Access等）

以上便会组成基本的包下的类名的组成了。

而且以上是原子化的。对包下类名的描述，也就是说，其实真实的类名，是以上一些元素的组合形式，而不仅仅是单个元素便构成了每个类。

### Reader,Writer和InputStream,OutputStream的区别

从我的观察类看，其实两者实现的都是对输入输出流的操作，也就是大体上，其实实现的功能是有重叠部分的。

但是根据的是不同的角度来描述输入输出。

一个是根据终端的是一个读的终端或者是一个写的终端来决定的。也就是以终端存在来构思输入输出接口的类实现的。

一个是根据读入数据流或者是写入数据流来决定的。也就是以传输过程中的数据流为主要构思输入输出接口类的实现的。

### 元素组合

##### 组合而成的以Stream为概念的类

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

##### 组合而成的以Reader和Writer为概念的类

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

对于以输入输出流为概念输入输出数据，有比如：

* 缓冲输入输出流
* 流字节数组输入输出流
* 数据输入输出流（各种基础java数据类型）
* 文件输入输出流
* 过滤器输入输出流（未定义实现）
* 对象输入输出流
* 管道输入输出流
* 序列输入流
* 字符串缓冲输入流

等；

对于以输入输出终端Reader和Writer为概念输入输出数据，有比如：

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