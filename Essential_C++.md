## 《Essential C++》Stanley B. Lippman著 侯捷译

### 初始化语法

```
int a = 0; 或者 int a(0);
前者延续了C语言的语法，后者能初始化具有多个初值的对象。

#include <complex>
complex<double> cpx(0, 7);
<>表示complex是一个模板类(template).可以有 float,double,long double;程序员可以指定complex最终的数据类型.
```

```
'\n':换行符
'\t':制表符
'\0':null
'\'':单引号
'\"':双引号
'\\':反斜杠
'/':正斜杠,不是转义字符
```

```
建议使用vector优先于array;
vector<int>vec(size);        //默认每个元素初始化为0
vector<int>vec(size,value);  //初始化每个元素为value
vector<int>vec({1,2,3});     //也可以通过数组初始化
vector<int>* pv[size] = {,,}
```

### 文件读写

```
#include <fstream>

//写
ofstream outfile("out.txt", ios_base::app);
if(!outfile)  {
    cerr << "Uable to Write Data." << endl;
}
else{
    outfile << "data" << endl; //将"data"定向到outfile
    //操纵符(manipulator)，并不会写入文件，也不会读取数据，其作用是在iostream上执行某些操作,
    //比如有hex(以16进制显示整数),oct,setprecision(n)(设定浮点精度为n)
    //endl会插入一个换行符，并清除输出缓冲区的内容
}
```
```
//读
ifstream infile("in.txt",)
fstream iofile("io.txt",ios_base::in|ios_base::app);
iofile.seekg(0); //定位文件到起始处
```

```
#include <cstdlib>
exit(-1); //结束整个程序
```

### 指针或引用

```
//reference:指针或引用
使用时,差异是，指针可能指向一个不存在的对象(比如NULL),在使用时需要做检查
引用必定会代表某个对象，所以不需要做检查
void func(const vector<int>&vec){vec[1] = 3;}; //const说明vec只读,引用避免了拷贝
void func(const vector<int>*vec){(*vec)[1] = 3;}; //与上面引用的效果完全相同

//local scope 和 file scope 都由系统自动管理
//动态内存管理 堆内存 由程序员自行管理
//如果不释放new出来的内存空间,会造成内存泄漏(memory leak)
int* pi = new int;  //不会自动初始化
int* pi = new int(12);
delete pi; //无需检查pi是否为0,编译器会替我们检查

int* pia = new int[10]; //在heap上分配10个int,C++没有提供在分配空间时为其赋初值的功能
delete [] pia;
//总结就一句话:new的一切都有程序员自己控制,包括初始化,释放啥的
```

### 函数默认参数值

```
void func(const vector<int>&vec, ofstream* ofil=NULL){} //引用是无法设置为NULL,因为引用的对象是一定得存在的,可以写成指针形式
void func(const vector<int>&vec, ostream &os=cout){};
//1. 函数参数的解析顺序为从右到左
//2. 默认值只能够指定一次,可以在函数的声明处,也可以在函数的定义处(不能都指定)
//建议:在声明处,一般位于头文件,只编译一次,可带来更高的可见性
```

```
//局部静态对象:在函数体内声明static,生存周期延长到整个程序结束
cerr << "error" << endl; //与cout的区别在于没有缓冲区,直接打印到屏幕上
```

### inline

只是对编译器的一个建议请求,不代表必须执行
```
//inline只适合涵数体内代码简单的涵数使用，不能包含复杂的结构控制语句例如while、switch
//并且内联函数本身不能是直接递归函数（即，自己内部还调用自己的函数）
//inline放在函数定义前才有作用,只放在声明前没有作用。内联函数不能增强性能,就避免使用它
//如果函数体比较大且在很多地方都被调用了,那么就会使执行体的大小变大。
class A
{  //不推荐写法
    public:void Foo(int x, int y) {  } //定义在类中的成员函数,自动成为内联函数
}
```
inline:在每个调用点上，编译器都得取得其定义，inline函数的定义必须放在头文件中。

### inline推荐写法

```
// 头文件(就只包含声明)
class A
{
    public:
    void Foo(int x, int y);
}

// 定义文件
inline void A::Foo(int x, int y){}
```

### 函数重载
具有不同参数(不同类型或者不同数量)的两个或多个函数可以具有相同的函数名。
但返回值无法作为函数重载的依据，因为其在语义上可能不完整，比如调用时可以不写返回值，就无法区分了。

### 函数模板（关键字typename）
：代码主体不变，仅仅就改变其中的数据类型
function template的参数列表一般：有明确的类型、暂缓决定的类型。

//function template的同时也可以重载函数
```
template<typename T>
void func(const string& msg, const vector<T>& vec);

template<typename T>
void func(const string& msg, const list<T>& vec);
```

### 函数指针
：将具有相同参数列表以及返回值的不同的函数统一接口

```
bool func(int pos, int& elem, const vector<int>* (*seq_ptr)(int))
{
    const vector<int>* pseq = seq_ptr(pos);
    if(pseq) { ... }
    ....
}

vector<int>*：表示返回值为一个vector指针

//如何获取函数地址呢？
const vector<int>* (*seq_ptr)(int) = some_func; //直接给函数名就可以

//函数指针数组
const vector<int>* (*ptr_array[])(int) = {
    func1, func2, func3
};
：但需要注意的是func1~func3的参数以及返回值都与指针定义相同
```

