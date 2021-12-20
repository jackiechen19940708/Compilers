作者：陈宇飞
链接：https://www.zhihu.com/question/43598164/answer/122186527
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

我所知道的现在两个compiler大的方向是LTO跟autoFDOLLVM Link Time Optimization: Design and Implementationlink time optimization 简单来说就是在link stage的时候再做一遍优化。现在所有的优化都是在生成.o 文件的时候做的，在链接的时候其实可以做更多的优化。 llvm给的例子如下--- a.h ---
extern int foo1(void);
extern void foo2(void);
extern void foo4(void);

--- a.c ---
#include "a.h"

static signed int i = 0;

void foo2(void) {
  i = -1;
}

static int foo3() {
  foo4();
  return 10;
}

int foo1(void) {
  int data = 0;

  if (i < 0)
    data = foo3();

  data = data + 42;
  return data;
}

--- main.c ---
#include <stdio.h>
#include "a.h"

void foo4(void) {
  printf("Hi\n");
}

int main() {
  return foo1();
}
linker在看到所有文件之后发现 1. foo2() 永远不会被调用，可以把foo2删掉2. i 永远是0哦，没有人会改i 了2. 那么i 永远不会小于0， foo3 也不回被调用，if 和foo3 全部删！删！删！3.最后foo4 也可以被删掉，因为没人调用这里给的例子相对trivial，但是大家可以大致理解为什么LTO厉害了吧。有上帝视角哦。LTO的坏处是：编译实在是太慢了！太慢了！大的软件上面要用LTO简直让人抓狂GitHub - google/autofdo: AutoFDOFDO 是feedback driven optimization。很多Java的人都吹说jvm可以一边跑一边自动优化，现在通过FDO，c++的compiler也可以啦！FDO的坏处是：设置特别麻烦，对小公司来说现在还是太昂贵了。大公司基本上得在自己的服务器上定时去搜集这些信息，然后在编译时间总结一下。这个系统这是得大把真金白银砸出来的
