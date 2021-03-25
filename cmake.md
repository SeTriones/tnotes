# cmake 相关知识点

## 指定 cmake 用的 gcc 版本

搜了一下文章，有一些例子是：

```bash
export CC="/path/newgcc/gcc"
export CXX="/path/newgcc/g++"
```

然而并没有用。

找到了一个更好的办法是通过 -D 向 cmake 传递参数，例如

```bash
cmake -DCMAKE_C_COMPILER="/path/newgcc/gcc" -DCMAKE_CXX_COMPILER="/path/newgcc/g++"
```

记得要让新版本的 gcc 找到对应的 libstdc++，所以需要添加一步

```bash
export LD_LIBRARY_PATH=/path/newgcc/lib64:$LD_LIBRARY_PATH
```

因为新的 gcc 需要 link 自己的 libstdc++，我把新 gcc 的 lib64 目录放到了 LD_LIBRARY_PATH 的最前面。

但是小伙伴 @ahyangyi 提到，不光需要 link libstdc++，可能需要 link 一些比较奇怪的 .a 和 .o，对于这个问题我们都不太确定。所以想到了一个办法，设置 sysroot。这时候找到了 cmake 的一个变量 CMAKE_SYSROOT，可以 -DCMAKE_SYSROOT 设置对应的 sysroot。但设置 sysroot 的方法我并没有验证过，用  LD_LIBRARY_PATH 的可以编译过（没有运行）。可以给后来人做一个参考。如果有问题，欢迎提 issue。