title: optparser
date: 2015-11-13 14:50:02
tags:
 - CGI
categories: [python]

---
Python 有两个内建的模块用于处理命令行参数：
一个是 getopt，getopt只能简单处理 命令行参数。
另一个是 optparse，是一个能够让程式设计人员轻松设计出简单明了、易于使用、符合标准的Unix命令列程式的Python模块。生成使用和帮助信息。

# 用法
- import OptionParser 类
- 创建一个 OptionParser 对象
- 使用 add_option 来定义命令行参数
<!--more -->

每个命令行参数就是由参数名字符串和参数属性组成的。如 -f 或者 –file 分别是长短参数名：
`parser.add_option("-f", "--file", ...)`
- 定义好了所有的命令行参数，调用 parse_args() 来解析程序的命令行：
`(options, args) = parser.parse_args() `

`你也可以传递一个命令行参数列表到 parse_args()；否则，默认使用 sys.argv[:1]。`

parse_args() 返回的两个值：
options，它是一个对象（optpars.Values），保存有命令行参数值。只要知道命令行参数名，如 file，就可以访问其对应的值： options.file 。
args，它是一个由 positional arguments 组成的列表。 

Actions
action 是 parse_args() 方法的参数之一，它指示 optparse 当解析到一个命令行参数时该如何处理。actions 有一组固定的值可供选择，默认是’store ‘，表示将命令行参数值保存在 options 对象里。其它的 actions 值还有：
store_const 、append 、count 、callback 。

```python
parser.add_option("-f", "--file",  
                  action="store", type="string", dest="filename")  
args = ["-f", "foo.txt"]  
(options, args) = parser.parse_args(args)  
print options.filename  
```
当 optparse 解析到’-f’，会继续解析后面的’foo.txt’，然后将’foo.txt’保存到 options.filename 里。当调用 parser.args() 后，options.filename 的值就为’foo.txt’。


# 实例
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-
usage = "usage: %prog [options] arg1 arg2"  
parser = OptionParser(usage=usage)  
parser.add_option("-v", "--verbose",  
                  action="store_true", dest="verbose", default=True,  
                  help="make lots of noise [default]")  
parser.add_option("-q", "--quiet",  
                  action="store_false", dest="verbose",  
                  help="be vewwy quiet (I'm hunting wabbits)")  
parser.add_option("-f", "--filename",  
                  metavar="FILE", help="write output to FILE"),  
parser.add_option("-m", "--mode",  
                  default="intermediate",  
              help="interaction mode: novice, intermediate, "  
                   "or expert [default: %default]")  
				   
#结果
usage: <yourscript> [options] arg1 arg2  
  
options:  
  -h, --help            show this help message and exit  
  -v, --verbose         make lots of noise [default]  
  -q, --quiet           be vewwy quiet (I'm hunting wabbits)  
  -f FILE, --filename=FILE  
                        write output to FILE  
  -m MODE, --mode=MODE  interaction mode: novice, intermediate, or  
                        expert [default: intermediate]  				   
```
当中的 %prog，optparse 会以当前程序名的字符串来替代：如 os.path.basename.(sys.argv[0])。
如果用户没有提供自定义的使用方法信息，optparse 会默认使用： “usage: %prog [options]”。

如果程序有很多的命令行参数，你可能想为他们进行分组，这时可以使用 OptonGroup：
```python
group = OptionGroup(parser, ``Dangerous Options'',  
                    ``Caution: use these options at your own risk.  ``  
                    ``It is believed that some of them bite.'')  
group.add_option(``-g'', action=''store_true'', help=''Group option.'')  
parser.add_option_group(group)  
```