```
template<typename T>  //类似于定义类型，具有多个不同的话：template<typename T1, typename T2>
int a(T ss, T cc)
{
        cout << ss << cc << endl;
        return 999;
}

template<typename T>
int func(const string& s, int(*func_ptr)(T, T))
{
        cout << s << " " << func_ptr(888, 33) << endl;
        return 0;
}

func("你好呀", &test<int>);
```


一个对象在函数中只能被定义一次，但const Object和inline是个例外。
**inline**:在每个调用点上，编译器都得取得其定义，**inline函数的定义必须放在头文件中**。
const Object:一旦出了文件之外就不可用，我们必须在多个程序代码文件中加以定义，不会导致任何错误。
对于

```
extern const vector<int>* (*seq_ptr[5])(int); //加上extern才能让编译器解读为是声明而非定义。
```

它并不是，只是一个指向const Object的指针。
想要让**const Object**跨文件被访问的**唯一**方法就是使用对象**指针**。

include ""和<>：告诉编译器去哪里找头文件。<>：同一磁盘目录下找；""：不同磁盘目录下找。

#### Standard Template Library(STL)构成的组件：
- 容器：如vector,list,map,set等
- 操作容器的泛型算法：find(),sort(),replace(),merge()

vector,list：顺序容器
map,set:关联容器
泛型算法是通过function template技术，达到与操作类型相互独立。不是直接在容器上操作，而是通过一对iterator(first and last)。

vector与array一样在内存中都是连续存放的。

```
//可以写find函数适用于vector和array的查找

template<typename T>
inline T* begin(const vector<T>& vec) { return vec.empty() ? NULL : &vec[0]; }
template<typename T>
T* find(const T* first, const T* last, const T& value) {
        if (!first || !last) { return NULL; }
        for (; first != last; ++first) {
               if (*first == value) { return first; }
        }
        return NULL;
}
```

但是，对于list，由于其元素之间采用指针方式连结，不再能够直接通过连续递增地址来实现访问，需要增加抽象层。

```
vector<int>::iterator it = vec.begin();
vector<int>::const_iterator it = vec.begin(); //只能读取，不能修改
```

标准库提供的find_if()，能够接受函数指针或function object，取代底部元素的equality运算符，大大提升弹性。

#### 所有容器的共通操作：
- equality(==)、inequality(!=)，返回true或false;
- assignment(=)，将某个容器复制给另一个；
- empty()，返回true或false;
- size()，返回包含元素个数;
- clear()，删除所有元素；
- begin()，end()，返回迭代器iterator，第一个元素，最后一个元素的下一个位置；
- insert()，将单个或某个范围内的元素插入到容器； 
- earse()，将单个或某一范围内的元素删除。

**其中**，insert和earse的行为在顺序容器和关联容器上有所不同。

### 使用顺序性容器

```
#include <vector>
#include <list>
#include <deque>
```

- vector以一块连续的内存来存放元素，对其进行随机访问效率很高，但除了删除或插入最后一个元素外，效率都很低，需要复制操作位置之后的元素到新的位置。
- list以双向链表（double-linked）而非连续空间存储内容。

```
list_node{ value; back; front;} //值;指向上一个元素;指向下一个元素
```

- deque(读作deck)：与vector相似，都以连续空间存放元素，但对deque前/末端元素进行删除或插入操作，效率更高。queue是用deque实现的。


### 使用泛型算法

```
#include <algorithm>

_ = max_element(vec.begin(), vec.end()); //在给定范围内寻找最大值
binary_search(vec.begin(), vec.end(), elem); //有序序列，二分查找
copy(vec.begin(), vec.end(), temp.begin()); //将vec中的元素复制到temp中(从begin开始)
```

#### Function Object（效率比函数指针高）

所谓function object是某种class的实例对象,这类class对function call运算符做了重载操作，使function object被当作一般函数来使用。
function object完全可以用独立的函数来实现，主要目的是为了提高效率。

- 6个算术运算：plus<>;negate<>;multiples<>;divides<>;modules<>;
- 6个关系运算：less<type>;greater<>;less_equal<>;greater_equal<>;equal_to<>;not_equal_to<>;
- 3个逻辑运算：logical_and<>;logical_or<>;logical_not<>;


#### binder adapter(绑定适配器)：将function object的参数绑定至某特定值。
```
bind1st(less<int>, val); //把val绑定于less<int>的第一个参数上
bind2nd(less<int>, val); //把val绑定于less<int>的第二个参数上
```

```
template<typename InIteritor, typename OutIteritor, typename ElemType, typename Comp>
void filter(InIteritor first, 
            InIteritor last, 
            OutIteritor at, 
            const ElemType& val, 
            Comp pred)
{
        while ((first = find_if(first, last, bind2nd(pred, val))) != last)
        {
               cout << *first << endl;
               *at++ = *first++;
        }
}
```

not1对一元function object取反；not2对二元function object取反

