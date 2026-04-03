# 关于gcc编译是代码中有pow报错的异常情况.

### 我现在有两个代码块,在gcc编译时使用quick-c.nvim快速编译和运行,其中第一个代码块没有报错并且正常运行,但是第二个代码块却报错,提示undefined reference to 'pow'  

代码块1:  
```c
#include <math.h>
#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
  int result = pow(1, 2) + pow(2, 3) + pow(3, 4) + pow(4, 5) + pow(5, 6);
  printf("result = %d\n", result); return EXIT_SUCCESS;
}

```
  
此代码块正常运行,没有报错.  
  
代码块2:  
```c
#include <math.h>
#include <stdio.h>

int main() {

  int i;
  unsigned long long int sum = 0;
  unsigned long long int temp;
  unsigned long long int weight;
  for (i = 0; i < 64; i++) {

    temp = pow(2, i);
    sum = sum + temp;
  }
  weight = sum / 25000;

  printf("舍罕王应该给予达依尔%llu粒麦子!\n", sum);
  printf("如果每25000粒麦子为1kg,那么应该给%llu粒麦子!\n", weight);

  return 0;
}

```
  
该代码块运行报错提示undefined reference to 'pow'  
于是我是用 **:lua print(vim.inspect(require('quick-c')))** 查找了一下quick-c的配置文件结构,发现在quick-c编译时并没有添加编译标识,而第一个代码块之所以能够直接编译成功就是因为 **pow()函数的参数** 都是 **字面常量** 的时候,编译器具有 **常量折叠(constant folding)** 优化功能,可以在编译期间就计算出这些变大是的值,由此编译器直接就在编译时计算 **pow()** 的表达式的结果.因为它在链接阶段并没有真正调用 **pow()** 函数,所以不需要 **-lm** 数学库.  
  
咱们来验证一下:
将代码块1改为使用变量来进行 **pow()** 函数计算.
```c
#include <math.h>
#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    int a = 1, b = 2, c = 3, d = 4, e = 5, f = 6;
    int result = pow(a, b) + pow(b, c) + pow(c, d) + pow(d, e) + pow(e, f);
    printf("result = %d\n", result); 
    return EXIT_SUCCESS;
}
```
  
在这种情况下,编译器无法在编译时确定变量的值,必须在运行的时候调用 **pow()** 函数,这时就会出现"underfined reference to 'pow'"错误.  
因此这就是第二个代码块编译报错的原因.  
### 我尝试的解决方法有两个:  
一是针对quick-c的配置文件配置compile_flags.  
在 Neovim 配置文件中（如 ~/.config/nvim/init.lua 或插件配置文件），添加以下内容：  
```lua
require('quick-c').setup({
  -- 保持原有 config 不变
  config = {
    -- 你现有的配置...
    autosave = { ... },
    betterterm = { ... },
    -- ... 其他配置项
  },
  -- 👇 添加这行
  compile_flags = {
    c = { '-lm' },
    cpp = {}
  }
})
```
  
然后重启nvim,对.c文件编译.还是报错.  
于是我在nvim里执行:  
```vim
:lua print(vim.inspect(require('quick-c').config.compile.flags))
```
  
输出结果为 **nil**  
说明配置没有被加载成功,于是我恢复init.lua,并在lua/plugins/quick-c.lua中配置config  
```lua
config = function()
  require("quick-c").setup({
    
    compile = {
      cflags = { "-lm" },
      cxxflags = {},
    },
    
  })
end,
```
验证命令  
```vim
:lua print(vim.inspect(require('quick-c').config.compile.cflags))
```
此时输出结果为:  
```lua
{ "-lm" }
```
  
再次对代码块2进行<leader>cqR  仍然是提示报错undefined reference to 'pow'.  
这说明 -lm没有没执行.  
于是到这里我就不知道该怎么做了.  




方法二是直接在kitty终端中进行gcc编译:
```zsh
gcc -lm test2.c -o test2 && ./test2
```

但是我长期使用代码还是要配置config才行.
