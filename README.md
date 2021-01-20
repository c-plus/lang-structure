# C+ lang frame 0.1

Overview：  
C+ lang is for programming learning. The goal is simple, safe, easy to learn and use.
Main features: management of ownership and using domain, access controller, virtual class and polymorphic function, interface class, macro template, generics, unified code organization, use of C++ objects and functions, instant intermediate code, etc.  
The following is Chinese, to be translated

## 1. 变量、常量及引用

### 1.1 变量声明及初始化

格式：`数据类型 变量;`   
或同时初始化：`数据类型 变量 = 初始化数据;`  
例：`u8 x = 1;`

局部变量使用前需初始化，可以先声明后初始化。全局变量如未初始化，系统会默认初始化。数值类型初始化为0，字符初始化为空。声明同时初始化可省略数据类型，C+自动推断。  
例：`x = 1;`

变量命名规则参考：小写字母，用下划线分开单词。函数名命名规则类似。

### 1.2 变量的可变和不可变

变量默认不可变。可变变量使用锐音符声明：``数据类型 ` 变量;``  
例：

```
u8 ` x = 1;  //声明可变变量x并初始化
` y = 1  //声明可变变量y，并初始化，自动推断类型
```

### 1.3  变量的内存分配。

使用时内存大小未确定或使用时会占用较大内存的变量原则上在堆上分配内存。声明变量时在类型后变量前加“~”，代表堆上分配变量，分配后内存大小可变。例：

```
u8 ~ ` x = 5;  //在堆上分配可变变量x
u8 ~ ` y[] = {1,2,3};  //在堆上分配可变长度数动态数组y[]，数组内容可修改；
u8 ~ ` y[100] = {1,2,3};  //在堆上分配固定长度数组y[100]，数组内容可修改；
u8 `z[3] = {1,2,3};  //在栈上分配数组z[3]，数组长度不可变，内容可修改；
```

所有标量类型、数据集存储大小固定且在？字节以内的默认栈上分配。超出此范围编译器提醒使用“~”符号在堆上分配内存。类对象一般在堆上分配内存。

### 1.4  变量作用域及所有权

 变量离开作用域后变量拥有的内存将被系统自动释放，变量被无效。变量内存的所有权可以被移动和拷贝。将一个变量赋值给另一个变量时，如果是堆上变量，前者变量的堆上内容所有权被移动给后者，前者被无效；如果是栈上变量，前者变量的堆上内容所有权被拷贝给后者，前者依然有效。  
将值传递给函数类似变量赋值，也存在所有权的移动或拷贝。因此这类情况下一般使用变量引用传值。函数的返回值也存在作用域的转移、所有权的变化。

### 1.5  变量控制器。

变量声明时可以设置变量控制器，格式：`变量类型 变量名: 控制器;`

- 字段变量的访问控制，具体见3.1。  
  :bi ，可以按位访问变量存储内容。  
  :by ，可以按字节访问变量存储内容。  
  :数字-数字-...，按相应数字的位数分段访问变量存储内容。

- 变量赋值控制
  变量声明时可用过滤器:{}来设置变量赋值时的检查或其他动作。格式：`类型 变量名:{赋值过滤代码};`  
  对变量赋值时，用赋值过滤代码展开替换赋值的右值。代码由编译期替换并展开，代码不能太复杂。例：

  ```
  u8 ` x: {if(0<$x<5) {$x} else print(“warning...”);} = 0;  //如右值($x)在0~5间，返回$x，否则...
  ```

- 类变量多态控制
  多态函数参数中实现多态的类引用变量要被设为虚类，具体见7.3。  
  通过“:vt”控制器设置，格式：`类类型 &参数名:vt;`

### 1.6  常量

申明常量格式：`const 数据类型 常量;`  
常量有：数值常量、字符常量、字符串常量、字符常量。

### 1.7  引用和函数引用

- 引用
  引用是变量的别名。编译时引用对象可确定的为直接引用，不单独分配内存空间，声明同时确定引用对象；编译时引用对象不确定的为间接引用，如函数参数、函数返值接收者、类成员变量，先声明后通过参数传递、返值传递和类初始化指向具体对象，并分配内存空间。  
  一般情况下生命周期内引用不可改变其引用对象，动态元组(链表)除外。  
  直接引用声明格式：`类型 &引用名 = &引用对象;`  
  引用也分可变和不可变。在一个作用域下一个可变变量只能有一个可变引用，任意数量不变引用。

- 函数引用
  函数引用是一种特殊引用，是函数入口地址的标记，可以接收其他函数地址。同样直接函数引用不单独分配内存空间；间接函数引用在函数调用时分配内存空间。  
  声明格式：`返值类型 函数引用名(无名参数表) = 引用函数名; `  
  例：

  ```
  u8 f(u8) {...}
  u8 x(u8)=f;  //x引用f函数
  ```

## 2. 数据类型

### 2.1 标量

标量是不可分的最小数据类型单元，类似其他语言的基本数据类型。标量类型也可以定义关联数据处理方法。{S}代表任意一个标量类型。

- 数值类型
  整数型默认i32，浮点数默认f64。  
  ibit、ubit依赖运行程序的计算机CPU架构，64位架构是64位，32位架构是32位的。  
  {V}代表任意一个数值标量类型。  
  可以在数值后面加上类型，以更清晰说明类型。如：5u8   u8类型数值5。

| 长度  | 有符合 | 无符号 |
| ----- | ------ | ------ |
| 8位   | i8     | u8     |
| 16位  | i16    | u16    |
| 32位  | *i32   | u32    |
| 64位  | i64    | u64    |
| 128位 | i128   | u128   |
|       | ibit   | ubit   |
| 32位  | f32    |        |
| 64位  | *f64   |        |

- char字符类型
  默认4字节单个Unicode字符，如’A’、’王’。中文等使用utf-8编码。  
  b开头，如b’A’，代表一个字节的ASCII码。  
  如是英文字符，char变量长度是一个字节，如是中文是3个字节。 

- bool布尔类型：有两个值true和false。默认false。

- 标量数据类型间的转换
  标量数据类型相互间可以通过“()”强制转换，或通过from方法在不同数据类型之间转换。例：

  ```
  u8x = 5;
  y = (char)x;  //将x转成字符，或char y = char::from(x);
  z = (u8 )‘5’;  //将字符’5’转成数字
  ```

