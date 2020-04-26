title: python的文件操作
date: 2015-11-16 13:50:02
tags:
 - file_python
categories: [python]

---

# 文件
之前都是在控制台标准输入输出用的input和raw_input,今天看一下实际的数据文件的读写。
可以使用对象file操作文件：
* 打开文件open
* 读取read()和写入write()文件
* 关闭文件close


<!--more -->

# 操作步骤

## open函数
`file object = open(file_name [, access_mode][, buffering])`
access_mode的选项:
模式 描述
r 只读,文件的开头,默认模式
rb 二进制格式只读，文件的开头，默认模式
r+ 读取和写入文件，文件的开头
w 存在,覆盖写入；不存在，创建写入
w+ 读取和写入文件。存在,覆盖写入；不存在，创建写入
a 打开追加文件，文件的结尾；不存在，创建追加
a+ 追加和读取文件，文件的结尾；不存在，创建追加

file 对象属性
file.closed	 如果文件被关闭则返回true，否则返回false。
file.mode	 返回该文件被打开的访问模式。
file.name	 返回文件的名称。
file.softspace	 如果空格明确要求具有打印，返回false，否则返回true。

## 读取和写入
使用read()和write()方法来读取和写入文件

read
`fileObject.read([count]);`
这里，传递的参数是从打开的文件中读出的字节数。此方法从该文件的开头读取，如果计数丢失，那么它会尝试尽可能多地读取，直到文件的末尾。

write
write()方法不要将换行字符('\n')添加到字符串的结尾：
`fileObject.write(string);`
这里，传递的参数是要写入到打开的文件的内容。

## close
一个文件对象的close()方法刷新未写入的信息，并关闭该文件的对象，在这之后没有数据内容可以执行写入。

Python自动关闭，当文件的引用对象被重新分配给另外一个文件。它是使用close()方法来关闭文件是一个很好的做法。
`fileObject.close();`

# 其他文件函数

## 文件位置tell,seek
tell()方法告诉该文件中的当前位置;换句话说，下一个读取或写入将发生在从该文件的开头的字节数。
seek(offset[, from]) 方法会更改当前的文件位置。偏移参数指示要移动的字节数。从该参数指定字节将被移至参考点。
如果from被设置为0，这意味着使用该文件的开始处作为基准位置，设置为1则是使用当前位置作为基准位置，如果它被设置为2，则该文件的末尾将被作为基准位置。


## 重命名和删除文件
需要借助于python的os模块
rename
rename()方法有两个参数，当前文件名和新文件名。
语法:
`os.rename(current_file_name, new_file_name)`

remove
可以使用remove()方法通过提供参数作为文件名称作为要删除的文件。
语法:
`os.remove(file_name)`

## Python中的目录
可以使用os模块的mkdir()方法在当前目录下创建目录。需要提供参数，这个方法包含的目录要创建的名称。
mkdir
语法:
`os.mkdir("newdir")`

chdir
可以使用chdir()方法来改变当前目录。chdir()方法接受一个参数，那就是要成为当前目录的目录的名称。
语法:
`os.chdir("newdir")`

getcwd
getcwd()方法显示当前的工作目录。
例子:
`os.getcwd()`

rmdir
rmdir()命令方法删除目录，它是通过方法的参数。
在删除一个目录，它的所有内容应该被删除。
`os.rmdir('dirname')`

# 实例

