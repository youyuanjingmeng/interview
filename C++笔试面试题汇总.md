1. 堆和栈的区别  
1）申请方式：栈区，分配的内存由编译器自动分配和释放；堆区，由程序员手动分配释放。  
2）申请效率：栈，由系统自动分配，速度较快；堆，一般速度较慢，容易产生内存碎片。  
3）申请大小的限制：栈，是一块`连续`的内存区域，windows下一般是2M，是一种向低地址扩展的数据结构；  
堆，向高地址扩展的数据结构，是`不连续`的内存区域，大小受限于PC机中有效的虚拟地址。  

2. 常见的内存分配方式  
1）静态存储区分配，内存在编译时就确定，如全局变量，static变量。  
2）栈上创建，例如函数内的局部变量，函数执行结束时该存储单元被自动释放。效率高，但是分配的容量有限。  
3）堆上分配（动态内存分配），自己申请自己释放。`malloc`,`free`,`new`，`delete`。  

3. 声明和定义  
声明可以出现在多个地方，描述对象的类型；  
定义只能出现在一个地方，它会为对象分配内存。  

4. 结构体和联合的区别  
1）结构和联合都是由多个不同数据类型成员组成的，但在任何同一时刻，联合中**只存放了一个**被选中的成员（所有成员共用同一块地址空间）,而结构的**所有成员都存在**（不同成员存放地址不同）。  
2）对于联合的不同成员赋值，将会改写其他成员。  
```C
union
{
    int i;
    char x[2];
}a;
int main
{
    a.x[0] = 10;
    a.x[1] = 1;
    printf("%d\n", a.i); /////////266,0x01 0A
}
```

5. new、delete、malloc、free的区别  
new、delete会自动调用对象的构造和析构函数，而malloc和free不会；  
new、delete是运算符，而malloc和free是标准库函数。  
new、delete返回的是某种数据类型指针，malloc、free返回的是void指针。  

6. `static`关键字的作用  
1）函数体内static变量作用域为该函数体，该变量的内存只被分配一次，其值在下次调用时仍然维持上次的值；  
2）模块内static全局变量被模块内所有函数访问，但不能被模块外其他函数访问。  
3）模块内的static函数**只可以**被该模块内的其他函数访问。  
4）类中的static成员变量属于整个类所有，对类的所有对象只有一份拷贝。  
5）类中的static成员函数属于整个类所有，该函数**不接受this指针**，只能访问**类的static成员变量**。  

7. 实现`string`类的相关操作  
```C++
class String
{
    public:
        String(const char *str = NULL);
        String(const String &other);
        ~String(void);
        String &operate = (const String &other);
    private:
        char *m_data;
};

String::String(const char *str)
{
    if (NULL == str)
    {
        m_data = new char[1];
        *m_data = '\0';
    }
    else
    {
        int nLen = strlen(str);
        m_data = new char[nLen + 1];
        strcpy(m_data, str);
    }
}

String::~String(void)
{
    if (NULL != m_data)
    {
        delete []m_data;
        m_data = NULL;
    }
}

/* 拷贝构造函数 */
String::String(const String &other)
{
    int nLen = strlen(other.m_data);
    m_data = new char[nLen + 1];
    strcpy(m_data, other.m_data);
}

/* 赋值函数 */
String &String::operate = (const String &other)
{
    if (this == &other)
    {
        return *this;    
    }

    delete []m_data;
    m_data = NULL;
    int nLen = strlen(other.m_data);
    m_data = new char[nLen + 1];
    strcpy(m_data, other.m_data);
    returh *this;
}
```

8. 实现单例模式  
```C++
//将构造、析构声明为私有
class Singleton
{
    public:
        static Singleton *Instance();
    private:
        Singleton();
        static Singleton *m_pInstance;
};

Singleton *Singleton::m_pInstance = nullptr;

Singleton::Singleton()
{
}

Singleton *Singleton::Instance()
{
    if (nullptr == m_pInstance)
    {
        m_pInstance = new Singleton;
    }
    return m_pInstance;
}
```
9. C++多态
多态：在基类的函数前加上`virtual`关键字，在派生类中重写该函数，运行时将根据对象的实际类型来调用相应的函数。
如果对象类型是派生类，则调用派生类的函数；如果对象类型是基类，则调用基类的函数。  
虚函数肯定是类的成员函数。  
在虚函数后加  = 0，则为纯虚函数。纯虚函数必须在子类实现。  
引入虚函数是为了`动态绑定`，引入纯虚函数是为了派生接口。  