```
//not1
std::function<bool(int)> less_than_5 = [](int x){ return x <= 5; };
std::count_if(nums.begin(), nums.end(), std::not1(less_than_5));

//not2
std::function<bool(int, int)> ascendingOrder = [](int a, int b) { return a<b; };
std::sort(nums.begin(), nums.end(), std::not2(ascendingOrder));
```

#### map查询某一key是否在map内(3种方式)

```
map<string, int>mp;

(1)
int count = 0;
if (!(count = mp["aaa"])) {} //如果key不存在于map内,这个key会自动被加入map中

(2)
map<string, int>::iterator it;
it = mp.find("aaa");
if (it != mp.end()) { cout << it->second; }

(3)
if (mp.count("aaa")) { }
任何一个key值在map中最多只会有一份，如果需要存储多份相同的key，必须使用multimap。
```

set是由一组key组成的，默认情况下，set都是依据所属类型默认的less-than运算符进行排序。
multiset可以存储多份相同的key。

### Iterator Inserter
copy(),copy_backwards(),remove_copy(),replace_copy(),unique_copy()等等，都与上述filter()极为相似，都必须保证目标容器足够大。
Insertion adapter能够帮助我们避免使用assignment运算符。

```
back_inserter(vec)  //会以容器的push_back()代替assignment
inserter()          //会以容器的insert代替assignment。对于vector而言：inserter(vec, vec.end());
front_inserter()    //会以容器的push_front()代替assignment()，适用于deque和list
```

### iostream iterator

```
#include <iostream>
#include <fstream>
#include <iterator>

ifstream in_file("in.txt");
ofstream ot_file("out.txt");

if (!in_file || !ot_file) {
    cerr << "!! unable to open the necessary files\n";
    return -1;
}
istream_iterator<string> is(in_file);
istream_iterator<string> eof; //不指定istream对象就默认代表end-of-file

vector<string>vec;
copy(is, eof, back_inserter(vec)); //使用iterator inserter防止vec空间不足

sort(vec.begin(), vec.end());
ostream_iterator<string> os(ot_file, " "); //第二个参数表示输出间隔符,可以不写

copy(vec.begin(),vec.end(), os);
```

### 构造函数和析构函数

```
class Triangylar {
publuc:
    //一组重载的constructor
    Triangular();
    Triangular(int len);
    Triangular(int len, int beg_pos);
    //...
};
```

```
Triangular t;
Triangular t2(10,2);
Triangular t3(10);

//这里不会调用赋值运算符，而是带有单一参数的constructor
Triangular t = 8;

Triangular t(); //这种方式错误，会被认为是函数
```

析构函数没有返回值，不带参数，不可能被重载，用来释放在constructor中或对象生命周期中额外分配的资源。

绝大多数情况下，成员逐一初始化会导致错误。

```
{
    Matrix mat2(4, 4);
    {
        Matrix mat2 = mat;
        //这里使用mat2
        //此处,mat2的destructor发生作用
    }
    //这里使用mat
    //此处mat的destructor发生作用
}
//成员逐一初始化，会将mat2的_pmat设为mat的_pmat，导致两个对象同时指向同一个数组,可能导致mat使用时指向已被释放的空间。
//解决方式：提供一份copy constructor
```

### const
确保某些成员函数不会改变其成员变量的值：增加const修饰符
虽然编译器不会对每个函数进行分析，决定他是const还是non-const，但它会检查每个声明为const的member function，看他是否真的没有改变class obj的内容。

```
//.hpp
class Triangular {
public:
        int length() const { return _length; }
        int beg_pos() const { return _beg_pos; }
        int elem(int pos) const;
        
        //non-const member function
        void next(int& val);
private:
        int _length;
        int _beg_pos;
        static vector<int>_elem;
};

//.cpp
int Triangular::elem(int pos) const {
    return _elem[pos - 1];
}
```

有个问题：

```
class Triangular {
public:
    BigClass& val() const { return _val; } //val()虽然不能直接修改_val,但它会返回一个non-const reference指向_val，允许程序在其他地方修改
    //...
private:
    BigClass _val;
};

//！member function 可以根据const与否而重载，
class Triangular {
public:
    const BigClass& val() const { return _val; }  //一个为const版本
    BigClass& val() { return _val; }              //一个为non-const版本
    //...
private:
    BigClass _val;
};

//exp
void example(const BigClass* pbc, BigClass& rbc)
{
    pbc->vla(); //const 对象会调用const member function
    rbc.val();  //non-const对象会调用 non-const member functon
}
```

### mutable
为了保证class object的常量性，需要加上const。但对于某些变量，如_next，改变它的值，从意义上来说，不能视为改变了class object的状态，或者说不算破环了对象的常量性。在变量之前加上关键字mutable，可以告诉编译器：改变_next的值并不破坏class object的常量性。

```
class Triangular {
public:
    //...
    bool next(int& val) const;
    void next_reset() const { _next = _beg_pos - 1; }
private:
    mutable int _next;
    int _beg_pos;
    int _length;
};
```

### this指针
编译器会自动将this指针加到每一个member function的参数列表，在member function内，this指针可以让我们访问其调用者的一切。

