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