## 文件写入
```python
import time
import random
 
#打开模式列表：
#w      以写方式打开，
#a      以追加模式打开 (从 EOF 开始, 必要时创建新文件)
#r+     以读写模式打开
#w+     以读写模式打开 (参见 w )
#a+     以读写模式打开 (参见 a )
#rb     以二进制读模式打开
#wb     以二进制写模式打开 (参见 w )
#ab     以二进制追加模式打开 (参见 a )
#rb+    以二进制读写模式打开 (参见 r+ )
#wb+    以二进制读写模式打开 (参见 w+ )
#ab+    以二进制读写模式打开 (参见 a+ )
f = open('tpm.txt', 'a+')
 
for i in range(10) :
    f.write(time.strftime('%Y-%m-%d %H:%M:%S'))
    f.write(' ' + str(random.randint(0, i)) + '\n')
 
f.close()
```
## 文件读取
```python
f = open('tpm.txt')
# read方式读取
s = f.read()
print(s, '\n\n\n')
print(f.tell())
#上面读取完后指针移动到最后，通过seek将文件指针移动到文件头
f.seek(0)
#使用readline每次读取一行
while(True):
    line = f.readline()
    print(line)
    if(len(line) == 0):
        break
 
f.close()

```
## 文件目录操作（OS包）
```python
#os模块，处理文件和目录的一系列函数
import os
 
#打印当前目录下的所有文件 非递归
print(os.listdir(os.getcwd()))
 
#切换目录为当前目录
os.chdir('.')
 
#判断目标是否存在，不存在则创建
if(os.path.exists('./osdirs') == False):
    os.mkdir("./osdirs")
 
#重命名文件或目录名
if(os.path.exists("./os") == False) :
    os.rename("./osdirs", "./os")
 
#rmdir删除目录，需要先清空文件中的子目录或文件夹
#removedirs可多层删除目录（需要目录中无文件） makedirs可多层创建目录
if(os.path.isdir("./os")) :
    os.rmdir("./os")
 
#删除文件
if(os.path.exists('./tpm.txt')):
    os.remove('./tpm.txt')

```
os模块中常用方法和属性：
>os.linesep 文件中分割行的字符串
os.sep文件路径名的分隔符
os.curdir当前工作目录的字符串名称
os.pardir父目录字符串名称
方法
os.remove()删除文件
os.rename()重命名文件
os.walk()生成目录树下的所有文件名
os.chdir()改变目录
os.mkdir/makedirs创建目录/多层目录
os.rmdir/removedirs删除目录/多层目录
listdir()列出指定目录的文件
getcwd()取得当前工作目录(current work directory)
chmod()改变目录权限
os.path.basename()去掉目录路径，返回文件名
os.path.dirname()去掉文件名，返回目录路径
os.path.join()将分离的各部分组合成一个路径名
os.path.split()返回（dirname(),basename())元组
os.path.splitext()(返回filename,extension)元组
os.path.getatime\ctime\mtime分别返回最近访问、创建、修改时间
os.path.getsize()返回文件大小
os.path.exists()是否存在
os.path.isabs()是否为绝对路径
os.path.isdir()是否为目录
os.path.isfile()是否为文件

## 文件目录操作(shutil包)
```python
import os
import shutil
 
#复制文件，相当于CP命令
shutil.copy('start2.txt', 'start3')
 
#移动文件或目录，相当于MV命令
shutil.move('start3', 'start4')
 
if(os.path.exists('./a/b/c') == False) :
    os.makedirs('./a/b/c')
#删除目录
shutil.rmtree('./a')
 
if(os.path.exists('./a/b/c/d') == False) :
    os.makedirs('./a/b/c/d')
     
#复制目录
if(os.path.exists('b') == False) :
    shutil.copytree('a', 'b')

```
shutil中常用方法
>copyfile( src, dst)	从源src复制到dst中去。当然前提是目标地址是具备可写权限。抛出的异常信息为IOException. 如果当前的dst已存在的话就会被覆盖掉
copymode( src, dst)	 	只是会复制其权限其他的东西是不会被复制的
copystat( src, dst)	 	复制权限、最后访问时间、最后修改时间
copy( src, dst) 		复制一个文件到一个文件或一个目录
copy2( src, dst) 		在copy上的基础上再复制文件最后访问时间与修改时间也复制过来了，类似于cp –p的东西
copy2( src, dst) 		如果两个位置的文件系统是一样的话相当于是rename操作，只是改名；如果是不在相同的文件系统的话就是做move操作
copytree(olddir,newdir,True/Flase)	 把olddir拷贝一份newdir，如果第3个参数是True，则复制目录时将保持文件夹下的符号连接，如果第3个参数是False，则将在复制的目录下生成物理副本来替代符号连接
rmtree(path[, ignore_errors[, onerror]])	删除目录
move( src, dst) 		移动文件或目录

## 遍历文件夹
```python
import os
#遍历当前路劲下的所有目录和文件夹
#返回元组包含三个参数：1.父目录 2.所有文件夹名字（不含路径） 3.所有文件名字
for root, dirs, files in os.walk(os.getcwd()):
    #输出文件夹信息
    for dir in  dirs:
        print(os.path.join(root,dir))
    #输出文件信息
    for file in files:
        print(os.path.join(root,file))

```
