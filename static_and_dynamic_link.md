几乎在所有的linux发行版上，都存在着一定程度的不兼容问题，如何让自己的程序能尽量多的在发行版上运行？ldd大多数的binary，会发现他们都依赖于libc.so，这是glibc提供的C标准库，那么就有了一个很直观的计划：静态链接glibc。

静态链接gblic，首先你需要一个静态版本的glibc，即libc.a而不是libc.so。在要注意，你无法静态链接动态库，也无法动态连接静态库，他们的文件格式并不相同，静态链接一个动态库，需要它的静态版本。

随之而来有一个问题，如何同时静态和动态链接。

有下面几种方法：

* 采用gcc的-Wl,-Bstatic和-Wl,-Bdynamic，一个简单的例子：

gcc main.o –Wl,-Bstatic –lfoo –Wl,-Bdynamic，会把foo静态链接。

-Wl,-Bstatic会把这个参数之后的库静态链接，直到遇见-Wl,-Bdynamic为止。在通常情况下，为了避免产生错误，推荐在每次用–Wl,-Bstatic都加上–Wl,-Bdynamic。（-Wl是gcc传递链接参数给ld的方法，具体请man gcc和man ld）

* 在链接的时候直接使用库的名字，比如/usr/lib64/xxx.a和/usr/lib64/yyy.so，gcc会很聪明的根据相应的扩展名采取静态或者动态链接。或者使用一个很少见的方式-l:xxx.a，在可以动态链接的系统上，使用-lxxx的话链接优先采用的是动态链接库，通过-l:xxx的话，会在LD_LIBRARY_PATH通过xxx文件名找对应的库并采取相应的链接方法。

* 用gccd的- -static，在- -static之后的所有库都采用静态链接的方式。但是如果你使用的某个库只有动态版本的话且加在 –static之后的话，会因为找不到符号而报错。这里有一个替代的参数，--mostly-static。

 

在链接的时候，顺序是很关键的，尽量把你用到的库写在.o和源码的后面，链接的时候ld先会看一遍所有.o和源码中的符号，如果缺失的符号在某一个库中，这个库才会被链接上，而且ld并不会往回看，这里很容易引发奇怪的undefined reference问题。当一堆.a互相依赖的时候，你可以把他们放在-Wl,--start-group和-Wl,--end-group中间，ld会反复尝试链接这个区间之内的所有库，直到没有新的库被加入为止（这个用法也可以用于.o）。例如：gcc -Wl,--start-group foo.a bar.a -Wl,--end-group。这时候你可能要问，为什么.so没有这样的问题？因为.so已经明确指定了自己的依赖，就像你在ldd的时候看到的一样。

如果你的一个.so依赖一个.a，可以通过--whole-archive把这个.a完整的包含到.so里（前提这个.a里所有的.o你都加上-fPIC），这个参数依旧是ld的（还记得通过-Wl把在gcc里把参数传递给ld吧，-Wl, --whole-archive）。

在某些特殊情况下，你需要把libgcc和libstdc++都静态链接，gcc里也有对应的参数-static-libgcc和-static-libstdc++，来把这两个库静态链接。

 

记住一个原则，如果你要静态连接某个lib LIB，那么需要这个库不被其他所有动态链接库引用。所以，想要静态链接libc是很困难的，一个正确的解决方式是通过glibc的兼容性，在比较老的glibc版本上编译，在新的glibc上自然可以运行。

最后，真的没有办法静态链接libc了吗？不然，对于某些简单的程序而言，你在gcc后面直接加上—static是可以实现编译出静态链接到libc的bin的。记住，--static必须紧跟在gcc的后面，因为gcc编译的时候会在你的lib后面加上一些通用库，比如libgcc和libstdc++等等。就是gcc main.cpp实际上会被gcc处理撑gcc main.cpp –lgcc –lstdc++。把—static加在最前面，使得所有的库都被尝试静态链接，如果没有链接错误，那么就恭喜你，成功了。

关于gcc的各种参数，可以看gcc的在线文档（https://gcc.gnu.org/onlinedocs/gcc/）或者man gcc。
