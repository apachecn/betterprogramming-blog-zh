# JDK 的实用程序类:处理 I/O

> 原文：<https://betterprogramming.pub/utility-classes-of-the-jdk-dealing-with-i-o-1a7cb7dcfbda>

## 60 多种帮助方法使文件操作更加简单

![](img/863e22b26dd09f22c6e56d52d05516f1.png)

Maksym Kaharlytskyi 在 [Unsplash](https://unsplash.com/s/photos/file-cabinet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Java NIO *(* “非阻塞 I/O”)是处理 I/O 操作的一个很棒的特性集，[由 Java 1.4 引入](https://www.jcp.org/en/jsr/detail?id=51)。在 Java 7 中进一步完善( [JSR 203](https://www.jcp.org/en/jsr/detail?id=203) )。这个版本还给我们带来了本文的两个实用程序类:`[java.nio.file.Files](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Files.html)`和`[java.nio.file.Paths](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Paths.html)`

至少假设 Java 8。所有列出的方法都省略了 static 关键字，泛型类型信息可能会被简化以减少视觉混乱并提高可读性。每个方法都链接到 Oracle 提供的 [Java Platform SE 8 文档](https://docs.oracle.com/javase/8/docs/)。

# 路径接口

许多 I/O 操作的核心是`[java.nio.file.Path](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Path.html)`接口。它表示目录和文件名元素的分层序列，可用于定位文件或目录，带有系统相关的文件路径分隔符。

主要是和`[java.io.File](https://docs.oracle.com/javase/7/docs/api/java/io/File.html)`互通。两种类型都提供转换方式:`[File#toPath()](https://docs.oracle.com/javase/7/docs/api/java/io/File.html#toPath())`和`[Path#toFile()](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Path.html#toFile())`。

# java.nio.file.Paths

`[java.nio.file.Paths](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Paths.html)`并不是一个拥有许多不同方法的“成熟的”实用程序类。它只是创建一个`[java.nio.file.Path](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Path.html)`实例的助手:

```
Path [**get**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-)(String first,
         String... more)Path [**get**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.net.URI-)(URI uri)
```

我们可以使用这个助手来代替连接目录和文件名的字符串。将使用适当的文件系统分隔符:

```
Paths.get("data", "logs", "error")
// => data/logs/error
```

# java.nio.file.Files

`[java.nio.file.Files](https://docs.oracle.com/javase/7/docs/api/java/nio/file/Files.html)`实用程序类是一个庞然大物。在 Java 8 中，它由 65 个静态方法组成。最新的 LTS Java 11 将这个数字提高到了 69。

如果合适，一些列出的方法会出现在多个部分中。

## java.nio.file.OpenOption

我们可以通过提供一个或多个`[java.nio.file.OpenOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html)`来影响文件的打开方式。有多个选项可用，在`[java.nio.file.StandardOpenOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html)`中定义:

*   `[APPEND](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#APPEND)`
*   `[CREATE](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#CREATE)`
*   `[CREATE_NEW](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#CREATE_NEW)` —如果已经存在，则失败
*   `[DELETE_ON_CLOSE](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#DELETE_ON_CLOSE)`
*   `[DSYNC](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#DSYNC)` —要求同步内容写入存储
*   `[READ](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#READ)`
*   `[SPARSE](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#SPARSE)`
*   `[SYNC](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#SYNC)` —要求将内容和元数据同步写入存储
*   `TRUNCATE_EXISTING`
*   `[WRITE](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardOpenOption.html#WRITE)`

## 创建文件和目录

可以很容易地创建不同种类的文件系统对象:

```
*// Create a single directory, no intermediaries*
Path [**createDirectory**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createDirectory-java.nio.file.Path-java.nio.file.attribute.FileAttribute...-)​(Path dir,
                     FileAttribute<?>... attrs)*// Create a directory, including all non-existent parents*
Path [**createDirectories**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createDirectories-java.nio.file.Path-java.nio.file.attribute.FileAttribute...-)​(Path dir,
                       FileAttribute<?>... attrs)*// Create an empty file (fails if exists)*
Path [**createFile**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createFile-java.nio.file.Path-java.nio.file.attribute.FileAttribute...-)​(Path path,
                FileAttribute<?>... attrs)*// Create a hard-link*
Path [**createLink**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createLink-java.nio.file.Path-java.nio.file.Path-)​(Path link,
                Path existing)*// Create a symbolic link*
Path [**createSymbolicLink**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createSymbolicLink-java.nio.file.Path-java.nio.file.Path-java.nio.file.attribute.FileAttribute...-)​(Path link,
                        Path target,
                        FileAttribute<?>... attrs)
```

还有一些处理临时对象的特殊方法。他们将在*默认的* `temporary-file`目录下创建一个文件或目录，用一个随机的名字。但是我们可以提供前缀/后缀以便更好地识别:

```
*// Create a file directly in the temporary-file directory*
Path [**createTempFile**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createTempFile-java.nio.file.Path-java.lang.String-java.lang.String-java.nio.file.attribute.FileAttribute...-)​(String prefix,
                    String suffix,
                    FileAttribute<?>... attrs)*// Create a directory directly in the temporary-file directory*
Path [**createTempDirectory**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createTempDirectory-java.lang.String-java.nio.file.attribute.FileAttribute...-)​(String prefix,
                         FileAttribute<?>... attrs)*// Create a directroy in provided directory*
*// but use the temporary naming logic*
Path [**createTempDirectory**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#createTempDirectory-java.nio.file.Path-java.lang.String-java.nio.file.attribute.FileAttribute...-)​(Path dir,
                         String prefix,
                         FileAttribute<?>... attrs)
```

我们也可以通过使用适当的`[java.nio.file.**OpenOption**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/OpenOption.html)`使用`[java.nio.channels.**SeekableByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/channels/SeekableByteChannel.html)`来创建一个文件:

```
*// Open or create a seekable byte channel*
SeekableByteChannel [**newByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newByteChannel-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                                   OpenOption... options)*// Open or create a seekable byte channel*
*// with initial file attributes for creation*
SeekableByteChannel [**newByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newByteChannel-java.nio.file.Path-java.util.Set-java.nio.file.attribute.FileAttribute...-)​(Path path,
                                   Set<OpenOption> options,
                                   FileAttribute<?>... attrs)
```

## 阅读内容

内容读取可分为两类:*基于字节的*和*基于字符的*。

这两个类别都充满了“一次性阅读”或“按需/懒惰”的方法。

**基于字节的**

```
*// Read all bytes, not recommended for large files*
byte[] [**readAllBytes**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#readAllBytes-java.nio.file.Path-)​(Path path)*// Open a file and create an unbuffered InputStream.*
*// Don't forget to close it!*
InputStream [**newInputStream**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newInputStream-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                           OpenOption... options)*// Open or create a seekable byte channel*
SeekableByteChannel [**newByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newByteChannel-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                                   OpenOption... options)*// Open or create a seekable byte channel*
*// with initial file attributes for creation*
SeekableByteChannel [**newByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newByteChannel-java.nio.file.Path-java.util.Set-java.nio.file.attribute.FileAttribute...-)​(Path path,
                                   Set<OpenOption> options,
                                   FileAttribute<?>... attrs)
```

**基于字符的**

```
*// Read all lines as UTF-8, not recommended for large files*
List<String> [**readAllLines**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#readAllLines-java.nio.file.Path-)​(Path path)*// Read all lines as a specific charset,*
*// not recommended for large files*
List<String> [**readAllLines**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#readAllLines-java.nio.file.Path-java.nio.charset.Charset-)​(Path path,
                          Charset cs)*// Read lines lazy as UTF-8, backed by a java.io.Reader*
Stream<String> [**lines**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#lines-java.nio.file.Path-)​(Path path)*// Read lines lazy as a specific charset, backed by a java.io.Reader*
Stream<String> [**lines**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#lines-java.nio.file.Path-java.nio.charset.Charset-)​(Path path,
                     Charset cs)*// Read lines buffered as UTF8*
BufferedReader [**newBufferedReader**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newBufferedReader-java.nio.file.Path-)​(Path path)*// Read lines buffered as a specific charset*
BufferedReader [**newBufferedReader**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newBufferedWriter-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                                 Charset cs)
```

## 写作

像阅读一样，写作内容可以分为两组:

**基于字节的**

```
*// Write bytes directly to a path.*
Path [**write**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#write-java.nio.file.Path-byte:A-java.nio.file.OpenOption...-)​(Path path,
           byte[] bytes,
           OpenOption... options)*// Opens or creates a file, returning an java.io.OutputStream*
OutputStream [**newOutputStream**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newOutputStream-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                             OpenOption... options)*// Opens or creates a seekable byte channel*
SeekableByteChannel [**newByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newByteChannel-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                                   OpenOption... options)*// Opens or creates a seekable byte channel*
*// with initial file attributes for creation*
SeekableByteChannel [**newByteChannel**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newByteChannel-java.nio.file.Path-java.util.Set-java.nio.file.attribute.FileAttribute...-)​(Path path,
                                   Set<OpenOption> options,
                                   FileAttribute<?>... attrs)
```

**基于字符的**

```
*// Writes text lines as UTF-8*
Path [**write**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#write-java.nio.file.Path-java.lang.Iterable-java.nio.file.OpenOption...-)​(Path path,
           Iterable<CharSequence> lines,
           OpenOption... options)*// Writes text lines as the specified charset*
Path [**write**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#write-java.nio.file.Path-java.lang.Iterable-java.nio.charset.Charset-java.nio.file.OpenOption...-)​(Path path,
           Iterable<CharSequence> lines,
           Charset cs,
           OpenOption... options)*// Opens or creates a file, returning an java.io.BufferedWriter,*
*// using UTF-8*
BufferedWriter [**newBufferedWriter**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newBufferedWriter-java.nio.file.Path-java.nio.file.OpenOption...-)​(Path path,
                                 OpenOption... options)*// Opens or creates a file, returning an java.io.BufferedWriter,*
*// using the specified charset*
BufferedWriter [**newBufferedWriter**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#newBufferedWriter-java.nio.file.Path-java.nio.charset.Charset-java.nio.file.OpenOption...-)​(Path path,
                                  Charset cs,
                                  OpenOption... options)
```

## 目录内容

有三种不同类型的目录内容迭代/遍历:

*   `[java.nio.file.**DirectoryStream**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/DirectoryStream.html)` —一个专门的`[java.lang.**Iterable**](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)`。
*   `[java.util.stream.**Stream**](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)` —使用更现代的[流 API](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184) 。
*   `[java.nio.file.**FileVisistor**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/FileVisitor.html)` —使用[访客模式](https://en.wikipedia.org/wiki/Visitor_pattern)。

每一个都有它的优点，取决于我们的需求。

当*走过`Path`上的*时，我们可以指定`[java.nio.file.**FileVisitOption**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/FileVisitOption.html)`。此时，只有`FOLLOW_LINKS`可用。如果检测到周期，则抛出`[FileSystemLoopException](https://docs.oracle.com/javase/8/docs/api/java/nio/file/FileSystemLoopException.html)`。

```
*// Lazily populated Steam, traversed depth-first.*
Stream<Path> [**walk**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#walk-java.nio.file.Path-java.nio.file.FileVisitOption...-)​(Path start,
                  FileVisitOption... options)*// Lazily populated Steam, traversed depth-first, up to maxDepth*
Stream<Path> [**walk**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#walk-java.nio.file.Path-int-java.nio.file.FileVisitOption...-)​(Path start,
                  int maxDepth,
                  FileVisitOption... options)*// Visitor-pattern*
Path [**walkFileTree**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#walkFileTree-java.nio.file.Path-java.nio.file.FileVisitor-)​(Path start,
                  FileVisitor<Path> visitor)*// Visitor-pattern, traversed depth-first, up to maxDepth*
Path [**walkFileTree**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#walkFileTree-java.nio.file.Path-java.util.Set-int-java.nio.file.FileVisitor-)​(Path start,
                  Set<FileVisitOption> options,
                  int maxDepth,
                  FileVisitor<Path> visitor)
```

还有两种方法可以列出/查找目录内容:

```
*// Lazily populated Stream of directory content*
Stream<Path> [**list**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#list-java.nio.file.Path-)​(Path dir)*// Lazily populated filtered Stream*
Stream<Path> [**find**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#find-java.nio.file.Path-int-java.util.function.BiPredicate-java.nio.file.FileVisitOption...-)​(Path start,
                  int maxDepth,
                  BiPredicate<Path,​BasicFileAttributes> matcher,
                  FileVisitOption... options)
```

## 检测文件系统对象属性

在遍历目录内容时，我们还想知道我们在处理什么。

有些方法接受`[java.nio.file.LinkOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/LinkOption.html)`，它决定了如何处理符号链接。只有选项`[NOFOLLOW_LINKS](https://docs.oracle.com/javase/8/docs/api/java/nio/file/LinkOption.html#NOFOLLOW_LINKS)`可用。

```
boolean [**isDirectory**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isDirectory-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                    LinkOption... options)boolean [**isExecutable**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isExecutable-java.nio.file.Path-)​(Path path)boolean [**isHidden**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isHidden-java.nio.file.Path-)​(Path path)boolean [**isReadable**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isReadable-java.nio.file.Path-)​(Path path)boolean [**isRegularFile**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isRegularFile-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                      LinkOption... options)*// If the arguments are equals == true,*
*// the files themselves are not checked*
boolean [**isSameFile**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isSameFile-java.nio.file.Path-java.nio.file.Path-)​(Path path,
                   Path path2)boolean [**isSymbolicLink**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isSymbolicLink-java.nio.file.Path-)​(Path path)boolean [**isWritable**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#isWritable-java.nio.file.Path-)​(Path path)long [**size**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#size-java.nio.file.Path-)​(Path path)boolean [**exists**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#exists-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
               LinkOption... options)boolean [**notExists**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#notExists-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                  LinkOption... options)
```

甚至可以检测到 [MIME 内容类型](http://www.ietf.org/rfc/rfc2045.txt):

```
String [**probeContentType**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#probeContentType-java.nio.file.Path-)**​**(Path path)
```

## 属性

*通常的*文件属性可以读写:

```
FileTime [**getLastModifiedTime**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#getLastModifiedTime-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                             LinkOption... options)Path [**setLastModifiedTime**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#setLastModifiedTime-java.nio.file.Path-java.nio.file.attribute.FileTime-)​(Path path,
                         FileTime time)UserPrincipal [**getOwner**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#getOwner-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                       LinkOption... options)Path [**setOwner**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#setOwner-java.nio.file.Path-java.nio.file.attribute.UserPrincipal-)​(Path path,
              UserPrincipal owner)Set<PosixFilePermission> [**getPosixFilePermissions**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#getPosixFilePermissions-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                                                 LinkOption... options)Path [**setPosixFilePermissions**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#setPosixFilePermissions-java.nio.file.Path-java.util.Set-)​(Path path,
                             Set<PosixFilePermission> perms)
```

但是我们也可以直接访问任何文件属性:

```
V [**getFileAttributeView**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#getFileAttributeView-java.nio.file.Path-java.lang.Class-java.nio.file.LinkOption...-)​(Path path,
                       Class<V> type, LinkOption... options)Object [**getAttribute**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#getAttribute-java.nio.file.Path-java.lang.String-java.nio.file.LinkOption...-)​(Path path,
                    String attribute,
                    LinkOption... options)Path [**setAttribute**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#setAttribute-java.nio.file.Path-java.lang.String-java.lang.Object-java.nio.file.LinkOption...-)​(Path path,
                  String attribute,
                  Object value,
                  LinkOption... options)A [**readAttributes**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#readAttributes-java.nio.file.Path-java.lang.Class-java.nio.file.LinkOption...-)​(Path path,
                 Class<A> type,
                 LinkOption... options)Map<String,​Object> [**readAttributes**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#readAttributes-java.nio.file.Path-java.lang.String-java.nio.file.LinkOption...-)​(Path path,
                                  String attributes,
                                  LinkOption... options)
```

## 文件操作

最后但同样重要的是，例行的文件操作。

除了`[java.nio.file.LinkOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/LinkOption.html)`之外，有些方法使用`[java.nio.file.CopyOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/CopyOption.html)`。可用选项在`[java.nio.file.StandardCopyOption](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardCopyOption.html)`中定义:

*   `[ATOMIC_MOVE](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardCopyOption.html#ATOMIC_MOVE)`
*   `[COPY_ATTRIBUTES](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardCopyOption.html#COPY_ATTRIBUTES)`
*   `[REPLACE_EXISTING](https://docs.oracle.com/javase/8/docs/api/java/nio/file/StandardCopyOption.html#REPLACE_EXISTING)`

```
*// Copy a file to a target file (not directory!)*
Path [**copy**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#copy-java.nio.file.Path-java.nio.file.Path-java.nio.file.CopyOption...-)​(Path source,
          Path target,
          CopyOption... options)*// Copy all bytes from InputStream to a Path,
// returns bytes read or written*
long [**copy**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#copy-java.io.InputStream-java.nio.file.Path-java.nio.file.CopyOption...-)​(InputStream in,
          Path target,
          CopyOption... options)*// Copy all bytes from a file to an OutputStream,*
*// returns bytes read or written*
long [**copy**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#copy-java.nio.file.Path-java.io.OutputStream-)​(Path source,
          OutputStream out)*// Delete a file or empty directory.*
*// Symbolic links aren't followed.*
*// Throw NoSuchFileException if file doesn't exist.*
void [**delete**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#delete-java.nio.file.Path-)​(Path path)*// Delete a file or empty directory.*
*// Symbolic links aren't followed.*
*// Doesn't throw an exception if file not exists*
boolean [**deleteIfExists**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#deleteIfExists-java.nio.file.Path-)​(Path path)*// Test whether a file or directory exists*
boolean [**exists**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#exists-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
               LinkOption... options)*// Test whether a file or directory doesn't exist*
boolean [**notExists**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#notExists-java.nio.file.Path-java.nio.file.LinkOption...-)​(Path path,
                  LinkOption... options)*// Move/rename a file or directory.*
*// CopyOption 'COPY_ATTRIBUTES' isn't supported*
Path [**move**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#move-java.nio.file.Path-java.nio.file.Path-java.nio.file.CopyOption...-)​(Path source,
          Path target,
          CopyOption... options)*// Read target of symbolic link.*
*// Throws NotLinkExpcetion if the target isn't a symbolic link*
Path [**readSymbolicLink**](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#readSymbolicLink-java.nio.file.Path-)​(Path link)
```

## Java 11 新增功能

Java 11 的发布为我们带来了四种读写字符串的新方法:

```
// Read file to String as UTF-8
String [**readString**](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#readString(java.nio.file.Path))​(Path path)// Read file to String as specified charset
String [**readString**](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#readString(java.nio.file.Path,java.nio.charset.Charset))​(Path path,
                  Charset cs)// Write String to file as UTF-8
Path [**writeString**](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#writeString(java.nio.file.Path,java.lang.CharSequence,java.nio.file.OpenOption...))​(Path path,
                 CharSequence csq,
                 OpenOption... options)// Write String to file as specified charset
Path [**writeString**](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#writeString(java.nio.file.Path,java.lang.CharSequence,java.nio.charset.Charset,java.nio.file.OpenOption...))​(Path path,
                 CharSequence csq,
                 Charset cs,
                 OpenOption... options)
```

# 结论

另一个优秀的实用类，它简化了许多复杂的 I/O 操作，不需要第三方库。

但是要注意，几乎所有的方法都会抛出各种异常！永远不要盲目信任 I/O 操作，并且总是捕捉(并处理)它们的异常。

# 资源

*   `[java.nio.file.Paths](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html)`
*   `[java.nio.file.Files](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html)`
*   [Java 非阻塞 I/O](https://en.wikipedia.org/wiki/Non-blocking_I/O_(Java)) (维基百科)
*   [Java I/O、NIO 和 NIO2](https://docs.oracle.com/javase/8/docs/technotes/guides/io/index.html) (Oracle)
*   [Java nio 2 文件 API 介绍](https://www.baeldung.com/java-nio-2-file-api) (Baeldung)
*   [JDK 集合和数组的实用类](https://medium.com/better-programming/utility-classes-of-the-jdk-collections-and-arrays-cc07dbcb8586)