```
Triangular& Triangular::copy(const Triangular &rhs)
{
    //先检查两个对象是否相同
    if(this != rhs) {
        _length = rhs._length;
        _beg_pos = rhs._beg_pos;
        _next = rhs._next;
    }
    return *this;
}
```

### 静态类成员(独立于对象)
**static data member**用来表示唯一的、可共享的member,它可以在同一类中的所有对象中被访问，有点像全局对象，唯一的区别是其名称前必须加上class scope运算符。如同访问一般的数据成员。
静态成员函数：和类对应的任何对象都没有关系，调用时只需加上class scope即可。如：Triangular::is_elem(8);
只需在声明中加上static关键字即可，无需在主体外部定义时重复加上static。

打造一个Iterator Class
所谓运算符就是能够直接作用于class object。
在这里Iterator Class只需维护一个index的索引。

```
class Triangular_iterator {public:    //...    Triangular_iterator(int index) :_index(index - 1) {}    bool operator==(const Triangular_iterator&) const;    bool operator!=(const Triangular_iterator&) const;    int operator*() const;    Triangular_iterator& operator++();  //前置(prefix)版  ++obj    Triangular_iterator operator++(int);//后置(postfix)版 obj++;private:    void check_integrity() const; //确保_index不大于max_elems,并确保_elems储存了必要的元素    int _index;};
```

```
inline void Triangular_iterator::check_integrity() const
{
    if(_index >= Triangular::max_elems) {
        throw iterator_overflow();
    }
    //必要时对vector进行扩展
    if(_index >= Triangular::_elems.size()) {    //这里Triangular_iterator类可以直接访问Triangular类，需要friend关键字声明或者说Triangular中写成静态成员函数的形式(友谊不再必要)
        Triangular::gen_elements(_index + 1);
    }
}
```

```
//++前置版本
inline Triangular_iterator& Triangular_iterator::operator++()
{
    ++_index;
    check_integrity();
    return *this;
}

//++后置版本
inline Triangular_iterator Triangular_iterator::operator++(int)
{
    Triangular_iterator tmp = *this; //暂存修改之前的值
    ++_index;
    check_integrity();
    return tmp;
}
```

编译器会自动为后置版产生一个int参数(其值必为0)。


### 调用Triangular_iterator

```
class Triangular {
        typedef Triangular_iterator iterator;

public:
        Triangular_iterator begin() const {
               return Triangular_iterator(_beg_pos);
        }
        Triangular_iterator end() const {
               return Triangular_iterator(_beg_pos + _length);
        }
        //...
private:
        int _beg_pos;
        int _length;
        //...
};

//调用举例
Triangular::iterator it = v.begin();
```

### friend关键字(在不破坏类的隐秘性的前提下，可以访问成员函数，提高效率)
友元不是类的成员，不受类的声明区域public、private和protected的影响。

```
//在类中主动声明为友元函数,则对方就能直接访问本类了
class Triangular {
        friend int Triangulat_iterator::operator*();
        friend void Triangulat_iterator::check_integrity();
        //...
public:
};
```

```
class A {
public:
        A(int val) :a(val) {}
        void func() {
               cout << a << endl; //10
        }
private:
        friend void fun1(const A& res);
private:
        int a; //10
};
void fun1(const A& res) {
        cout << res.a << endl; //10  可以访问A类的私有成员对象
}
```

### 实现一个copy assignment operator
对于未申请额外空间的类，可以直接采用如 a = b(默认成员逐一复制的方式)，但如果存在额外开辟的类(直接的指针复制)，会导致一个对象被destructor，而另一个对象访问错误的问题。

```
Matrix& Matrix::operator=(const Matrix& rhs)
{
        if (this != rhs)
        {
               _row = rhs._row; _col = rhs._col;
               int elem_cnt = _ros * _col;
               delete[] _pmat;
               _pmat = new double[elem_cnt];  //开辟新的空间
               for (int ix = 0; ix < elem_cnt; ++ix) {
                       _pmat[ix] = rhs._pmat[ix];
               }
        }
        return *this;
}
```


### 实现一个function object
是一种“提供有function call运算符”的class。
function call运算符可以接受任意个数的参数：0，1，或更多。


```
//.hpp
class LessThan {
public:
        LessThan(int val) :_val(val) {}
        int comp_val() const { return _val; }    //读取基值
        void comp_val(int nval) { _val = nval; } //写入基值
        bool operator()(int _value) const;
private:
        int _val;
};

//.cpp
inline bool LessThan::operator()(int _value) const {
        return _value < _val;
}
```

```
LessThan lt(10);
bool x = lt(5);  //it会被编译器写成lt.operator(5);

//当作参数传给泛型算法
vector<int>::const_iterator it1 = vec.begin();
vector<int>::const_iterator it2 = vec.end();
while ((it1 = find_if(it1, it2, lt)) != it2) {
        cout << *it1 << ' ';
        ++it1;
}
```

### 重载iostream运算符

