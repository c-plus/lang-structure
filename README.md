# Background #

I became a new programmer because of my interest.  
But programming is too unfriendly to an old fellow.  
So I had a idea to create a new programming language for new programmer.  
It's a crazy way to learn programming.  
I call this programming language C+. Simple and safe. Combines C++ and rust.  
# Lang-structure #

The following contents is in Chinese. I will translate it into English as soon as possible.  
1.变量  
  ①声明及初始化。  
  类似C，数据类型 变量 = 初始化数据。  
  例：  
    u8 X = 1;  
  不同C，变量使用前需初始化，可以先声明后初始化。声明同时初始化可省略数据类型，C+自动推断。  
  例：   
    X = 1;  
  ②变量区分可变和不可变。默认不可变。可变变量名以单下划线开始。    
  例：  
    `_X = 1;  // _X是可变变量。`  
    变量命名规则参考：_UsaPresident，用大写字母分开单词。  
  ③变量分栈上变量和堆上分配内存变量。  
  使用时分配内存大小未确定，或占用内存较大的变量原则上在堆上分配。  
  在变量后增加“[]”代表堆上分配变量。[]代表分配内存大小未定。  
  例：  
    `
    u8 _X[] = 5;  //在堆上分配可变变量_X； 
    
    u8 _Y[] = {1,2,3};  //在堆上分配可变长度可修改数组_Y[3]；  
    
    u8 _Z[3] = {1,2,3};  //在栈上分配可修改数组_Z[3]，长度不可变；
    `         
      
2.基本类型  
  与rust差不多。  
  ①数值类型   
  分为有符号整数 i8, i16, i32, i64, ibit；  
  无符号整数 u8, u16, u32, u64, ubit；  
  浮点数 (f32, f64)；  
  ②char字符类型：4字节单个Unicode字符，如’A’、’王’。  
  ③bool布尔类型：有两个值true和false。  

3.数据集合及类型  
  数据集合的数据一般放在{}里，用“,”隔开。  
  关键字type定义数据集合类型，成员一般放在{}里，用“;”隔开。  
  数据集合类型变量成员可是符合数据类型，可复合嵌套，比如结构数组，结构里嵌套结构。  
  数据集合类型可定义数据处理方法。  
(1)类型  
  ①字符串：结合C++和Rust的用法。  
  例：  
    `char X = “abc”;  //字符串常量，不可修改。没有’\0’。   
    char _Y[] = “abc’’  //堆上可变长度的字符串   
    char _Z[3] = “abc’’  //栈上的字符数组，相当{‘a’, ’b’, ’c’}   
    u8 _I = UserInput();  //用户输入到变量_I  
    char _M[_I] = “abc’’  //按用户输入数值可变长度的堆上字符串`  
  ②数组：相同类型数据成员集合，同C语言，如上例子。  
  ③元组   
  不同类型数据成员集合，如{123, “abc”, true}。  
  可用type定义元组类型，如  
    `type M {u8; char; bool;};  //定义了一个叫M的元组类型，相当一个成员字段匿名的结构体    
    M N[3];  //声明了一个包含3个M类型的元组的数组    
  例：    
    M = {123, “abc”, true};  //声明并初始化一个元组变量M  
    u8 N = M[0];  //用索引访问元组成员  
    (X, Y, Z) = M;  //声明X,Y,Z三变量并初始化，解构M三个数据成员并模式匹配，并自动推断类型    
                    //相当X=123; Y=”abc”; Z=true; ` 

   ④结构体：有成员（字段）名的元组。  
   使用“.”访问成员。  
   例：  
     `type M {u8 X; char Y; bool Z;}  //定义一个叫M的结构体  
     M Max = {123, “abc”, true}  //声明一个M类型的结构体并初始化  
     u8 S = Max.X;  //通过“.”访问Max的X成员  
     u8 S = Max[0];  //通过索引访问Max的成员，同上结果一样  
     (U, V, W) = Max;  //通过解构Max，声明并初始化U, V, W变量     
     M Min[];  //声明一个堆上的M类型的结构体数组`  

   ⑤枚举  
   类似C语言，一般定义如下，  
     type E {Max^Min};  
   特别的，泛型定义，数据成员为类型，包括基本类型、数据集类型等。如下，  
     type T <u8, u16, u32, u64>;  //T是u8,u16,u32,u64类型里的任意一个类型，类似ubit  

   ⑥联合  
   类似C语言，一般定义如下，  
     type U {u8 X | i32 Y}  //变量X, Y共用一段存储空间  

   ⑦位段  
   C语言定义在结构类型上，这里定义在基本数值类型上，有差别。数值类型变量后加“:”声明位段变量，可以按位段号访问，通过“.”符号。这样的变量称为位段类型变量。  
   位段变量严格意义上不是数据集合类型，属于基本数值类型的附属类型。  
   位段的好处是几个变量共享内存空间，比按位运算简便。  
   需要注意的是，此种方式可能比通过按位运算符运算的效率低。  
   例：  
     `u8 _X:1-3-4 = 5;  //声明并初始化一个位段变量_X，有3个位段，分别对应1,2~4,5~8位。  
     _X.0 = 1;  //第一个位段赋值1，即_X第一位设为1；  
     _X.1 = 0b00000101;  //第二个位段，即2~4位设为101；  
     _X.2 = 0b00001011;  //第二个位段，即5~8位设为1011；  
     u8 _Y: = 6;  //如省略位段说明，默认按每位访问。  
     _Y.1 = 1;  //第二位设为1；  
     _Y.7 = 0;  //第8位设为0。  
     u16 _Z:by = 7;  //按字节访问，有两个位段。  
     _Z.0 = 0b11010101;  //将第1位段，_Z的1~8位分别设为11010101；  
     _Z.1 = 0x9E;  //将第2位段，_Z的9~16位分别设为10011110；`  

(2)数据集合类型变量的成员访问  
  数据集合类型变量可通过索引访问成员，枚举、位段除外，可通过索引访问，类似数组，如：M[1]。  
  数据集合类型变量有复合嵌套的，通过索引访问成员的基本类型成员，枚举、位段除外，  
  如：  
    `type M {u8; char; bool;};  //定义了一个叫M的元组类型，相当一个成员字段匿名的结构体。  
    M N[3] = {...};  //声明了一个包含3个M类型的元组的数组。  
    N[1]是访问第二个成员，N[.1]访问N[0]的char数据成员。`  