### 2.2 数据集及类型

数据集是多个相关数据(值)的组合，这些数据称为是该数据集的成员。数据集的成员可以是数据集。成员数据类型相同的数据集称为数组；如不同称为元组、类等。要不要加字典map？  
数据集列表格式：`{数据1, 数据2, ...}`，用“,”隔开数据。  
用关键字set声明元组、类等数据集类型： `set 数据集类型名 {数据集结构};`  
可以直接匿名类型定义变量： `set {数据集结构} 变量1, 变量2...;`，或省略set：`{数据集结构} 变量1, 变量2...;`  
数据集结构的定义放在{}里，一般用“;”结束每个成员声明。  
数据集类型可定义关联数据处理方法。类的数据成员和方法成员分别称成员变量和成员函数。  
一般类型名大写字母开头，与变量不同，大写字母分开单词。

- 字符串
  字符数组，类似Rust，含长度信息，不以’\0’结尾。例：

  ```
  char x = “abc”;  //字符串常量，不可修改。没有’\0’
  char ` y[5] = “abc你好’’ //栈上的字符数组，相当{‘a’, ’b’, ’c’, ‘你’, ‘好’}
  char ~ ` z[] = “abc’’;  //堆上可变长度的字符串
  u8 ` i = userInput();  //用户输入到变量i
  char ~ ` m[i];  //长度为i的可变长度堆上字符串
  ` m[] = “abc” + “def”;  //声明m并初始化，结果m = “abcdef”
  m += “ghi”;  //结果m = “abcdefghi”
  m -= “ghi” //结果m = “abcdef”
  ```

- 数组
  相同类型成员数据集，基本同rust、C++语言。 

- 元组
  不同类型数据成员的集，如`{123, “abc”, true}`。  
  声明元组类型： `set 元组类型名 {类型1; 类型2; ...};`  
  不允许递归定义，但可定义本元组的引用作为成员。  
  例：

  ```
  set M {u8; char; bool};  //声明了一个叫M的元组类型，相当一个成员匿名的类
  M m[3];  //定义了一个包含3个M类型成员的元组数组
  set {u8; char; bool} k[3];  //匿名声明了一个元组数组
  s = {123, “abc”, true};  //定义一个元组变量s，自动匹配默认数据类型
  u8 n = s[0];  //用索引访问元组成员
  (x, y, z) = m;  //声明x,y,z三变量并初始化，解构m三个数据成员并模式匹配，并自动推断类型
                  //相当x=123; y=”abc”;z=true;
  ```

- 类
  有成员变量(有名字的数据成员)和成员函数的元组类型。  
  类声明格式：`set 类名 {成员声明;... | 成员声明;... |成员声明;...};`  
  类声明的数据集结构分三段。从左起第一段里的成员可公开访问，第二段限制访问，第三段不可访问。不允许递归定义，但可定义本类的引用作为成员。不允许在类的内部定义类。类对象使用“.”访问成员。对象默认分配在堆上。不可匿名类型声明类变量。类可以嵌套，成员可以为其他类。成员变量名不可为数字，首字符不可为下划线。例：

  ```
  set M {u8 x; char y; | bool z;}  //定义一个叫M的类，成员x,y可公开访问，z限制访问
  M m1 = {123, “abc”, true};  //声明一个M类型的类并初始化
  u8 s = m1.x;  //通过“.”访问m1的x成员
  u8 s = m1[0];  //通过索引访问m1的成员
  (u, v, w) = m1;  //通过解构m，声明并初始化u, v, w变量
  M1 m2[];  //声明一个堆上的M类型的类数组
  set N {||u8 x; char y; bool z;};  //类N的对象成员全部不可访问
  set P {|u8 x; char y; bool z;};  //类N的对象成员全部限制访问
  set Q {u8 x; char y; bool z;};  //类P的对象成员全部可访问
  {u8 x; char y; bool z;} m3 = {123, “abc”, true};  //错，不可匿名类型声明类变量
  ```

- 哈希图表
  特别的元组数组。声明格式：`set {键类型:值类型} 图表名[];`  
  键和值关联一一对应，键不能重复。

- 枚举
  声明枚举类型：`set 枚举类型名 {成员1 ^ 成员2 ^ ...};`  
  可以附加一定信息到枚举成员里，如下：

  ```
  set E {Max(u8) ^ Min(char)};
  e = E::Min(“最小”);  //附加了”最小”信息，可以用模式匹配取出附加信息
  set Option {Some({T}) ^ None};  //类似Rust里的Option，但Some和None必须一起使用
  ```

- 联合
  联合类型数据集的成员变量使用同一存储空间，一次只能有一个成员变量使用该空间。联合变量不能被直接使用，只能使用其成员变量。联合变量默认对第一个成员变量初始化。   
  声明联合类型： `set 联合类型名 {成员1 + 成员2 +...};`   
  例：

  ```
  set U {u8 ` x + i32 ` y};  //联合的成员变量总是可变的
  U ` u;  //联合变量总是可变的。
  u.x = 2;  //此时x使用这段内存，这段内存的内容是2
  u.y = 4;  //此时y使用这段内存，这段内存的内容是4
  ```