```
//output运算符
ostream& operator<<(ostream& os, const Triangular& rhs) {
        os << rhs.beg_pos() << "," >> rhs.length();
        rhs.display(rhs.length(), rhs.beg_pos(), os);
        return os;
}

//input运算符
istream& operator>>(istream& is, Triangular& rhs) {
        char ch1;
        int bp, len;
        is >> bp >> ch1 >> len;  //并没有考虑错误的检验

        rhs.beg_pos(bp);
        rhs.length(len);
        rhs.next_reset();
        return is;
}
```

### 指针，指向Class Member Function
poing to member function与point to function的一个不同点是：前者必须通过同一类的对象加以调用。

```
class num_sequence {
public:
        typedef void (num_sequence::* PtrType)(int);

        void fibonacci(int);
        void pell(int);
        void lucas(int);
        void triangular(int);
        void sequare(int);
        void pentagonal(int);
private:
        PtrType _pmf;
};

//调用
num_sequence::PtrType pm = &num_sequence::fibonacci;

num_sequence ns;
(ns.*pmf)(pos);  //与ns.fibonacci(pos)相同
```

```
vector<vector<int> > seq; //>>之间需要有空格，否则编译不通过。
```

### 面向对象编程风格

最主要的两个特质：**继承**和**多态**。

**继承**：将一群相关的类组织起来，并能够共享其间共通的数据和操作行为。

**多态**：让我们在这些类上进行编程时，可以如同操控单一个体，而非互相独立的类，并赋予我们更多的弹性来加入或者移除任何特定的类。

继承机制定义了父子关系，父类定义了所有子类共通的共有接口以及私有实现。每个子类都可以增加或覆盖继承来的东西。
在C++中，父类被称为基类，子类为派生类。
抽像基类:并不存在的实体，逻辑上的概念，可以在不改变程序的前提下，方便地加入或移除任何一个派生类。

**静态绑定**:调用函数在程序执行之前就被确定。

**动态绑定**:只有当程序执行时，才能根据pointer或reference所指的实际对象(具体的派生类)绑定相应的函数。


### 虚函数(关键字virtual)
虚函数源于c++中的类继承，是多态的一种。在c++中，一个基类的指针或者引用可以指向或者引用派生类的对象。同时，派生类可以重写基类中的成员函数。这里“重写”的要求是函数的特征标（包括参数的数目、类型和顺序）以及返回值都必须与基类中的函数一致。

可以在基类中将被重写的成员函数设置为虚函数，其含义是：当通过基类的指针或者引用调用该成员函数时，将根据指针指向的对象类型确定调用的函数，而非指针的类型。

```
class base {
        int a, b;
public:
        virtual ~base() {}   //基类中的析构函数必须为虚函数，否则会出现对象释放错误
        void test() {
               std::cout << "基类方法" << std::endl;
        }
};

class inheriter :public base {
public:
        void test() {
               std::cout << "派生类方法" << std::endl;
        }
};
```

```
base *p1 = new base;
base *p2 = new inheriter;

cout<<sizeof(base)<<endl;                        //12
cout<<sizeof(inheriter)<<endl;                   //12

//base的test前不加virtual
std::cout << "基类方法" << std::endl;
std::cout << "基类方法" << std::endl;

//base的test前加上virtual ———— 这样就可以将基类和派生类同名方法区分开
std::cout << "基类方法" << std::endl;
std::cout << "派生类方法" << std::endl;
```


- 只需将基类中的成员函数声明为虚函数即可，派生类中重写的virtual函数自动成为虚函数；
- 基类中的析构函数必须为虚函数，否则会出现对象释放错误。以上例说明，如果不将基类的析构函数声明为virtual，那么在调用delete p2;语句时将调用基类的析构函数，而不是应当调用的派生类的析构函数，从而出现对象释放错误的问题。
- 虚函数的使用将导致类对象占用更大的内存空间。对这一点的解释涉及到虚函数调用的原理：编译器给每一个包括虚函数的对象添加了一个隐藏成员：指向虚函数表的指针。虚函数表（virtual function table）包含了虚函数的地址，由所有虚函数对象共享。当派生类重新定义虚函数时，则将该函数的地址添加到虚函数表中。无论一个类对象中定义了多少个虚函数，虚函数指针只有一个。相应地，每个对象在内存中的大小要比没有虚函数时大4个字节（32位主机，不包括虚析构函数）。如下：
- base类中包括了两个整型的成员变量，各占4个字节大小，再加上一个虚函数指针，共计占12个字节；inheriter类继承了base类的两个成员变量以及虚函数表指针，因此大小与基类一致。如果inheriter多重继承自另外一个也包括了虚函数的基类，那么隐藏成员就包括了两个虚函数表指针。重写函数的特征标必须与基类函数一致，否则将覆盖基类函数；重写不同于重载。我对重载的理解是：同一个类，内部的同名函数具有不同的参数列表称为重载；重写则是派生类对基类同名函数的“本地改造”，要求函数特征标完全相同。当然，返回值类型不一定相同（可能会出现返回类型协变的特殊情况）



### 虚基类
在c++中，派生类可以继承多个基类。问题在于：如果这多个基类又是继承自同一个基类时，那么派生类是不是需要多次继承这“同一个基类”中的内容？虚基类可以解决这个问题。

虚基类可以使得从多个类（它们继承自一个类）中派生出的对象只继承一个对象。
当类中存在虚函数时，类就会多出一个隐藏的虚函数指针。

