# clibparser（GLR Parser）

C++实现的LR Parser Generator。

- 文法书写方式：以C++重载为基础的Parser Generator。
- 语义分析：在LR解析的过程中，状态机向符号表提供分析接口，使得状态转换可手动干涉，解决“`A*b`”问题
- 识别方式：**以下推自动机为基础，向看查看一个字符、带回溯的LR分析**。
- 内存管理：自制内存池。

## 文章

- [【Parser系列】实现LR分析——开篇](https://zhuanlan.zhihu.com/p/52478414)
- [【Parser系列】实现LR分析——生成AST](https://zhuanlan.zhihu.com/p/52528516)
- [【Parser系列】实现LR分析——支持C语言文法](https://zhuanlan.zhihu.com/p/52812144)
- [【Parser系列】实现LR分析——完成编译器前端！](https://zhuanlan.zhihu.com/p/53070412)
- [【Parser系列】实现LR分析——识别变量声明](https://zhuanlan.zhihu.com/p/54716082)
- [【Parser系列】实现LR分析——解决二义性问题](https://zhuanlan.zhihu.com/p/54766453)

## 功能

- 词法分析阶段由lexer完成，返回各类终结符。
- 语法分析阶段由parser完成，输入产生式，通过生成LALR表来进行分析。
- **LR识别完成，生成AST完成。**
- **[C语言文法](https://github.com/antlr/grammars-v4/blob/master/c/C.g4)基本实现！请参阅cparser.cpp！支持多种基本数据类型。**
- 虚拟机指令生成。

文法支持顺序、分支、可选、跳过终结符。目前可以根据LR文法**自动**生成AST。后续会对AST进行标记。

## 示例代码

```cpp
struct sx {
    int *a;
    double b;
};
int *a, *_a, _b = 1;
char *_c = "Hello world!";
double *b;
int *main1(int c, float *d, sx x, char y) {
    unsigned int *a, *b;
    float d, *e, f;
    sx *s1;
    //sy *s2;
}
int main2() {
    int a, b, c;
    a + b + 1 - 2;
    a = b = c;
    //--1+++---1++;
    //a.b----[1](1,2);
}
int ta, test(unsigned int a, double b, char c), tb(long d), tc, td(int e);
char *aa = "bajdcc";
int bb = 2;
int dd = bb;
int main(){
    char *ee = "hello";
    char *ff = aa;
    char *gg = ff;
    int cc = 1;
    aa;
    bb;
    bb + 1;
    dd;
    cc + 1;
    int x = 1, y = 2, z = 3;
    x += y *= z;
    z = x < y;
    z = 1 * 2 / 3 + 4;
    ++z + z;
    z++ + z;
    &z;
    *ee;
    *(ee + 2);
}
```

结果（每个单词都保存了在源文件中的位置，这里省略）：

**因内容太多，故放置在文件[output.txt](https://raw.githubusercontent.com/bajdcc/clibparser/master/output.txt)中。**

## 调试信息

实现C语言的解析。

生成NGA图，去EPSILON化，生成PDA表，生成AST。

以下为下推自动机：

**由于太长，文法定义和下推自动机在根目录下的grammar.txt文件中！**

以下为下推自动机的识别过程（**太长，略**），如需查看，请修改cparser.cpp中的：

```cpp
#define TRACE_PARSING 0
#define DUMP_PDA 0
#define DEBUG_AST 0
#define CHECK_AST 0
```

将值改为1即可。

## 测试用例

采用[Antlr 4 - C test](https://github.com/antlr/grammars-v4/tree/master/c/examples)中的9个c文件。

测试文件在test.cpp中，编译为clibparser-test。

**9个测试用例均通过。**

## 目标

当前进展：

- [x] 词法分析（LL手写识别）
   - [x] 识别数字（科学计数+十六进制）
   - [x] 识别变量名
   - [x] 识别空白字符
   - [x] 识别字符（支持所有转义）
   - [x] 识别字符串（支持所有转义）
   - [x] 识别数字（除去负号）
   - [x] 识别注释
   - [x] 识别关键字
   - [x] 识别操作符
   - [x] 错误处理
- [x] 生成文法表达式
- [x] 生成LR项目
- [x] 生成非确定性文法自动机
    - [x] 闭包求解
    - [x] 去Epsilon
    - [x] 打印NGA结构
- [x] 生成下推自动机
    - [x] 求First集合，并输出
    - [x] 检查文法有效性（如不产生Epsilon）
    - [x] 检查纯左递归
    - [x] 生成PDA
    - [x] 打印PDA结构（独立于内存池）
- [x] 生成抽象语法树
    - [x] 自动生成AST结构
    - [x] 美化AST结构
    - [x] 美化表达式树（减少深度）
- [x] 设计语言
    - [x] 使用[C语言文法](https://github.com/antlr/grammars-v4/blob/master/c/C.g4)
    - [x] 实现回溯，解决移进/归约冲突问题，解决回溯的诸多BUG
    - [x] 调整优先级
    - [x] 【关键】解决“`A*b`”问题，部分语义分析嵌入到LR分析之中
- [ ] 语义分析
    - [x] 识别重名
    - [x] 输出错误单词的位置
    - [x] 基本类型（单一类型及其指针，不支持枚举、函数指针和结构体）
    - [x] 识别变量声明（类型可为结构体）
    - [x] 识别函数声明（识别返回类型、参数）
    - [x] 识别结构体声明和类型
    - [x] 计算变量声明地址
    - [ ] 识别语句
    - [ ] 识别表达式
- [ ] 代码生成
    - [x] 全局变量及初始化
    - [x] 局部变量及初始化
    - [ ] 形参
    - [ ] 函数调用
    - [ ] 数组寻址
    - [ ] 结构体成员（点和指针）
    - [x] 一元运算
    - [x] 二元运算
    - [x] 赋值语句
    - [ ] 返回语句
    - [ ] if语句
    - [ ] for语句
    - [ ] while语句
    - [x] 取址和解引用
- [x] 虚拟机
    - [x] 设计指令（寄存器式分配）
    - [x] 设计符号表
    - [x] 解析类型系统
    - [x] 生成指令
    - [x] 虚页分配（已实现）
    - [x] 运行指令
    - [ ] 扩展指令

1. 将文法树转换表（完成）
2. 根据PDA表生成AST（完成）

## 改进

- [ ] 生成LR项目集时将@符号提到集合的外面，减少状态
- [x] PDA表的生成时使用了内存池来保存结点，当生成PDA表后，内存池可以全部回收
- [x] 生成AST时减少嵌套结点
- [ ] 优化回溯时产生的数据结构，减少拷贝
- [ ] 解析成功时释放结点内存
- [x] 将集合结点的标记修改成枚举
- [x] 可配置归约与纯左递归的优先级
- [x] LR分析阶段提供语义分析接口
- [x] 美化表达式树（减少深度）

## 参考

1. [CParser](https://github.com/bajdcc/CParser)
2. [CMiniLang](https://github.com/bajdcc/CMiniLang)
3. [vczh GLR Parser](https://github.com/vczh-libraries/Vlpp/tree/master/Source/Parsing)
4. [antlr/grammars-v4 C.g4](https://github.com/antlr/grammars-v4/blob/master/c/C.g4)