- 位段
  整数类型变量通过设置变量访问控制控制器，将变量划分成更小的位段单元，用“.”符号按位段号访问变量内部数据。这样的变量称为位段变量。位段变量实现几个小变量拼在一起共享一个存储空间。可直接对普通变量用位操作来实现位段变量的相同功能，实际上编译器就是通过位操作来实现位段变量的功能。但相比位操作，位段变量对用户来说更简单。例：

  ```
  u8 ` x:1-3-4 = 5;  //声明并初始化一个位段变量x，有3个位段，分别对应1,2~4,5~8位
  x.0 = 1;  //第一个位段赋值1，即x第一位设为1
  x.1 = 0b00000101;  //第二个位段，即2~4位设为101
  x.2 = 0b00001011;  //第三个位段，即5~8位设为1011
  u16 ` y:bi = 6;  //按位访问，16位有16个字段
  y.1 = 1;  //第2位设为1
  y.15 = 0;  //第16位设为0
  u16 ` z:by = 7;  //按字节访问，有两个位段
  z.0 = 0b11010101;  //将第1位段，_z的1~8位分别设为11010101
  z.1 = 0x9E;  //将第2位段，_z的9~16位分别设为10011110
  ```

- 序列
  有规律的一组数值或字符。   
  序列定义：`起始索引值..结束索引值{序列公式}`  
  如：1..3{y=2x}，即{2,4,6}。  
  索引是相邻数据间隔最小的整数或字符序列，如0..9，0到9的10个数；’A’..’Z’，A到Z的26个大写字母。

### 2.3 数据集对象的成员访问及选择

- 成员访问
  类和联合通过“对象名.成员名”访问。其他通过“对象名[索引号]”访问，枚举、字段、序列除外。 例：

  ```
  set M {u8; char; bool};
  M m = {123, “abc”, true};  //m[0]是123，m[1]是”abc”，m[2]是true
  char x[5] = “abc你好”;  //x有5个成员，x[0]是’a’，x[2]是’c’，x[3]是’你’，x[5]是’好’
  ```

  数据集可以嵌套数据集，后者称为嵌套成员。有嵌套成员的数据集的存储表现是将成员和嵌套成员按定义的先后顺序展开到最小数据单元(标量)，平铺。可以通过“对象名[.单元索引号]”访问这些最小标量单元，枚举、位段除外。重复继承的派生类只展开一份重复的基类。如：

  ```
  set M {u8 ; char; bool;};
  set N {M m; u16 a[3];};
  set X {N n; u8 b;};
  X x = ... ;
  ```

  M/N/X分别展开如下，  
     {u8,  char,  bool, u16,  u16,  u16,  u8}  
  M [.0]   [.1]    [.2]  
  N [.0]   [.1]     [.2]     [.3]   [.4]    [.5]  
  X [.0]   [.1]     [.2]     [.3]   [.4]    [.5]   [.6]  
  x[0]访问第1个成员n，x[1]访问第2个成员b。x[.1]访问x[0]的char，x[.3]访问n[1]的a[0]，x[.6]访问同x[1]。  
  x..代表x的成员索引序列，x...代表标量数据索引序列。  
  x[]代表x数据集遍历的当前成员，x[.]代表数据集遍历的当前标量单元。

- 数据集对象的成员选择
  例，一个{u8, u8}数据集里：  
  {2,_}指的是第一个数据是2，第二个数据任意的所有实例。  
  {2, !=3}指的是第一个数据是2，第二个数据不为3的所有实例。  
  {2, 3..8}指的是第一个数据是2，第二个数据是3到8的所有实例。  
  {2, >3&&<8}指的是第一个数据是2，第二个数据在3~8之间的所有实例。

### 2.4 数据集成员的增减

数据集成员数量动态变化的动态数组和动态元组变量可以通过push、pop、del、insert等方法减少或增加数据。或者使用“+”、“+=”、“-”、“-=”运算符。通过have方法确认是否包含某数据，get方法获得数据。  
动态元组，可放入任意类型的数据，并改变长度，类似链表。声明形式：``{T} ~`动态元组名 {};``  
动态数组的声明形式：``{T} ~` 动态数组名[];``  
例：

```
{T} ~` m {};
m = {123, “abc”} + {456, “def”};  //与同类数据集运算结果不同，是{123, “abc”, 456, “def”}
m -= {“def”};  //结果{123, “abc”, 456}
m.push(12);  //结果{123, “abc”, 456, 12}
m.pop();  //结果{123, “abc”, 456}
m += {“def”};  //结果{123, “abc”, 456, “def”}
m.del(1..2);  //结果{123, “def”}
m.insert(1, “abc”);  //在索引1成员前插入数据，结果{123, “abc”, “def”}
m.get(1);  //与m[1]访问结果一样
bool n = m.have(12);  //结果n=false
```

### 2.5 数据集变量的初始化

数据集变量可以通过初始化列表来初始化。类一般通过初始化函数初始化。例：

```
set {u8; char; bool} m1 = {123, “abc”, true};  
u16 a[5] = {.1=3, 5, .4=9}  //“.”+序号，初始化指定成员，其他默认初始化为0，最终{0,3,5,0,9}
set {u8; char; bool} m2 = {.1=”ab”};  //指定初始化第二个元素，第一三默认初始化，最终{0,”ab”,true}
```

### 2.6 数据集方法

除类外的数据集定义关联方法格式：`返值类型 类型名::函数名(参数表) {函数体} `  
例：