```
//base称为mytest1类的虚基类。
//假设base还是另外一个类mytest2的虚基类，对于多重继承mytest和mytest2的子类mytest3而言，base的部分只继承了一次。

class base   //2个int  8个字节  + 一个虚函数指针4个字节  = 12个字节
{
int a, b;
public:
virtual void test(){ cout<<"基类方法！"<<endl; }
virtual ~base(){};
};

class mytest1 : virtual public base {  //继承了base的12个字节 + 一个指向虚基类指针4字节 = 16字节
};
class mytest2 : virtual public base {
};
class mytest3 : public mytest1, public mytest2 { //继承了base的12个字节 + 2个指向虚基类指针4字节 = 20字节
};

cout<<sizeof(base)<<endl;    //输出12
cout<<sizeof(mytest1)<<endl; //输出16
cout<<sizeof(mytest2)<<endl; //输出16
cout<<sizeof(mytest3)<<endl; //输出20

//对非虚继承而言，是不需要这样的一个指针的。而mytest3类的大小为sizeof(base)+sizeof(mytest1-base)+sizeof(mytest2-base)，即20
```


- 若一个类多重继承自具有同一个基类的派生类时，调用同名成员函数时会出现二义性。为了解决这个问题，可以通过作用域解析运算符澄清，或者在类中进行重新定义； 
- 继承关系可能是非常繁复的。一个类可能多重继承自别的类，而它的父类也可能继承自别的类。当该类从不同的途径继承了两个或者更多的同名函数时，如果没有对类名限定为virtual，将导致二义性。当然，如果使用了虚基类，则不一定会导致二义性。编译器将选择继承路径上“最短”的父类成员函数加以调用。该规则与成员函数的访问控制权限并不矛盾。也就是说，不能因为具有更高调用优先级的成员函数的访问控制权限是"private"，而转而去调用public型的较低优先级的同名成员函数。

### 纯虚函数
若一个类的成员函数被声明为纯虚函数，则意味着该类是ABC(Abstract Base Class，抽象基类)，即只能被继承，而不能用来声明对象。纯虚函数通常需要在类声明的后面加上关键词“=0”。
当然，声明为纯虚函数并不意味着在实现文件中不可对其进行定义，只是意味着不可用抽象基类实现一个具体的对象。

被声明为protected的所有成员都可以被派生类访问，除此之外(派生类)都无法访问protected。

```
class num_sequence {
public:
        virtual ~num_sequence() {};

        int elem(int pos) const;
        std::ostream& print(std::ostream& os = std::cout) const;

        int length() const { return _length; }
        int beg_pos() const { return _beg_pos; }

        static int max_elems() { return _max_elems; }
protected:
        virtual void gen_elems(int pos) const = 0;  //纯虚函数  在派生类中必须有明确定义
        bool check_intergrity(int pos, int size) const;

        num_sequence(int len, int bp, std::vector<int>& re)
               :_length(len), _beg_pos(bp), _relems(re) {}

        int _length;
        int _beg_pos;
        //此处为引用，用来指向派生类中的某个static vector（为什么不用pointer呢？reference永远无法代表空对象，我们可以不需要对其是否为空进行检查）
        std::vector<int>& _relems;  

        const static int _max_elems = 1024;  //static data member 无法被派生类继承
};
```

如果虚函数没有实质意义的话，可以将虚函数赋值为0，让它成为一个纯虚函数。

任何类如果有一个（或多个）纯虚函数，那么由于接口不完整（纯虚函数没有函数定义），程序无法为它产生任何对象，这种类只能作为派生类的子对象使用，前提是这些派生类必须为所有虚函数提供确切的定义。

还记得吗？non-virtual声明的对象在编译时已完成解析(静态绑定)

一般规则，如果一个类中有一个或多个virtual声明的成员，那么其destructor也必须声明为virtual。

基类的public在派生类中是public，基类的protected在派生类中同样是protected。都被视为派生类自己的成员。

#### 在基类和派生类中提供同名的non-virtual函数，并不是什么好的解决办法：

如果派生类中有与基类的member同名，便会遮掩住基类的那份member,也就是说在派生类内对该名称的任何使用，都只会被解析为该派生类自身的那份member。如果要使用，必须在名称前用class scope运算符加以限定。

但是，这样就导致了一个很大的问题：如果使用了基类的那份，说明并不是virtual，这会导致每次调用的都是基类的那一份代码（对于同名函数，只有基类中声明为virtual，派生类才可以重写）。

[基于此点得出结论：基类中的所有函数都应该被声明为virtual，虽然不是什么正确的结论，但却可以马上解决我们两难的困境]

> 派生类所提供的函数，可能并未被用来覆盖基类所提供的同名函数。原因是，函数虽同名，但参数列表、返回类型、常量性（const）可能不同，就不会被覆盖。

