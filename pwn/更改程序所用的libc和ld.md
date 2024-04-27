打Pwn时赛题环境与本机不同，但是解题又依赖于靶机环境，这时候一般会附带一个 libc.so 用于在本机复现。

## 获取 libc

#### 查看 libc 的版本

```shell
$ strings libc.so.6 | grep "GNU"
```

下载

#### 替换 libc 和 ld

其实就是两条指令的事情，但是这里写的详细一些。

首先通过 ldd 指令可以获得一个二进制文件的依赖库，如下

```shell
$ ldd pwn_binary 
        linux-vdso.so.1 (0x00007fff609fc000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f1a9a2b1000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f1a9a8a4000)
```

这些其实就是写死在 binary 中的信息，patchelf 可以帮我们替换这些东西。

要替换 ld，可以通过 `patchelf --set-interpreter /path/to/ld.so /path/to/binary`

比如用一个自己编译的 ld 替换虚拟机的 ld，只要执行

```shell
patchelf --set-interpreter /glibc/2.27/amd64/lib/ld-2.27.so ./pwn_binary
```

使用 ldd 指令查看一下

```shell
$ ldd pwn_binary
        linux-vdso.so.1 (0x00007ffd08dc2000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd343247000)
        /glibc/2.27/amd64/lib/ld-2.27.so => /lib64/ld-linux-x86-64.so.2 (0x00007fd34383b000)
```

发现替换成功了。如果要替换 libc，就需要使用

```shell
patchelf --replace-needed originlibc newlibc /path/to/binary
```

这里做的是一个 replace，把原来的依赖文件替换为新的依赖文件，以现在这个 `pwn_binary` 为例，就是

```shell
patchelf --replace-needed libc.so.6 /glibc/2.27/amd64/lib/libc.so.6 ./pwn_binary
```

ldd 一下就可以发现替换成功了

```shell
ldd pwn_binary
        linux-vdso.so.1 (0x00007ffc5cf66000)
        /glibc/2.27/amd64/lib/libc.so.6 (0x00007fb021cce000)
        /glibc/2.27/amd64/lib/ld-2.27.so => /lib64/ld-linux-x86-64.so.2 (0x00007fb022286000)
```

如果要替换回到系统使用的 libc，或者是别的 libc，以替换到 2.23 的 libc 为例，这个时候就要这样写

```shell
patchelf --replace-needed /glibc/2.27/amd64/lib/libc.so.6 /glibc/2.23/amd64/lib/libc.so.6 ./pwn_binary
```

对于依赖多个动态库的 elf 文件，也可以用同样的指令进行替换。

这里要注意的是，ld 和 libc 的版本相差不能太大，不然运行时就会出现错误。

然后就是新的问题：一般情况下可能最多给个 libc，不提供 ld，手上也没有相似版本的 ld，又或者是提供的 libc 没有符号，gdb 看不了堆布局很难受。怎么办？请往下看。

### 获取libc版本

然后有两种方法，一是下载对应版本的 ld 和 libc，二是自己手动编译一个。

#### 下载

已经有师傅做好了整理，可以提供自动化的下载，比如 

[glibc-all-in-one](https://github.com/matrix1001/glibc-all-in-one)，但是这个当然不是完整的，有些版本可能需要自己去谷歌然后下载。但是其实版本只要相近就可以了，不是一定要完全一样。特别说一下 

[glibc-all-in-one](https://github.com/matrix1001/glibc-all-in-one) 下载下来的 libc 是有符号的，很棒

#### 编译libc——以 2.23 为例

提前说一下，

[glibc-all-in-one](https://github.com/matrix1001/glibc-all-in-one) 也提供了自动化编译脚本，但是在我的电脑上有时候会编译出错，所以有时候可能还是需要自己编译，这里以编译 2.23 版本作为例子。

##### 准备工作

我的虚拟机为 Ubuntu 18.04。编译旧版本的 libc，最好用旧版本的 gcc，所以我们考虑安装一下，好消息是不同版本的 gcc 是可以共存的。

首先添加旧版本的镜像源。使用 `sudo vim /etc/apt/sources.list`添加

```bash
deb http://mirrors.aliyun.com/ubuntu/ xenial main
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
```

然后 `sudo apt-get update`更新源。

安装旧版本的gcc

```bash
sudo apt-get install gcc-4.8
sudo apt-get install gcc-4.8-multilib
sudo apt-get install g++-4.8
sudo apt-get install g++-4.8-multilib
sudo apt-get install gcc-5
sudo apt-get install gcc-5-multilib
sudo apt-get install g++-5
sudo apt-get install g++-5-multilib
```

添加到维护程序版本的 `update-alternatives`中

```bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 40 --slave /usr/bin/g++ g++ /usr/bin/g++-4.8
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 50 --slave /usr/bin/g++ g++ /usr/bin/g++-5
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 70 --slave /usr/bin/g++ g++ /usr/bin/g++-7
```

通过 `sudo update-alternatives --config gcc`就可以切换版本了

编译2.23版本的libc时我们选择gcc-4.8

##### 编译安装

首先下载2.23版本的源码

```bash
wget https://ftp.gnu.org/gnu/glibc/glibc-2.23.tar.gz
```

然后解压

```bash
tar -zxvf glibc-2.23.tar.gz
cd glibc-2.23
```

建立目录并完成设置

###### 64位

```bash
mkdir build64
cd build64
sudo CFLAGS="-g -g3 -ggdb -gdwarf-4 -Og -Wno-error" CXXFLAGS="-g -g3 -ggdb -gdwarf-4 -Og -Wno-error" ../configure --prefix=path_to_install
sudo make -j$(nproc)
sudo make install
```

###### 32位

```bash
mkdir build32
cd build32
sudo ../configure --prefix=/path/to/install --host=i686-linux-gnu\
CC="gcc -m32" CXX="g++ -m32" \
CFLAGS="-g -g3 -ggdb -gdwarf-4 -Og -Wno-error" \
CXXFLAGS="-g -g3 -ggdb -gdwarf-4 -Og -Wno-error"
sudo make -j$(nproc)
sudo make install
```

这里 make 后面的 -j$(nproc) 是指定多线程编译，如果你的电脑 CPU 线程比较多，那就可以大幅加速编译过程。

make 的时候很可能会出现如下错误

[![img](assets/更改程序所用的libc和ld/3877627060.png)](https://www.cjovi.icu/usr/uploads/2021/01/3877627060.png)

解决办法就是在这三个变量后面加 `__attribute__ ((nocommon))`。他们在/glibc-2.23/misc/regexp.c中（不出意外是从第33行开始的）。

[![img](assets/更改程序所用的libc和ld/3370886825.png)](https://www.cjovi.icu/usr/uploads/2021/01/3370886825.png)

改成这样就可以了。然后应该就可以`sudo make`和`sudo make install`了（据说还可能有别的问题，但是我没有碰到）。

至此编译安装的过程就结束了，编译出的ld和libc在安装目录下的lib文件夹里面。我这里是叫`ld-linux.so.2`和`libc.so.6`。可以考虑cp到elf文件所在的目录下。

*最近在编译 2.34 版本的 glibc 时碰到了大量的 -Werror 问题，在较高版本 gcc 中可以用 `-w` 来忽略所有的警告，不再把警告当作错误。类似的参数低版本似乎是 `-Wno-error`*