```
char u8:: to_char() { char x = (char)self }
u8 u8[]:: average() { u8 ` x =0; for(self..) { x = x+self[]; x=x/self.len }
set U {u8 ` x + i32 ` y};
U x; 
```

## 3. 运算符

类似rust，没有++i，只有i+=1。

| 优先级 | 运算符 | 名称或含义       | 使用形式                  | 结合方向 | 说明       |
| ------ | ------ | ---------------- | ------------------------- | -------- | ---------- |
| 1      | []     | 数组下标         | 数组名[常量表达式]        | 左到右   | --         |
|        | ()     | 圆括号           | (表达式)/函数名(形参表)   | --       |            |
|        | .      | 成员选择（对象） | 对象.成员名               | --       |            |
|        |        |                  |                           |          |            |
| 2      | -      | 负号运算符       | -表达式                   | 右到左   | 单目运算符 |
|        | ~      | 按位取反运算符   | ~表达式                   |          |            |
|        | &      | 取地址运算符     | &变量名                   |          |            |
|        | !      | 逻辑非运算符     | !表达式                   |          |            |
|        | (类型) | 强制类型转换     | (数据类型)表达式          | --       |            |
|        |        |                  |                           |          |            |
| 3      | /      | 除               | 表达式/表达式             | 左到右   | 双目运算符 |
|        | *      | 乘               | 表达式*表达式             |          |            |
|        | %      | 余数（取模）     | 整型表达式%整型表达式     |          |            |
| 4      | +      | 加               | 表达式+表达式             |          |            |
|        | -      | 减               | 表达式-表达式             |          |            |
| 5      | <<     | 左移             | 变量<<表达式              |          |            |
|        | >>     | 右移             | 变量>>表达式              |          |            |
|        |        |                  |                           |          |            |
| 6      | >      | 大于             | 表达式>表达式             | 左到右   | 双目运算符 |
|        | >=     | 大于等于         | 表达式>=表达式            |          |            |
|        | <      | 小于             | 表达式<表达式             |          |            |
|        | <=     | 小于等于         | 表达式<=表达式            |          |            |
| 7      | ==     | 等于             | 表达式==表达式            |          |            |
|        | ！=    | 不等于           | 表达式!= 表达式           |          |            |
|        |        |                  |                           |          |            |
| 8      | &      | 按位与           | 表达式&表达式             | 左到右   | 双目运算符 |
| 9      | ^      | 按位异或         | 表达式^表达式             |          |            |
| 10     | \|     | 按位或           | 表达式\|表达式            |          |            |
| 11     | &&     | 逻辑与           | 表达式&&表达式            |          |            |
| 12     | \|\|   | 逻辑或           | 表达式\|\|表达式          |          |            |
|        |        |                  |                           |          |            |
| 13     | ?:     | 条件运算符       | 表达式1? 表达式2, 表达式3 | 右到左   | 三目运算符 |
|        |        |                  |                           |          |            |
| 14     | =      | 赋值运算符       | 变量=表达式               | 右到左   | --         |
|        | /=     | 除后赋值         | 变量/=表达式              | --       |            |
|        | *=     | 乘后赋值         | 变量*=表达式              | --       |            |
|        | %=     | 取模后赋值       | 变量%=表达式              | --       |            |
|        | +=     | 加后赋值         | 变量+=表达式              | --       |            |
|        | -=     | 减后赋值         | 变量-=表达式              | --       |            |
|        | <<=    | 左移后赋值       | 变量<<=表达式             | --       |            |
|        | >>=    | 右移后赋值       | 变量>>=表达式             | --       |            |
|        | &=     | 按位与后赋值     | 变量&=表达式              | --       |            |
|        | ^=     | 按位异或后赋值   | 变量^=表达式              | --       |            |
|        | \|=    | 按位或后赋值     | 变量\|=表达式             | --       |            |
|        |        |                  |                           |          |            |
| 15     | ，     | 逗号运算符       | 表达式,表达式,…           | 左到右   | --         |

## 4. 函数

C+函数的定义类似C++：`返值类型 函数名(形参1，形参2，...) {}`  
函数形参声明需说明类型及是否可变，无需说明是否在堆上还是栈上分配。参数表()空代表无参数。

### 4.1 函数返值

返值类型处空代表无返值。“^;”语句在函数体内表示强制返回。“!”用在返值类型处代表不返回，不返回函数执行前的状态，一直停留在函数中。例：

```
u8 add(u8 x, u8y) { u8 z; z=x+y; z}  //函数最后一个表达式的值就是返值
add(u8 x, u8 y, &u8 z) { z=x+y;}  //无返值
add(u8 x, u8y) {u8 z; z=x+y; ^; z }  //无返值，最后一个语句强制返回，后面的表达式未能执行
! add(u8 x, u8 y, &u8 z) { z=x+y;}  //不返回
```

### 4.2 回调函数

通过将函数形参设为函数引用来实现函数间的相互调用，如回调函数。  
格式：`函数名(..., 返值类型 函数形参名(无名参数表) , ...){}`  
回调函数机制类似接口，通过中间函数的函数引用形参调用不同的函数，实现不同的功能。例：

```
u8 add(u8 a, u8 b) { a+b}
u8 minus(u8 a, u8 b) { a-b}
calcu(u8 c, u8 d, u8 ca(u8, u8) ) { u8 x = ca(c, d); x }  //封装了两个方法，后面添加乘法不用改calcu()代码
main() {
  u8 e = 5;
  u8 f = 8;
  u8 result = cacu(e, f, add);  //结果13
  result = cacu(e, f, minus);  //结构-3
}
```

### 4.3 闭包

又称匿名函数，类似C++的lambada表达式，格式：`[捕获选项](参数表){函数体}`  
闭包是内联函数，返值类型编译器自动推导。无参数可省略“()”。

- 捕获选项
  闭包可捕获上下文中的环境变量，即可在闭包内使用闭包外的变量。捕获方式有两种：按值、按引用。前者将环境变量的值拷贝进闭包使用，后者使用引用绑定环境变量在闭包内使用。  
    [=]  表示所有环境变量按值捕获；  
    [&]  表示所有引用变量按引用捕获；  
    [var]  表示某变量按值捕获，其他按引用；  
    [&var]  表示某变量按引用捕获，其他按值；  
    [var1, var2]  表示变量1，变量2按值捕获，其他按引用；  
    [&var1, &var2]  表示变量1，变量2按引用捕获，其他按值；  
    [&self]  表示类中定义的闭包按引用捕获外部类；  
  如捕获方式未说明，编译器会根据变量的类型推导捕获方式，简单类型按值，复杂类型按引用。在闭包内使用的外部变量，代码编辑器要突出显示，以提醒编程人员避免误使用。

- 闭包作为函数实参
  闭包作为简单的回调函数使用比较方便，前述回调函数例子可改写如下，简单不少：

  ```
  calcu( u8 ca() ) { u8 x = ca(); x}
  main() {
    u8 e = 5;
    u8 f = 8;
    u8 result = cacu(  []{e+f}  );  //结果13
    result = cacu(  []{e-f}  );  //结构-3
  }
  ```

### 4.4 运算符函数

C+使用运算符函数实现运算符重载。运算符函数是内联函数，格式：`运算符.(参数表){函数体}`  
运算符函数参数有两个或三个，其中一个为空，代表运算符位置，其他为同一类型，强制引用类型。参数按运算展开顺序排列。例：

```
set Complex { u8 r; u8 i; ...};
Complex +!(Complex &a, ,Complex &b) { Complex c; c.r = a.r + b.r; c.i = a.i + b.i; c }
main(){
  Complex c = {1, 2};
  Complex d = {3, 4};
  Complex e = c+d;  //编译器无法执行Complex对象“+”运算，自动搜索找到对应运算符+!函数，
}                   //按参数表顺序将&c→a、&d→b
```

不能重载的运算符有？

## 5. 控制流

### 5.1 if else语句

与c语言相同。


### 5.2 while 和 do while语句

与c语言相同。

### 5.3 for语句

- 格式1：`for(循环次数) {循环语句}`

- 格式2：`for(计数序列1,计数步进1,循环条件1;计数序列2,计数步进2,循环条件2) {循环语句}`  
  序列步进和循环条件可省略其一或全部。默认步进1。序列可以有几个，可多层嵌套循环。序列默认对应循环变量从i起，依次j，k..。

例：

```
u16 ` x[10];
for(0..9) x[i] = i;  //索引变量i从0到9，每次循环步进1；x[i]可以省略成x[]
u16 ` y[7][8];
for(0..6,1 ; 7..0,-2) y[i][j] = i+j;  //双层嵌套循环，在左序列是内层循环
{u8; u16; u32; u8; u16; u32; u8; u16; u32;} n;
for(n.., n[] != 3) n[] +=2;  //省略步进，默认1
for(n..) n[] += 2;  //省略步进及条件，默认1
for(1..3, 1; 2..8, 2) x[i] = y[j] +2;  //嵌套循环，内层x[1]~x[3]，步进1；外层y[2]~y[8]，步进2;
```

###　5.4 跳转语句go

go跳出当前语句块，相当C语言里的continue和break。go 1跳出1层循环，go 2跳出2层循环，以此类推。go 0跳出所有层循环。

### 5.5 多分支语句?

格式：` ?(表达式) {条件开关1: 处理1; 条件开关2: 处理2; _: 共用处理; *: 默认处理;}` 

先计算括号中的表达式的值，然后与条件开关去匹配，符合就执行该分支处理，执行完再执行_:分支的共用处理(可以没有)，然后退出?表达式。如条件开关都不匹配，就执行*:分支的默认处理，可以没有，即不做任何处理。但编译器会发出警告：没有执行穷尽检查。  
条件开关可以有几个并列的条件，通过“,”分割，但其中要有对应括号中表达式的值，除非括号中表达式为空。表达式为空，作用类似if else语句。  
?语句不加“;”作为右值表达式使用，各分支必须返回与括号表达式值同类型的值，否则编译不通过。如括号中表达式为空，各分支可以返回不同类型的值。如括号中表达式的值类型为逻辑值，?语句可简化为：  
`?(逻辑表达式) 处理1,处理2;`
例：

```
{u8, u8} x = 0;
?(x) {
  {0,_}, {1,_}:  print(“第一位为0 or 1”);  //第一位为0或1，执行打印...
  {2..5,_} :  print(“2~5”);  //第一位如是2~5打印...
  {7, <10&&>8} :  print(“第一位为7,第二位为9”);  //
  _:  print(“选中”);  //每个匹配后的分支执行后再执行此分支，可省略
  *:  print(“没选中”);  //前面没有匹配后的分支执行此分支
}
f32 y = 0;
?() { y <= 0 : print(“y≤0”); (y>0)&&(y<=10) : print(“0<y≤10”); *: print(“y>0”);}
u8 z = ?(x>y) x,y;
```

## 6. 所有权和引用域

类似Rust，C+引入所有权和作用域来避免类似指针引发的内存泄露问题。

### 6.1 所有权。

规则如下：  

- 每个值都有一个所有者的变量；
- 值只能有一个所有者；
- 当所有者（变量）离开作用域后，值将被丢弃，内存被收回；
- 赋值操作时，包括参数传值、函数返值，提供值的复杂类型变量的值所有权会被移动给被赋值的变量，前者被系统无效。简单类型变量的值所有权不变，赋值执行内存拷贝。简单类型指标量、位段及成员是标量或字段的元组和联合。复杂类型是简单类型外的类型，主要是类、数组。
- 一个变量的引用并不获得变量的所有权，只获得使用权。

### 6.2 所有域和使用域

被引用的值所有者的作用域简称所有域，引用的作用域简称使用域。如被所有域小于使用域，就会出现悬垂引用的问题。编译器会检查被引用对象和引用的作用域，规则如下：

- 使用域必须被包含在对应的所有域内，否则编译不通过；
- 函数返值是引用且不同条件下来自函数不同的参数时，获得函数返值的变量的使用域必须被这些参数引用的对象的所有域中最小的一个包含。编译器会在函数调用时确认传入引用给这些参数的对象的所有域大小。

 例：

```
&char longest(&char str1[], &char str2[], &char str3[]) {
  if(str1.len>str2.len) {str1} 
  else {str2}
}
main() {
  char x = “abcde”;
  &char z;
  {
    char y = “abc”;
    z= longest(&x, &y);
  }
  print(“The longest is {}”, z);  //longest函数返值来自str1和str2，函数调用时传入引用的对象中y的所有域最小
}                                  //编译器检查z的使用域大于y的所有域，编译不通过。实际z获得的是x的引用
```



## 7. 数据处理方法及面向对象编程

包括数据集和标量，数据类型可定义关联数据处理方法。将数据和关联方法作为一个整体就是对象，以对象为中心的编程方式就是面向对象编程。C+面向对象编程主要特征：数据和方法封装、继承、多态、接口类。

### 7.1 数据和方法封装

C+文件分.cpi接口文件和.cp源文件两种。接口文件包含模块外可使用的变量、函数、数据集类型声明。可使用指可使用对象、生成对象或继承。源文件一般包含他们的具体实现，也可包含声明。声明放在源文件里的，模块外不可使用。数据集变量如需要模块外可使用，类型和变量声明都要放到接口文件中。  
接口文件里申明的类的成员的访问控制按如下：可公开访问的类成员模块外可访问；限制访问的类成员模块外不可访问模块内可访问；不可访问的类成员类外不可访问。类一般不在源文件中声明？(模块里是否只有类和函数？)  
上述类成员访问权限设置的目的：

- 可公开访问成员  
  类对外的接口，定义后最好不要变化。
- 可限制访问成员  
  用于模块外类的继承和模块内类之间的数据共享。后者增加了耦合性，模块内范围不大，增加的耦合性可控。一般也不变化。尽量通过将成员函数设置成限制访问，成员变量为不可访问，来实现数据共享。实在需要访问成员变量，可设过滤器以过滤访问，保证数据共享受控。过滤器是C+一大特点。
- 不可访问成员  
  一些成员变量和函数属于内部实现细节，可能会变化，不适合对外共享。

例：

```
//接口文件M.cpi里
u8 x;  //全局变量，模块M外可访问
set A { u8 ` aa ; u16 getbb(); | u16 ` index; u16 bb; | u16 calcu(); }  //类A模块M外可使用，可建立对象
                                            //A对象成员aa、getbb()模块外可访问，其他不可
                                            //index、bb模块内可访问，calcu()类不可访问

