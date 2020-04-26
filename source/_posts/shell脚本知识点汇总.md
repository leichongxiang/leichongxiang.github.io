title: shell脚本中问题
date: 2018-03-04 12:53:35
tags:
 - linux
 - shell
categories: [linux]

---

## set -e
等价于set -o errexit
在"set -e"之后出现的代码，一旦出现了返回值非零，整个脚本就会立即退出。有的人喜欢使用这个参数，是出于保证代码安全性的考虑。但有的时候，这种美好的初衷，也会导致严重的问题。

扩展：
set -x
-x还有另一种写法-o xtrace


## set -o pipefail
管道前的命令的返回值和管道后命令的返回值都为0，才是0.
举例子：
```bash
# test.sh
set -o pipefail
ls ./a.txt |echo "hi" >/dev/null
echo $?

#运行test.sh，因为当前目录并不存在a.txt文件，输出：
ls: ./a.txt: No such file or directory
1
#设置了set -o pipefail，返回从右往左第一个非零返回值，即ls的返回值1


#注释掉set -o pipefail这一行，再次运行，输出：
ls: ./a.txt: No such file or directory
0
# 没有set -o pipefail，默认返回最后一个管道命令的返回值,为0
```

## set扩展
```
- 结束选项的信号,将引发其余的参数被赋值到位置参数中
-- 如果第一个参数是以-开头的，就将haproxy追加到位置参数上
if [ "${1#-}" != "$1" ]; then
  echo $1
  set -- haproxy "$@"
fi
echo $1
echo $@
It's appending the value of $@ onto the end of the positional parameters.

# 结果
leicx@node01:~/ahi$ sh test.sh -a
-a
haproxy
haproxy -a

```
## shopt命令
是set命令的一种替代，很多方面都和set命令一样，但它增加了很多选项。可有使用“-p”选项来查看shopt选项的设置。“-u”开关表示一个复位的选项，“-s”表示选项当前被设置。

## ${str:n:m}
表示提取字符串str中，从第n个开始的,后边m个字符