```
//派生类重写virtual的条件：
//函数名相同、参数列表相同、返回类型相同、常量性(const声明)、
//virtual不是必须的

class num_sequence {
public:
        //派生类的clone()函数可返回一个指针，指向num_sequence的任一派生类
        virtual num_sequence* clone() = 0;
        //...
};
class Fibonacci :public num_sequence {
public:
        //ok,覆盖了num_sequence基类中的clone()
        //重写基类的virtual函数时，virtual声明不是必须的
        Fibonacci* clone() { return new Fibonacci(*this); }
        //...
};
```

### 虚函数的静态解析
在两种情况下，虚函数机制不会出现预期行为： 
- 基类的constructor和destructor内；
- 使用基类的对象，而非基类对象的pointer或reference时。

在基类的constructor中，派生类的虚函数绝对不会被调用。因为在调用constructor时，派生类中的data member还未被初始化，如果调用派生类的一份虚函数，它便会有可能访问未经初始化的data member。

```
void print(LibMat object, const LibMat* pointer, const LibMat& reference)
{
        //以下必定调用LibMat::print();
        object.print();    //基类对象指定了函数，不会采用虚函数机制来解析

        //以下一定会通过虚函数机制来进行解析
        //我们无法预知那一份print()会被调用(因为是动态绑定的呀)
        pointer->print();
        reference.print();
}
```

### 运行时的类型鉴定机制（Run-Time Type Identification, RTTI）
- typeid
- static_cast
- dynamic_cast

typeid会返回一个type_info对象，其中存储着与类型相关的种种信息。

```
#include <typeinfo>

inline const char* num_sequence::who_am_i()const {
        return typeid(*this).name();
}
```

```
num_sequence* ps = &fib;
//...
if(typeid(*ps) == typeid(Fibonacci))  //以Fibonacci为名字的类
     //ok，ps的确指向某个Fibonacci对象
```

```
//如果我们接下来写。就会造成编译错误
//原因在于ps并不知道它指向的是Fibonacci对象，虽然我们、虚拟机制、typeid知道。
ps->gen_elmes(64);  
```

```
//正确写法 - 1
ps->Fibonacci::gen_elmes(64);
```

```
//正确写法 - 2   采用static_cast运算符进行类型转化, 不安全，需要先用typeid进行类型判断
num_sequence* ps = &fib;
if (typeid(*ps) == typeid(Fibonacci))
{
        Fibonacci* pf = static_cast<Fibonacci*>(ps); //无条件转化
        pf->gen_elems(64);
}
```

```
//正确写法 - 3   采用dynamic_cast运算符进行类型转化
num_sequence* ps = &fib;
if (Fibonacci* pf = dynamic_cast<Fibonacci*>(ps))  //dynamic_cast内部会先进行判断是否为Fibonacci对象，是则返回1，否则返回0
{
        pf->gen_elems(64);
}
```

### 以template进行编程

```
//方式1 建议方式
template<typename valType>
inline BTnode<valType>::BTnode(const valType& val) :_val(val)
{
        _cnt = 1;
        _lchild = _rchild = 0;
}

//方式2  不建议，解释如下：
template<typename valType>
inline BTnode<valType>::BTnode(const valType& val)
{
               _val = val;  

        _cnt = 1;
        _lchild = _rchild = 0;
}
```

**建议**：将所有template类型参数视为class类型来处理，这意味着我们会把它声明为const reference而非by value。
具体解释：constructor函数体内对_val的赋值操作可分解为2步：
(1)函数体执行之前，Matrix的default constructor会先作用于val之上；(2)函数体内会以copy assignment operator将val复制给_val。
但如果采用第一种方式，在成员初始化列表中将_val初始化，只需要一步：以copy constructor将val复制给_val。

**拷贝构造**：传入对象的值生成一个新的对象。
**拷贝赋值**：传入对象的值复制到另一个已经存在的对象。

### template实现常量表达式和默认参数值

```
template<int len, int beg_pos = 1>  //通过模板传入常量或默认参数值
class num_sequence {...};

//参数默认值和一般函数默认值一样，由左向右解析
//调用
num_sequence<16, 0> nq;   or
num_sequence<16> nq;
```

```
//函数地址也可作为常量传递
template<void (*pf)(int pos, const std::vector<int>& seq)>
class num_sequence { ... };

//调用
void fibonacci(int pos, const std::vector<int>& seq);
num_sequence<fibonacci> nq;
```

template无法基于参数列表的不同而重载。

```
//也可以传递function object
template<typename elemType, typename Comp = std::less<elemType>>
class LessThanPred {
public:
        LessThanPred(const elemType& val) :_val(val) {}
        elemType comp_val() const { return _val; }
        void comp_val(const elemType& nval) { _val = nval; }

        bool operator()(elemType _value) const {
               return Comp(val, _val);
        }
private:
        elemType _val;
};

//另一个提供比较功能的function object
class StringLen {
public:
        bool operator()(const std::string& s1, const std::string& s2) {
               return s1.size() < s2.size();
        }
};

//调用
LessThanPred<int> ltpi(1024);
LessThanPred<std::string, StringLen> ltpi("sad");
```

### Member Template Function

```
template<typename OutStream>
class PrintIt {
public:
        PrintIt(OutStream& os) :_os(os) {}
        
        //class内也可以定义member template function
        template<typename elemType>
        void print(const elemType& elem, char delimiter = '\n') {
               _os << elem << delimiter;
        }
private:
        std::ostream _os;
};

//调用
PrintIt<std::ostream> to_std_out(std::cout);
to_std_out.print(427);
to_std_out.print("sad");
```