//源文件M.cp里
u8 y;  //模块外不可访问的全局变量
A::{  //定义内联实现
  u8 ` aa: { if(0<$aa<5) {$aa} else print(“warning...”); } ;  //aa的过滤器实现，控制对aa的赋值
  u16 getbb() {...}  //getbb内联实现。如代码量大，编译器会自动将内联函数转成外联函数
}  
u16 A::calcu() {...}  //代码量较大的calcu方法外联
```

### 7.2 继承

可以在一个多个类的基础上派生新的类，基础的类称为基类，派生的类称为派生类，派生类继承基类。与C++类似。有的编程语言称为父类和子类。派生类继承基类的数据成员和方法成员。派生类可以重新定义与基类方法成员名称及参数接口相同的方法，称为覆盖。派生类不可定义与基类同名的数据成员。  
类的继承语法如下，继承顺序从左到右排列：`set 派生类名:基类名1,基类名2,... {...}`

- 派生类成员的访问控制。  
  派生类从基类继承的成员的访问控制与基类相同。但这里的模块内外是否可访问指的是派生类所在的模块，不是基类所在模块。派生类不能访问基类的不可访问成员，避免过多耦合。

- 派生类的初始化  
  语法格式：`类名 类变量名 = {派生类新成员初始化表}, 基类1名{基类1初始化表}, 基类2名{基类2初始化表}, ... ;`  
  派生类初始化表是基类初始化表加上派生类新成员初始化表，一般按继承顺序排列，编译器按反顺序初始化。

  初始化表中数据要按初始化格式一一对应要初始化的成员变量。初始化格式在类声明时定义，在源文件里定义对应初始化格式的初始化代码，按顺序对应。编译器先按初始化格式用初始化表对相应成员变量初始化，然后执行相应初始化代码。类可以有多个初始化格式。语法如下：  
  `set 类名 {成员...} : {初始化格式1}, {初始化格式2}, ...;`  
  `类名: {初始化代码1}, {初始化代码2}, ...;`  
  初始化格式可省略，默认初始化格式按类的所有成员变量声明顺序一一对应初始化数据。初始化代码可为空，如排在初始化代码列表最后，可省略。  
  类类型变量如是全局变量可以默认初始化，数据成员按全局变量默认初始化规则。注意类类型全局变量不得含引用类型数据成员。类类型变量如是局部变量必须显示初始化，可先声明后初始化。  
  例：

  ```
  //接口文件里
  set Student {char name[5]; u8 age; || u8 month;} : {name, age, month}, {name, age}, {name} ; 
  //源文件里
  Student: {}, { month=12*age; } ;  //初始化代码块为空且排在最后的可省略
  Student stu1 = {“Tom”, 10, 120};  //按格式1初始化
  Student stu2 = {“Tom”, 12};  //按格式2初始化
  ```

- 多继承的内存表现  
  无继承的单个类的成员变量按声明顺序存储。派生类本身新增成员变量存储在先，继承的基类成员变量按继承顺序存储。重复继承的，只保留一份基类。如下菱形继承：

  ```
  set A {u8 a;};  set B:A {u8 b;};
  set C:A {u8 c;};
  set D:C,B {u8 d; u8 dd;};
  ```

  D对象的内存表现相当{u8 d; u8 dd; u8 c; u8 b; u8 a;}

- 覆盖、隐藏、引用赋值   

  派生类可以重新定义与基类成员函数名称及参数接口相同的方法，称为覆盖。  
  派生类调用同名同参成员函数时，编译器先检查派生类有无覆盖基类函数，如有调用派生类自己的，如没有按继承顺序往上层基类找，用基类的函数。同名成员函数在动态绑定情况下，系统会根据赋给多态函数的类类型，调用相应的函数。  
  与C++不同，派生类与基类同名不同参的成员函数不会隐藏基类对应函数。  
  派生类不可定义与基类同名的成员变量。多继承的同级基类中有同名成员的情况下，先被继承的类的成员隐藏其他同名成员。如需要访问其他同名成员，需用作用域::显示访问。  
  派生类类型的引用可以赋给基类类型的引用。编译器自动将基类引用指向派生类中从基类继承的那部分成员变量的内存空间起始地址。

- 继承和嵌套区别  
  类的成员可以是另外一个类类型的变量，称为嵌套，此成员称为嵌套类。嵌套与继承对应不同的现实类型抽象。嵌套对应“有”，继承对应“是”。“有”的含义，“有狗的房子”；“是”的含义：“别墅是房子”。  
  C+的嵌套类可某种程度上看作基类。在遇到类的继承实现比较复杂，如菱形继承，可以通过类的嵌套来实现。在没有歧义的情况下，外部类可以把嵌套类的成员当作自己的成员直接访问。外部类类型的引用也可以赋给嵌套类类型的引用。这样就可以动态绑定。  
  嵌套类成员与类的普通成员变量一样按声明的先后顺序在内存中存储。嵌套类成员在普通成员变量后声明。这样，存储顺序就与类继承的存储顺序一致。有嵌套类成员的类初始化语法如下：  
  `类名 类变量名 = {成员1数据, 成员2数据,..., {嵌套类成员1数据}, {嵌套类成员2数据}, ...}`  
  例：

  ```
  set Dog {char color[10]; || char name[10];} ;  //接口文件里定义，默认初始化格式
  set House {char addr[20]; | Dog dog1;} ;  //接口文件里定义，默认初始化格式
  House house1 = { “1#A street, B city”, {“yellow”, “little yellow”} };
  house1.color = “blue”;  //即house1.dog1.color = “blue”
  ```

### 7.3 多态

C+通过泛型、多态函数和虚类来实现多态。与C++不同，C+基类作者不用在设计基类时就预先考虑后面的多态可能，如哪些成员函数要设为虚函数。C+没有虚函数，多态是基类使用者要考虑的。使用者在要实现多态功能的函数中引入虚类类型参数来实现多态，这种设计模式称为“用时多态设计”。含虚类参数的函数称多态函数。

实现机制类似回调函数：将有多态变化的类引用参数设为虚类引用。编译器在多态函数参数表中隐式增加一个或多个函数引用参数，虚类成员函数调用时使用这些函数引用替换原入口地址。调用多态函数时，实参类引用传入虚类形参的同时编译器隐式将实参类相应成员函数的入口地址传递给前述函数引用，这样实现了多态函数传入不同的类引用调用不同的成员函数的多态功能。这种静态绑定，相比虚函数表方式的动态绑定降低了开销，相对泛型又没有增加代码量。

虚类只能多态函数中定义使用。为了避免菱形继承的复杂问题：

- 虚类不得访问其基类成员变量；
- 有重复继承的派生类引用不得赋给基类引用，虚类除外。

例：

```
set A {
       u8 x; 
       f(){ print(x); }
};  
set B: A {
          u8 y; 
          f(){ print(y); }
};  //B继承A
set C {
       u8 z;  A a; 
       f(){
           a.f(); 
           print(z, a.x);
       }
}; 

test1(&A a){ a.f(); }  //未使用虚类，为普通函数。f()的入口地址是A::f()入口地址
test2(&A a:vt){ a.f(); }  //使用虚类a的多态函数。相当test2(&A a, f1() ) {  f1();  }

main(){
  B b = {2}, {1};
  C c = {4, {3} };
  test1(&b);  //打印1
  test1(&c);  //打印3
  test2(&b);  //打印2。相当test2(&b,B::f)
  test2(&c);  //打印3,4,3
}
```

### 7.4 接口类

没有数据成员只有方法成员的类称为接口类，简称接口。  
接口类相当一种特殊基类，其他类通过类似继承的方式实现其成员函数，这种方式称为实现该接口。可以基于接口类设计多态函数。接口类默认是虚类，不用显示说明。赋给接口类引用的类必须已实现该接口。接口的方法可以有默认实现。接口成员函数需显示使用“&self”作为参数，这样以区别普通成员函数。编译器为普通成员函数隐式插入“&self”参数。例：

```
set Circle {...} ;
set Rectangle {...} ;
set Shape {
  u8 area(&self) {...}; 
  u8 perimeter(&self) {...}; 
};  //定义一个接口类Shape，有两个方法
u8 Circle:Shape:: area(&self){...}  //Circle实现Shape接口。覆盖接口的默认实现
u8 Circle:Shape:: area(&self);  //Circle实现Shape接口。没有函数内容{}块，调用接口的默认实现
u8 cacul_area(&Shape){...}  //多态函数
```

## 8. 泛型

泛型可以实现静态多态。

### 8.1 直接泛型

泛型可用作泛型参数和函数参数，声明语法：`set 泛型名 <类型1,类型2,...>; `     
含义是此泛型可以是尖括号内的几个类型中的任意一个。  
泛型名一般为一个大写字母或大写字母加数字。  
{V}是数值类型泛型，即任意一个数值类型；{S}是标量类型泛型；{T}是任意类型泛型。

### 8.2 泛型类

泛型类，带泛型参数表，声明语法：`set 泛型类名<泛型1, 泛型2,...> {泛型1 变量1; 泛型2 变量2;..}`  
<>内是泛型参数表，与函数参数表类似，泛型参数表与泛型类名间不得有空格。  
泛型参数表至少有一个泛型参数，可有非泛型参数，但必须是编译后的数值类型常量。  
泛型类变量声明时必须将泛型参数固化。结合非泛型参数，可以在类变量声明同时隐式完成初始化。  
泛型类可以继承，语法：`set 派生类名<派生类泛型参数表> :基类1, 基类2, ...  {...}; `  
泛型派生类变量的声明语法如下：  
`派生类名<派生类泛型参数表固化><基类1参数表固化><基类2参数表固化> 变量;`  
泛型参数表固化顺序与继承顺序一致，派生类名与尖括号、尖括号之间没有空格。

### 8.3 泛型函数

有泛型参数的函数称为泛型函数，与普通函数形式相同。  
例：

```
set T1, T2 <Circle, Triangle, Rectangle>;  //T1,T2泛型是A1、A2、A3三个类型中的任意一个
set Stack <{S}, u8 size> { {S} stack[size]; ... }  //定义一个泛型类堆栈，泛型参数表有个非泛型参数
Stack<char, 20> s1;  //生成一个20单位的char栈，声明同时完成初始化
set XStack<T1,T2>: Stack {T1 t1; T1 t2; T2 t3; ...};  //t1和t2类型相同
XStack<Circle, Triangle><u16, 30> xs = {...};  //t1, t2是Circle类型，t3是Triangle类型
```

## 9. 宏

### 9.1 标识宏

set Tuesday 2;

### 9.2 宏函数



```
set vector!((u16 X)*) {<T> _tempV[]; (_tempV.push(X);)* _tempV}
//vector!(1,2,3)宏展开如下
{
<T> _tempV[];
_tempV.push(1);
_tempV.push(2);
_tempV.push(3);  //_tempV.push(X);对应1,2,3分别展开3次
_tempV
}

set find_min(<V>X, (<V> Y)*) {X}, { Min(X, find_min!( (Y)* ) ) }
/*
 假设Min函数有2个数值类型的参数。<V>代表数值类型的泛型。
 find_min(6)展开，因只有一个参数，选择第一个展开项，返回X，即6。
 find_min(4,5,6)展开，因有三个参数，选择第二个展开项，如下
 返回min(4,find_min!(5,6))
              ↓
           返回min(5,find_min!(6))
                        ↓
                     返回6
 */
```

 宏参数有三种类型：  
①变量名、函数名和数据值组成的表达式  
需在宏参数表中声明变量名或函数名所代表的变量及返值的类型，数值的类型。
如上vector!()宏要求参数是u16的

```
u32 X, Y; 
u16 M, N;
vector!(1, 2, X+Y);  //展开后X、Y会对应上文的变量，但类型不同，编译会报错
vector!(1, 2, M+N);  //OK
```

②关键字、类型名及运算符与运算符操作数组成的表达式  
需在宏参数表中声明为ty类型，如：

```
set TY!(ty X) {X Y=0;}  //TY!(u32)展开就是{u32 Y=0;}；
                        //TY!( {u8 a; u16 b;} )展开就是{ {u8 a; u16 b;} Y=0;}
set cacu!(u8 a, ty X, u8 b) { a X b}  //cacu!(3, +, 2)展开就是{3 + 2}
```

③标识符  
文件名、跳转节点名、命名空间路径名等标识符，允许“::”。需在宏参数表中声明为id类型。  
可以定义无参数匿名宏函数!()，供一次性使用函数，×。直接用代码块，表达式。 

### 9.3 方法宏

可用宏定义数据集的方法，使用时展开成本地代码，减少函数调用资源消耗。一般用于比较简单的方法。

### 9.4 运算符宏

+!(&A a, type x, &A b) { A c; c.r = a.r x b.r; c.i = a.i x b.i; c}，使用 g =e +! f  
或者运算符函数+.(&A a, ,&A b) { A c; c.r = a.r + b.r; c.i = a.i + b.i; c}，调用g =e +. f  
宏展开后定义的变量，作用域仅限于宏展开的代码块。因宏展开会将定义中的{}一起展开。



------

The following are not sorted out.

例：

 set A { u8 a; u8 sum() {}; }  

 set B { A a1; u8 b; u8 sum() {}; }  //B类嵌套一个A类成员

 B b1 = {};  //B的一个实例

 b1.a1.sum();  //b1调用a1的sum()方法

 b1.sum();  //b1调用自己的sum()方法

 嵌套类抽象应用举例：有狗的房子或有狗的人。

(2)类的继承

 可以在一个多个类的基础上派生新的类，基础的类称为基类，派生的类称为派生类。与C++类似。有的编程语言称为父类和子类。

 派生集可以重新定义与基集方法成员名称及参数接口相同的方法。

 

 或者在编译期解决？每一个成员函数的调用，都会检查调用者的类型，因多态的存在类型会变，到实际调用者而不是宣称调用者定义的代码段取函数入口地址。

 或者将数据和方法不绑定，数据集+函数集。函数集可匿名嵌入数据集，或者？函数集记录关联的数据集？

 分开就和trait差不多了。Trait的好处就是，和泛型结合，对实现某特性的对象进行方法调用，编译时必然要去确认调用者的数据类型。

 ?方法不可继承，需显示声明使用基类的方法。

 还是要将类的嵌套和继承（派生）分开，以对应不同类型的抽象，“有”“是”。

 嵌套类方法不可继承。派生类可继承父类方法，可多态。

 两者数据成员内存表现一样。

 从抽象的角度，不使用接口（特性）。所有方法集必须有关联的数据集。使用类似的关联泛型的方法集。

 派生类对象可以赋给基类引用，反之不可。

 类的数据成员可见性取决于模块，模块内，类对象数据成员是可见的。基类数据私有成员对派生类不可见。

 通过set A { 

u8 aa:[aa>0, { if($aa==5) _index+=1; } ];  //aa

​    u16 _index; 

}  

或者外置，这样整洁点。

set A { u8 aa ; u16 _index; };  

   A::{u8 aa:[aa>0, { if($aa==5) _index+=1; } ]; }

 

(1)数据集和方法

 ①元组和结构，可以在定义时同时定义关联方法。或者在定义后定义方法。

例：

 set X { u8 a; u8 b; u8 sum(&self) {self.a+self.b} }  //定义了一个结构体类型及关联处理方法sum

 X::{ u8 diff(&self) {self.a-self.b} }  //在结构体定义后定义方法，方法定义在结构体定义外

 X::{ u8 mul(&self) {self.a*self.b} 

u8 div(&self) {self.a / self.b} } 

X test = {3,2};  //声明一个X类型变量test并初始化

 u8 Y = test.sum();  

 set Y { X x; u8 c; u8 sum(&self){self.a+self.b+self.c} }  //X是Y的基集；Y重新定义了sum方法。

 set F1<{T}> { u32 size_of({T} X); u32 len({T} Y); }  //如泛型参数表F1<{T}, {T}>，代表有两个不同的泛型参数 

\------------

?文件名含层级信息”0#模块名.cp”，代表模块位于0层，根层。新增模块，遇到同层有相同模块名的，需要修改新增模块的模块名。例：原有”2#Tiger.cp”，新增模块也想叫”Tiger”，那可改模块名为”Tiger-1”，即”2#Tiger-1.cp”。每个文件首行有层级信息。

以上不太好，文件还是不要放在一个目录下。

代码的组织：一个模块包含一个接口文件和至少一个实现文件，含多个实现文件的模块放到以模块名命名的单独文件夹。接口文件包含模块外部可见的全局变量、类声明、类成员声明、函数声明，以模块名为文件名，文件类型.cpi。实现文件包含具体的实现，文件类型.cp。子模块需在父模块目录下另起一个与子模块同名的文件夹。

Amod

 |—Amod.cpi

 |—Amod.cp

 |—Amod1.cp

 |—Bmod

​    |—Bmod.cpi

​    |—Bmod.cp

​    |—Bmod1.cp

Amod接口文件Amod.cpi内容如下：

\#“Amod.cp”

{}  //Amod.cp的接口定义...

\#”Amod1.cp”

{}  //Amod1.cp的接口定义...

\#”Bmod”

-------------

u8[] :: max() {self}  //对u8类型数组定义方法

set shape {Circle+Triangle+Rectangle; area(){} Perimeter(){} };  //定义一个组合类(类似接口)，有两个接口函数

set pentagon:shape { area(){...} Perimeter(){...} };  //pentagon承接shape，两个接口函数要实现。

 

错误处理

 统计常用错误的种类，如逻辑错误、IO错误。比如用到打开文件函数的时候，编译器自动提醒需要增加找不到文件的错误处理。比如变量的属性是0<x<5，使用此变量对象后编译时编译器提醒要对超出范围做错误处理。

 

多态：静态和动态。静态用泛型。动态用虚函数表，或者在函数里插入类名（不可行），在函数里插入函数表指针。组合类？基类使用时基于速度和内存的考量用关键字指定用哪种多态。

用直接赋值还是用构造函数初始化？有过滤器，直接赋值也可以，但不能在初始化时增加一些语句。

增加初始化过滤器？

保护变量可被子类访问，保护方法不能被子类访问？（父类的方法具体实现可能会变）

 

 

 