### 异常处理(exception handling facility)
包括异常的鉴定和发出，以及异常的处理方式。

- throw: 当问题出现时，程序会抛出一个异常。这是通过使用 throw 关键字来完成的。
- catch: 在您想要处理问题的地方，通过异常处理程序捕获异常。catch 关键字用于捕获异常。
- try: try 块中的代码标识将被激活的特定异常。它后面通常跟着一个或多个 catch 块

```
//throw:抛出异常
throw 42;
throw "panic：no buffer!"
throw iterator_overflow();
```

```
//抛出异常try内部的throw()后面程序不会再执行，而try外部后面的程序会继续执行
inline void Triangular_iterator::check_integrity() const
{
        if (_index >= Triangular::max_elems) {
               try {
                       throw iterator_overflow();
               }
               catch (iterator_overflow& e) {
                       log_message(e.what_happened());
               }
        }
        if (_index >= Triangular::_elems.size()) {
               Triangular::gen_elements(_index + 1);
        }
}
```

```
class iterator_overflow {
public:
        iterator_overflow(int index, int max) :_index(index), _max(max) {}
        int index() const { return _index; }
        int max() const { return _max };

        void what_happend(std::ostream& os = std::cerr) {
               os << "error:...." << _index << _max;
        }

private:
        int _index;
        int _max;
};
```

### 捕获异常（catch）

```
//可以自定义异常
#include <exception>

struct MyException : public exception
{
        const char* what_happened() const throw () {
               return "C++ Exception";
        }
};

//调用
//可以指定想要捕捉的异常类型，这是由catch关键字后的括号内的异常声明决定
try {
        throw MyException();
}
catch (const char* msg) {
        std::cout << msg << endl;
}
catch (MyException& e) {
        std::cout << "MyException caught" << std::endl;
        std::cout << e.what_happened() << std::endl;
}
catch (std::exception& e) {
        //其他的错误
}
```

重新抛出异常只需要写下关键字throw即可，它只单独出现于catch子句中；不然会直接调用terminate()，终止整个程序。

```
//捕获任何类型的异常
catch (...) {
        log_message("exception of unknow type");
        //清理 然后退出
}
```

如果“函数调用链”不断被解开，到了main()还是找不到合适的catch子句，便会调用标准库提供的terminate()——其将默认终止整个程序的执行。

如果catch子句接住了异常，程序将接着catch子句后，继续执行。

### 局部资源管理

```
void func() {
        //请求资源
        int* p = new int;
        process(p); //如果出现异常，可能调用了terminate()，会导致p所指不被释放，内存泄露
        delete p;
}

//可以采用try{}catch{}但不是很好的办法。
//写成class的形式，利用destructor自动释放内存
```

**C++规定**：在异常处理机制终结某个函数之前，C++保证，函数中所有局部对象(类)destructor都会被调用。

```
#include <memory>
void func() {
        std::auto_ptr<int> p(new int);

        process(p);  //就算发生异常也不怕
        p->size(); 

        //p的destructor会在此处被悄悄调用
}
```

auto_ptr是将dereference运算符和arrow运算符予以重载，如果iterator class一样，让我们如同使用一般指针一样使用auto_ptr对象。

### 标准异常
```
std::exception          该异常是所有标准 C++ 异常的父类。
std::bad_alloc          该异常可以通过 new 抛出。
std::bad_cast           该异常可以通过 dynamic_cast 抛出。
std::bad_exception      这在处理 C++ 程序中无法预期的异常时非常有用。
std::bad_typeid         该异常可以通过 typeid 抛出。
std::logic_error        理论上可以通过读取代码来检测到的异常。
std::domain_error       当使用了一个无效的数学域时，会抛出该异常。
std::invalid_argument   当使用了无效的参数时，会抛出该异常。
std::length_error       当创建了太长的 std::string 时，会抛出该异常。
std::out_of_range       该异常可以通过方法抛出，例如 std::vector 和 std::bitset<>::operator[]()。
std::runtime_error      理论上不可以通过读取代码来检测到的异常。
std::overflow_error     当发生数学上溢时，会抛出该异常。
std::range_error        当尝试存储超出范围的值时，会抛出该异常。
std::underflow_error    当发生数学下溢时，会抛出该异常。
```

**ostringstream**提供内存内的输出操作，输出到一个string对象上，自动将不同类型的数据格式化为字符串。

```
#include <sstream>

std::ostringstream ex_msg;

ex_msg << "Internal Error: current index " << _index
<< " exceeds maxinmum bound: " << _max;

static std::string msg = ex_msg.str(); //string对象
msg.c_str();                           //const char*
```

### Tips

> 模板函数的声明和定义只能在一起，在头文件中。分开就报错。

原因：一般来说，为了防止重定义，我们会将函数的定义和声明分开。而对于模板，**在实例化模板之前**，编译器对模板的定义体是不处理的，就不会发生重定义的情况。只有定义了实体才会实例化模板函数。

#### <已完结>
