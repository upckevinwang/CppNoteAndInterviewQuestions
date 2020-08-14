#auto自动类型推断
auto自动类型推断，发生在编译期，不会造成程序运行时效率的降低。    
##auto用法  
1. 用于替代冗长复杂、使用范围专一的变量声明。例如替代迭代器的简略声明。  
2. 在定义模板函数时，用于声明依赖模板参数的变量类型。  
例： <pre class="prettyprit lang-指定样式">
	template <typename Tx,typename Ty>
	void Multiply(Tx x,Ty y){
		auto v=x*y;
	}
</pre>
3. 模板函数依赖于模板参数的返回值。  
例：<pre class="prettyprit lang-指定样式">
	template <typename Tx,typename Ty>
	auto multiply(Tx x,Ty y)->decltype(x*y){
		return x*y;
	}
</pre>
此时模板函数的返回值依赖于模板的参数。  
auto在这里的作用也被称为返回值占位，只为返回值占一个位置，真正的返回值是后面的decltype(x*y)。
##auto注意事项
1. auto变量必须是在定义时初始化，类似于const。  
2. 定义在一个auto序列的变量，必须始终推导成同一类型。  
例：auto a=10,b=20.0,c='abc';//不是同一类型，禁止。  
3. 如果初始化表达式是引用，则去除引用语义。  
例：<pre class="prettyprit lang-指定样式">
	int a=10;
	int &b=a;
	auto c=b;//此时c为int类型，去除了引用。
	auto &d=b;//d的类型为int&，保留了引用。
</pre>  
4. 若初始表达式为const或volatile，则去除const/volatile语义；若想保留，则在auto之前加上相应关键字（const或volatile）。  
5. 若auto带上&号，则保留const语义。  
6. 若初始表达式为数组时：1）auto推导类型为指针；2）auto&推导类型为数组。  
7. 函数或模板参数不能被声明为auto。  
例：<pre class="prettyprit lang-指定样式">
	void func(auto a)//非法
</pre>
8. auto仅是一个占位符，并不是一个真正的类型，不能使用一些以类型为操作数的操作符，如sizeof(auto)或typeid(auto)。  
#decltype的作用
选择并返回操作数的数据类型。  
1. 只是为了推断出表达式的类型，而不用这个表达式的值来初始化对象。  
例：<pre class="prettyprit lang-指定样式">
	decltype(func()){
		sum=x;
	}
	//sum的类型取决于func()函数的返回值类型，但不会实际调用func()函数。
</pre>
2. 与auto不同，会保留关键字const。  
3. decltype和引用
（1）若初始化表达式是引用类型，则decltype的类型也是引用。  
<pre class="prettyprit lang-指定样式">
	const int i=3,&j=i;
	decltype(j) k=5;//k的类型与j已知，为const int&
</pre>
（2）若表达式为引用类型，但需得到引用所指的类型，则可以表示为：
<pre class="prettyprit lang-指定样式">
	decltype(j+0) k=5;//k为int类型。
</pre>  	  
（3）对指针的解引用操作返回的是引用类型。  
<pre class="prettyprit lang-指定样式">
	int i=5;
	int *p=&i;
	decltype(p) b;//b为一个int*指针
	decltype(*p) c=i;//c为int&类型
	decltype(*p) d;//会报错，因为d是一个引用类型，必须初始化。
</pre>
（4）若表达式不是引用，但需要推断出引用，则需要再加上一对括号。
<pre class="prettyprit lang-指定样式">
	int i=3;
	decltype((i)) j=i;//j为int&类型
</pre>    
##auto和decltype的比较
1. auto会忽略顶层const，而decltype会保留。  
2. 对引用操作，auto会推断出原有类型，decltype则会推断出引用。  
3. 对解引用操作，auto推断出原有类型，decltype则会推断出引用。  
4. auto推断会实际执行，decltype不会执行，只做分析。  
#STL的六大组件
1）容器  
包括序列式容器：vector、list、deque；关联式容器：set、map、multiset、multimap。  
容器主要用来存放数据的，从实现的角度来看是一种class template。  
2）算法  
主要有sort()、search()、copy()、erase()、find()等，从实现的角度来看是一种function template。  
任何的一个STL算法在使用时，都需要一对迭代器标示的空间，且区间是左闭右开。  
3）迭代器  
主要用来将容器与算法结合起来，是所谓的“泛型指针”。从实现的角度来看是一种将*、->、++、--等操作重载的类模板，所有的STL容器都有自己的迭代器。  
迭代器与指针:  
迭代器是模板类，从某种程度上讲，它是泛型指针，它提供一种方法顺序访问一个聚合对象中各个元素，但是不会暴露该对象的内部结构。迭代器本质上时封装了原生指针，是指针概念的一种提升，提供了比指针更高级的行为，相当于一种智能指针。  
迭代器返回的是对象引用而不是对象的值，迭代器在使用后就释放了，不能再继续使用，但是指针可以。  
迭代器是指针的抽象泛化，指针是迭代器的一种，指针只能用于特定的容器。  
4）仿函数  
是一种行为类似函数，实际是重载了()的class或class template。  
一般函数指针可视为狭义的仿函数。  
例：sort(arr.begin,arr.end(),cmp);//cmp即为仿函数  
5）空间配置器  
主要用来给容器进行空间的配置和管理，是一个实现了动态分配空间，空间管理，空间释放的类模板。  
6）适配器  
用来修饰容器、仿函数、迭代器接口的东西，主要有容器适配器、迭代器、仿函数适配器。   
例：stack、deque是两种容器适配器，因为它们底层都是用deque实现的。  
六大组件之间的关系：容器通过空间配置器取得数据存储空间；算法利用迭代器向容器存放数据；仿函数协助算法实现不同的策略；适配器可以用来修改容器、迭代器或仿函数接口。  
#set的用法
头文件#include <set\>  
set的特性：所有的元素会根据元素的键值自动排序，与map不同的是set的元素键值就是实值。  
成员函数有：  
1. insert();//插入元素，只能用insert()。  
2. begin(),end(),clear(),empty(),size();//判断set是否为空，尽量使用empty()，尽量不要使用size()==0。  
3. find();//返回的是迭代器。  
4. count();//返回某个元素的个数，因为不允许有相同的键值，所以count()返回的值只有0或1。  
5. erase();//删除某个元素，可以是set中的键值或迭代器，set.erase(key)或者set.erase(iterator)。
#priority_queue优先队列
头文件#include <queue\>  
priority\_queue<int,vector<int\>,greater<int\>>;小根堆  
priority\_queue<int,vector<int\>,less<int\>>;大根堆  
默认情况下是大根堆，当需要存储指针时或结构体时，需要自己重写仿函数。  
成员函数有：  
1. top();//返回堆顶元素。  
2. push();//压入元素。  
3. pop();//弹出栈顶元素，注意没有返回值。  
4. size();//栈中元素的个数。  
5. empty();//判断栈是否为空。  
#explicit关键字
针对类构造函数只有一个参数，禁止隐式自动转换，当无参数或者多于一个参数时无效。  
例：  
<pre class="prettyprit lang-指定样式">
	class CxString{
		public:
			char *text;
			int _size;
			CxString(int size){//单参数构造函数
				......
			}
	};
	CxString string1(24);//合法
	CxString string2=10;//合法，此时会调用转换操作，实际等于CxString string2(10);
	但当构造函数前加上explicit关键字时，该操作便变成非法的，取消了类构造函数的隐式自动转换。
</pre>
禁止单参数构造函数的隐式转换，典型的是拷贝构造函数。  
例：
<pre class="prettyprit lang-指定样式">
	class A{
		private:
			int number;
		public:
			A(int a){number=a;}
	};
	A instance=2;//不会调用赋值运算符，会隐式调用单一参数构造函数。
</pre>
单参数构造函数的调用时机：  
1）用于初始化对象。A instance=2；
2）作为函数参数被调用时 void func(A a){} func(x)
3）作为函数返回值时，仅限于值传递
<pre class="prettyprit lang-指定样式">
	A Get(){
		T x;
		return x;
	}
</pre>
#指针和引用
指针使用操作符*和->，引用使用操作符&和.；  
引用必须指向某项对象，在创建时必须初始化，而指针并不需要（未初始化的指针是合法的，但是危险）。  
不存在指向空值的引用，所以在使用引用时不需要测试它的合法性，因此引用的效率比指针的高。  
例：  
<pre class="prettyprit lang-指定样式">
	string &rs;//非法，引用必须初始化。
	string s("xyzzy");
	string &rs=s;//合法
	string s1("Nancy");
	string s2("Clancy");
	string &rs2=s1;
	rs2=s2;//此时rs仍引用s1，但s1的值现在是“Clancy”。
</pre>
##指针和引用的区别
1. 指针是一个实体，需要分配内存空间；而引用只是变量的别名，不需要分配内存空间。  
2. 存在多级指针，但没有多级引用，只有一级引用。  
3. sizeof(引用)得到的是所指变量的大小；sizeof(指针)得到的是指针本身的大小。   
##考虑使用指针的情况
1. 存在不指向任何对象的可能。  
2. 需要在不同的时刻指向不同的对象。  
##考虑使用引用的情况
一旦指向一个对象并且之后不会改变指向，则应该使用引用，当重载某个操作符时，应该使用引用。  
#转换操作符
##static_cast
不能把struct转换为int，double转换为指针，而且不能去除const属性。int转char时，若没有足够的比特位存放int，则会截断，只保留低8位。  
传统C风格的使用方法：  
<pre class="prettyprit lang-指定样式">
	int firstNumber=5,secondNumber=10;
	double result=((double)firstNumber)/secondNumber;
</pre>  
static_cast的使用方法：  
<pre class="prettyprit lang-指定样式">
	double result=static_cast<double>(firstNumber)/secondNumber;
</pre>
##const_cast
用于去除const或volatile属性，仅能用在影响const或volatile的地方。
<pre class="prettyprit lang-指定样式">
	class Widget{...};
	class SpecialWidget:public Widget{...};
	void Update(SpecialWidget *psw);
	SpecialWidget sw;//sw是一个非const对象
	const SpecialWidget &csw=sw;//csw是sw的引用，是一个const对象。
	Update(&csw);//非法，Update函数不接受一个const对象。
	Update(const_cast<SpecialWidget*>(&csw));//合法，const属性被转换掉。
</pre>
##dynamic_cast
用于把指向基类的指针或引用转换成指向其派生类或兄弟类的指针或引用，而且可以指导转换的结果，若失败则返回空指针或抛出异常（异常针对引用）。主要是为了调用非虚函数，若是虚函数则无需转换就可使用。  
<pre class="prettyprit lang-指定样式">
	Widget *pw;
	Update(dynamic_cast(SpecialWidget*>(pw));//合法，此时传递给Update函数的是一个指向SpecialWidget的指针pw。
	void updateViaRef(SpecialWidget &rsw);
	updateViaRef(dynamic_cast<SpecialWidget&>(*pw));//合法
</pre>
注意不能用来int转double等，也不能用来转换掉const。  
###RTTI（运行时类型检查）
主要体现在dynamic\_cast和typeid，虚函数表索引为0的位置，存放了指向type\_info的指针（本身的type\_info并不存放于虚函数表中），对于存在虚函数的类型，typeid和dynamic\_cast总会去查询type_info。
##reinterpret_cast
最普遍的用途是在函数指针类型之间进行转换，除此之外还可以用于指针与足够大的整数类型进行转换，整数类型（包括枚举类型）到指针类型的转换，无法用来转换掉const。
reinterpret_cast<x>(y);(其中x与y必须有一个值是指针类型)
例：  
<pre class="prettyprit lang-指定样式">
	char A='a';
	char *p=&A;
	cout<< p<< endl;
	//此时，输出a，而不是p的地址，因为运算符重载要打印地址。  
	//只针对char类型的指针，对于其他类型的指针可以正确输出地址。  
	cout<< reinterpret_cast< void*>(p)<< endl;//此时便可以输出p的地址。
</pre>
注意在《More Effective C++》中提到，reinterpret\_cast几乎都是运行期定义（implementation-defined），所以使用reinterpret_cast的代码很难移植。
#C++多态
定义：在类函数之前加上virtual关键字，让子类和父类之间的同名函数有联系，实现动态绑定。  
分类：  
1. 运行时多态：通过继承和虚函数来体现。  
2. 编译时多态：通过函数重载和函数模板。  
有virtual的类会比普通的类大一点，因为在virtual的类对象里面需要一个隐藏的指针（虚指针），用来指针虚表（vTable）。  
虚函数表在编译时确定；虚指针在构造函数之前分配空间，在构造函数运行时，赋值为虚函数表的地址。  
虚函数的代价除了虚指针和虚表，还有一个原因是虚函数无法使用内联函数（inline）。  
只要类声明或继承了虚函数，就会存在虚表，而虚表是属于类的，而不是每个对象单独所有，一个类的所有对象共享虚函数表。  
在Linux/Unix中，虚函数表存放在只读数据段中；在其他系统中存放在常量区。  
<pre class="prettyprit lang-指定样式">
	class Shape{
		public:
			Shape();
			virtual ~Shape();
			virtual void render();
			void move(const pos&);
			virtual void resize();
		protected:
			pos center;
	};
	class Ellipse:public Shape{
		public:
			Ellipse(float majr,float minr);
			virtual void render();
		protected:
			float major_axis;
			float minor_axis;
	};
</pre>
针对上述代码，基类Shape的内存分布为：虚指针（vPointer）和成员变量center。虚函数表为：~Shapw();render();resize();  
派生类Ellipse的内存分布为：虚指针（vPointer）、center、major\_axis、minor\_axis。虚函数表为：   
~Shape();//继承自基类  
render();//派生类自己的  
resize();//继承自父类  
在面向对象语言中，接口的多种不同的实现方式即为多态。  
虚函数表中还会包含RTTI所需要的对象和类的有关信息。  
##virtual使用原则
1. 虚函数不可以是内联函数，因为虚函数只有到运行时才知道要调用的是哪一个函数。  
2. virtual只能修饰类的成员函数，不能用于普通函数。  
3. 虚函数在基类与派生类中必须名字、形态、返回类型相同（有一个例外，当返回值是类本身的指针或引用时）。
4. 静态成员函数不能是虚函数，因为静态函数的特点是不受限于某个对象。  
5. 构造函数也不能是虚函数，因为在构造的时候，对象还是一片未定型的空间，只有构造完成之后，对象才是类的实例。  
6. 析构函数通常是虚函数。 
7. 用override来说明派生类中的虚函数，这样便可以在派生类中检查虚函数是否覆盖基类中的虚函数。若是使用fina修饰，则表示不允许后续的其他函数覆盖该函数。  
<pre class="prettyprit lang-指定样式">
	struct B{
		virtual void f1(int) const;
		virtual void f2();
		void f3();
	};
	struct C:B{
		void f1(int) const override;//合法
		void f2(int) override;//非法，基类中没有f2(int)函数
		void f3() override;//非法，f3不是虚函数，不能用override修饰。
	};
</pre>
###为什么一般将析构函数设计为虚函数
将可能会被继承的父类的析构函数设置为虚函数，可以保证当我们new一个子类，然后使用基类指针指向该子类对象，释放基类指针时可以释放掉子类的空间，防止内存泄漏。  
但是C++默认的析构函数并不是虚函数，因为虚函数需要额外的虚函数表和虚表指针，会占用额外的内存。因此对于不会被继承的类，析构函数不应该是虚函数。只有当需要当作父类时，析构函数才应该设置为虚函数。  
###静态函数和虚函数的区别
静态函数在编译的时候就已经确定运行时机；虚函数在运行时动态绑定，因为有虚函数表和虚表指针，调用的时候会产生额外的开销。  
#pair的使用
头文件#include <utility\>
pair<T1,T2> P;  
直接初始化pair<T1,T2> P(V1,V2);  
访问：P.first，P.second  
pair排序时，默认是升序排序，当first值相等时，再对second升序排序。  
vector<pair<T1,T2>\> p;  
插入元素：p.emplace_back(make\_pair(V1,V2));  
#函数声明的后边加上const（常量成员函数）
只能加在非静态成员函数后边，表示成员函数隐含传入的this指针是const指针，不可修改类成员，可以被const或非const对象调用，但是非const成员函数，只能被非const对象调用。  
非const函数编写时调用const函数，可以有效地避免代码重复，编写时需要进行类型转换。  
例：  
<pre class="prettyprit lang-指定样式">
	class List{
		private:
			Node* pHead;
			int length;
		public:
			int getLength() const;
			bool getNodeInfo() const;
			bool deleteNode();
	};
	const List myList;
	myList.deleteNode();//非法，因为deleteNode函数是非const成员函数，而myList是const对象。
	myList.getLength();//合法，getLength是const成员函数。
</pre>
如果想在此函数中修改某个成员变量，可以再声明变量时，在其之前加上关键字mutable。  
例：  
<pre class="prettyprit lang-指定样式">
	private: mutable bool isValid;
	public: bool checkList() const {return isValid=true};
</pre>
##const放在函数前后的区别
1）当const放在函数名之前时，修饰的是函数返回值。  
例：
<pre class="prettyprit lang-指定样式">
	const int abc(int a,int b,int c){return a+b+c;}
	abc(1,2,3)++;//报错，因为函数返回值立即执行了++操作，这是不允许的。
</pre>
所以，const放在函数名前，表示返回值不能立即被修改。
若是返回引用类型：  
a）当函数返回引用类型时，返回的是对象本身，而不是复制的返回值。  
b）不能返回局部对象的引用，不能返回局部对象的指针。因为当函数执行完毕后，会释放局部对象/指针的内存空间。  
例：
<pre class="prettyprit lang-指定样式">
	int& abc(int a,int b,int c.int& res){return res=a+b+c;}//合法
	int& abc(int a,int b,int c){return a+b+c;}//非法
</pre>  
2）当const放在函数名之后时，表示是常成员函数，不能修改对象内的任何成员，并且只有成员函数之后可以加const，普通函数不可以。  
若想要修改成员变量，可以在成员变量定义时，加mutable。  
#静态成员变量及静态成员函数(static)
只能用来修饰类的成员函数，不能用来修饰类外函数，同时不能再被const和volatile修饰。  
在程序编译时分配空间，在程序结束后释放空间。  
静态成员函数不存在this指针，静态成员变量的初始化必须在类外，不必加static。  
例：  
<pre class="prettyprit lang-指定样式">
	class Point{
		public:
			void init();
			static void output();
	};
</pre>
1) <pre class="prettyprit lang-指定样式">
	void main{
		Point::init();
		Point::output();
	}
</pre>
Point::init()非法，不能使用类名调用非static成员函数。  
2）<pre class="prettyprit lang-指定样式">
	void main{
		Point pt;
		pt.init();
		pt.output();
	}
</pre>
合法，可以通过类的对象调用static或非static成员函数。  
静态成员（包括变量和函数）之间可以相互访问，但无法访问非静态。  
3）<pre class="prettyprit lang-指定样式">
	private: int x;
	public:
		static void output(){
			cout<<x<<endl;
	};
</pre>
非法，static成员函数不能使用非static成员变量，主要是因为生命周期不同。  
4）<pre class="prettyprit lang-指定样式">
	public:
		static void output();
		void init(){
			output();
		};
</pre>
合法，类的非static成员函数可以访问static成员函数。  
5）类的static成员变量在使用前必须初始化。  
例：在类中有定义： private: static int m;  
类外初始化： int Point::m=10;//初始化时不用加static，而且必须要在类外进行初始化。  
静态成员变量不属于这个类，即sizeof(className)的值不包括静态成员变量的大小。  
总结：对象与对象之间的成员变量是相互独立的，要想共享，则需要使用静态成员函数和静态成员变量。  
#const前后修饰指针
<pre class="prettyprit lang-指定样式">
	char greeting[]="hello";
	char* p=greeting;//非const指针，非const数据
	const char* p=greeting;//非const指针，const数据
	char* const p=greeting;//const指针，非const数据
	const char* const p=greeting;//const指针，const数据
</pre>
const若出现在\*左边，则表示被指物为常量；若出现在右边，则表示指针自身为常量；若出现在两边，则表示被指物和指针均为常量。（左物右针）  
const char* p与char const *p等价。  
在修饰类的成员函数时，const放在函数后边，表示该函数不会对该对象做任何更改。  
#优先队列自写仿函数  
以出入链表，对链表进行排序为例。  
<pre class="prettyprit lang-指定样式">
	struct ListNode{
		int val;
		ListNode *next;
		ListNode(int x):val(x),next(nullptr){}
	};
	struct cmp{
		bool operator()(const ListNode* a,const ListNode* b){
			return a->val>b->val;//小根堆，堆内元素均大于堆顶元素
		}
	}；
	priority_queue< ListNode*,vector< ListNode*>,cmp> que;
</pre>
#Lambda表达式
Lambda表达式本质上是一个匿名函数。  
例：计算num数组中能被3整除的元素个数。  
1）<pre class="prettyprit lang-指定样式">
	count=count_if(num.begin(),num.end(),[](int x){return x%3==0;});
</pre>
2)<pre class="prettyprit lang-指定样式">
	int count=0;
	for_each(num.begin(),num.end(),[&count](int x){count+=x%3==0;});
</pre>
[](int x)中[]代替了函数名（匿名函数），并且没有声明返回类型，如果需要声明返回类型，要用尾置返回类型。  
引入auto：  
<pre class="prettyprit lang-指定样式">
	auto m=[](int x){return x%3==0;};
</pre>
Lambda可捕捉访问作用域内的动态变量，名称放在[]内。  
用lambda实现仿函数功能。  
<pre class="prettyprit lang-指定样式">
	autocomp=[](const ListNode* a,const ListNode* b){return a->val>b->val;};
	priority_queue< ListNode*,vector<ListNode*>,decltype(comp)> que(comp);//注意需要加decltype
</pre>
注：对sort()使用lambda时，不需要decltype关键字，而且对sort()自写仿函数时，只可重写>或<，不可重写>=或<=，会发生越界行为。  
#public,private,protected关键字
1）private成员只能被本类成员（类内、派生类不行）和友员访问。    
2）protected可以被派生类访问。    
3）final，在类名之后加final，禁止类被继承；在函数名之后加final，禁止虚函数在子类中被重载。  
##基类与派生类之间的说明符
1）public继承：基类public、protected、private成员在派生类中保持不变。  
2）protected继承：基类public、protected、private成员在派生类中变成protected、protected、private。  
3）private继承：基类public、protected、private成员在派生类中全部变成private。  
例：  
<pre class="prettyprit lang-指定样式">
	class A{
		protected:
			int a;
		public:
			int b;
		private:
			int c;
	};
	A tempA;
	tempA.a=10;//非法，在类外不可以访问protected
	tempA.b=10;//合法，在类外可以访问public
	tempA.c=10;//非法，在类外不可以访问private
	class B:public A{
		cout<< a<< endl;//合法，在类内可以访问protected
		cout<< b<< endl;//合法，public成员
		cout<< c<< endl;//非法，private成员不能被派生类访问
	};
</pre>
若想在派生类中调用基类中同名成员函数，可以使用B.A::func();  
#智能指针
##auto_ptr
头文件#include \<memory>  
是一种防止“被抛出异常时发生资源泄漏”的只能指针。  
思想：用一个对象存储需要被自动释放的资源，依靠析构函数释放资源。保证在任何情况下，只要自己被摧毁，就一定连带释放自己的资源。  
1)没有所有的指针算术运算，不允许使用一般指针惯用的赋值初始化方式。  
<pre class="prettyprit lang-指定样式">
	auto_ptr< classA> ptr1(new classA);//合法
	auto_ptr< classA> ptr2=new classA;//非法
</pre>
2）auto_ptr拥有权的转移  
<pre class="prettyprit lang-指定样式">
	auto_ptr< classA> ptr1(new classA);
	auto_ptr< classA> ptr2(ptr1);//ptr2拥有了new出来的对象，ptr1不再拥有它，即所有权的转移，对象只会在ptr2销毁时被delete一次。
</pre>
若ptr2被赋值之前正拥有一个对象，赋值时将会delete掉那个对象。  
只有auto_ptr可以用来当做另一个auto_ptr的初值，普通指针是不行的，例：
<pre class="prettyprit lang-指定样式">
	auto_ptr< classA> ptr;//可以定义空的auto_ptr指针，因为构造函数有默认值0。
	ptr =new classA;//非法
	ptr=auto_ptr< classA>(new classA);//合法
</pre>
3）被当做一个参数传递给函数时，被调用端的参数取得auto_ptr的所有权，所函数不再将其传递出去，它所指的对象就会在函数退出时删除。  
4）const并非意味着不可修改auto_ptr的所拥有的对象，而是指不能更改auto_ptr的所有权。  
###auto_ptr的使用原则
1. 不要使用auto_ptr绑定指向静态分配对象的指针。  
2. 不要使用两个auto_ptr指向同一个对象。  
3. 不要使用auto_ptr对象保存指向动态分配数组的指针。  
4. 不要将auto_ptr保存在容器中。  
##shared_ptr
与auto_ptr不同的是，shared_ptr是一个标准的共享所有权的智能指针，允许多个指针指向同一个对象，通过计数机制实现，解决了auto_ptr独占的局限性。  
成员函数有：  
1）use_count：返回引用计数的个数。  
2）unique：返回是否是独占所有权=1。  
3）swap：交换两个shared_ptr对象。  
4）reset：放弃内部对象的所有权或拥有对象的变更，使use_count减1.  
5）get：返回内部指针。  
例：  
<pre class="prettyprit lang-指定样式">
	auto r=make_shared< int>();//此时r的use_count=1.
	auto q=r;//q原来指向的对象的use_count-1，r的use_count+1，此时q和r指向同一对象，所以q和r的use_count均为2.  
</pre>
使用make_shared用其参数来构造给定类型的对象。make_shared函数是一种最安全的分配和使用动态内存的方法，返回一个指向对象的shared_ptr，创建shared_ptr最好使用make_shared函数。    
一旦use_count为0，便会自动释放自己所管理的对象。  
在容器中使用shared_ptr，需要用erase去处理用完的shared_ptr。  
shared_ptr共享相同的状态，而不是多个对象独立的拷贝。  
注意多个线程同时读同一个shared_ptr对象是线程安全的，但如果多个线程对同一个shared_ptr对象进行读和写，则需要加锁。不同的shared_ptr可以被多线程同时修改。    
###使用规范
1）不delete get返回的指针。  
2）若使用了get返回的指针，最后一个对应的shared_ptr销毁后，所使用的指针便无效了。  
3）若使用的shared_ptr管理的资源不是new分配的，则需要传递一个删除器。  
##unique_ptr
独占所指向的对象，同一时刻只能有一个unique_ptr指向给定的对象，无法复制，禁止拷贝语义，可以使用移动语义。  
1）创建unique_ptr  
<pre class="prettyprit lang-指定样式">
	unique_ptr< int> pInt(new int(5));
</pre>
2）无法复制构造和赋值操作  
<pre class="prettyprit lang-指定样式">
	unique_ptr< int> pInt2(pInt);//非法
	unique_ptr< int> Pint3=pInt;//非法
</pre>
因为独占性，其原理是通过将拷贝构造函数和赋值函数设为private/delete。   
但是有一个例外：可以拷贝或赋值一个将要销毁的unique_ptr，例如从函数返回一个局部对象的拷贝。  
<pre class="prettyprit lang-指定样式">
	unique_ptr< int> clone(int p){
		return unique_ptr< int>(new int(p));
	}
</pre>   
3）可以进行移动构造和移动赋值操作（用来转移所有权，转移后原指针变为空）。  
<pre class="prettyprit lang-指定样式">
	unique_ptr< int> pInt2=move(pInt);//转移所有权
</pre>
转移所有权还可以使用：  
<pre class="prettyprit lang-指定样式">
	unique_ptr< int> pInt2(pInt.release());
</pre>
4）可以从函数返回一个unique_ptr。
###unique_ptr的应用
1）为动态申请的资源提供异常安全保证。  
2）返回函数内动态申请资源的所有权。  
3）在容器中保存指针。  
<pre class="prettyprit lang-指定样式">
	vector< unique_ptr< int>> vec;
	unique_ptr< int> p(new int(5));
	vec.emplace_back(move(p));//使用移动语义
</pre>
4）管理动态数组
<pre class="prettyprit lang-指定样式">
	unique_ptr< int[]> p(new int[5]{1,2,3,4,5});
	p[0]=0;//重载了[]
</pre>
5）作为auto_ptr的替代品  
##weak_ptr
是一种不控制对象生命周期的智能指针，指向一个shared_ptr管理的对象，目的是配合shared_ptr而引入的一种智能指针，只可以从shared_ptr或weak_ptr对象构造，不会对use_count造成影响。   
支持拷贝或赋值，但不会影响use_count，主要用来解决shared_ptr因循环引用而有不能释放资源的问题。  
成员函数有：  
1）expired：用于检测所管理的对象是否已经释放，若释放则返回true，否则返回false。  
2）lock：用于获取所管理的对象的强引用（shared_ptr），若expired为true，则返回一个空的shared_ptr；否则返回一个shared_ptr。  
3）use_count：返回与shared_ptr共享的对象的引用计数。  
4）reset：将weak_ptr置空。  
#struct与class区别和联系
1）默认的成员访问权限不同。  
class默认是private，struct默认是public。  
2）默认的继承方式不同。  
class是private，struct是public。  
3）class可继承struct，也可继承class，struct亦是如此。  
4）class和struct都可以使用{}（初始化值列表）来赋初值，但是有两个前提：(a)字段是public，因为public才可以直接访问；(b)没有父类，没有自定义构造方法和虚方法，可以有普通的成员方法。  
5）定义模板参数，使用typename，也可以使用class，但不能使用struct，一般情况下typename和class是通用的，但有两个例外：(a)当T是一个类，且这个类又有子类时，只能用typename；(b)typename T::innerClass myInnerObject这里的typename告诉编译器，T::innerClass是一个类，程序需要声明一个类的对象，而不是T的静态成员变量。  
#纯虚函数与抽象类
纯虚函数的格式：virtual <类型><函数名><形参表>=0;  
例:virtual double calcPerimeter()=0;//纯虚函数  
含有纯虚函数的类是抽象类，抽象类无法实例化对象。  
例:  
<pre class="prettyprit lang-指定样式">
	class Shape{
		public:
			virtual double calcPerimeter()=0;
	};
	Shape shape=new Shape();//非法，抽象类无法实例化对象
	//纯虚函数的实现交给抽象类的派生类去实现。  
</pre>
#void指针 void*
void指针所指的内存区域，可以存储任何类型的数据，也可以说是没有数据类型，直到使用这一块内存的时候，才知道里面装的数据。  
可以用任意类型的指针为void指针赋值，但是不能用void指针为已知类型的指针赋值。  
##C++类的四个默认函数
1）默认构造函数A()，无参构造函数。  
2）拷贝（复制）构造函数：A(const A& a)，用一个对象为另一个赋值，当一个类中有指针类型的成员变量时，一定要重写复制构造函数和赋值构造函数。  
3）析构函数~A()。  
4）赋值函数A& b=const A& a，用一个对象a直接为另一个赋值。  
浅复制：复制时仅仅拷贝了数值，若类对象中有指针类型，浅复制后两个对象指向同一块内存空间。  
深复制：拷贝时会对整个空间进行拷贝。  
拷贝构造函数与赋值函数的区别：  
拷贝构造函数是在对象被创建时调用的，赋值函数只能被已经存在了的对象调用。  
例：  
<pre class="prettyprit lang-指定样式">
	string a("hello");
	string b("world");、
	string c=a;//拷贝构造函数，而不是赋值构造函数，最好写成string c(a);
	c=b;//调用了赋值函数
</pre>
用户自定义编写拷贝构造函数和赋值函数是为了实现深复制。默认的只会完成浅复制。  
<pre class="prettyprit lang-指定样式">
	A(const A& a)=default;//明确指明使用默认拷贝构造函数
	A(const A& a)=delete;//禁止使用拷贝构造函数
</pre>
若自己定义了带参数的构造函数，则在创建一个对象时必须初始化。例：Test t;//会报错，必须改为Test t(10);
##委托构造函数
<pre class="prettyprit lang-指定样式">
	class demo{
		private:
			int x,y;
		public:
			demo():demo(0,0){}//缺省构造函数，委托给双参数构造函数
			demo(int a):demo(a,0){}//单参数构造函数，同样委托给双参数构造函数
			demo(int a,int b){x=a;y=b;}//双参数构造函数，被其他构造函数调用
	};
</pre>
#拷贝构造函数的形参不可以是值传递，必须是引用传递
<pre class="prettyprit lang-指定样式">
	class Example{
		public:
			Example(int a):aa(a){}
			Example(Example ex)
				aa=ex.aa;//拷贝构造函数
		private:
			int aa;
	}
	Example e1(10);//调用构造函数，合法
	Example e2=e1;//调用拷贝构造函数，非法
</pre>
因为若是值传递，又需要为e1的值拷贝创建一个副本，然后又需要调用拷贝构造函数，形成无限循环。  
#size,resize和capacity,reserve
size表明容器中实际有多少个元素，resize会在容器的底部添加或删除一些元素，使容器中的实际元素数量达到指定大小。  
capacity表明最少添加多少个元素会导致容器重新分配内存，reserve会调整capacity到指定大小。  
<pre class="prettyprit lang-指定样式">
	vec.reserve(7);//此时，vec.capacity()=7
	vec.reserve(5);//此时，vec.capacity()仍为7。
</pre>
1)当初始化未指定vec的大小，用push\_back或emplace\_back向里面添加元素时，vec的capacity会以2的指数次幂增加。  
2）当初始时指定vec的大小为n时，扩容时会分配一个2n的空间，以2倍扩容。  
#钻石继承问题（菱形继承问题）
存在的问题是二义性和内存冗余问题。  
<pre class="prettyprit lang-指定样式">
					GrandFather
			Father1					Father2
					    Son
</pre>
Father1类中存在方法setValue()，Father2中有方法getValue(),  
Son s(10);s.setValue(20);s.getValue()的值仍为10。这是因为在创建Son时，GrandFather被构造两次，Father1和Father2被分别创建，相互独立。因此在Father1中修改value值，不会改变Father2中的value值，这就是钻石继承数据不一致问题，Son中有两个分属于不同对象的value值。  
通常采用虚基类和虚继承解决数据不一致问题。  
虚继承：在继承定义中包含了virtual关键字。  
虚基类：在虚继承中通过关键字virtual继承而来的基类。  
使用虚基类和虚继承可以让一个指定的基类在继承体系中将其成员数据实例共享给从该基类直接或间接派生出的其他类，即使从不同路径继承而来的同名数据成员在内存中只有一个拷贝，同一个函数名也只有一个映射。  
<pre class="prettyprit lang-指定样式">
	class Father1:virtual public GrandFather
	class Father2:virtual public GrandFather
	class Son:public Father1,public Father2
	//此时getValue()的值为20
</pre>
##构造函数的调用顺序
1. 虚基类按声明顺序调用。  
2. 非虚基类按声明顺序调用。  
3. 调用派生类中成员对象的构造函数（嵌套类）。  
4. 派生类自己的构造函数。  
注意事项：  
1. 在派生类对象中，同名的虚基类只产生一个虚基类子对象，而同名的非虚基类则产生一个非虚基类子对象。  
2. 虚基类的子对象由最后派生出来的类的构造函数通过调用虚基类的构造函数来初始化的，若在派生类的成员初始化列表中没有列出对虚基类构造函数的调用，则表示使用虚基类的默认构造函数。  
3. 虚基类并不是在声明基类时声明的，而是在继承关键字之前。  
##基类子对象
<pre class="prettyprit lang-指定样式">
	class A{
		public:
			int aNum;
	};
	class B:public A{
		public:
			int bNum;
	};
	B b;//此时aNum就是b对象的基类子对象。
</pre>
#constexpr与const
constexpr在使用时需要保证返回值和参数时字面值（确定值）。  
const并不能代表“常量”，它仅仅是对常量的一个修饰，告诉编译器这个变量仅能被初始化，且不能被修改，变量的值可以在运行时也可以在编译时指定。  
constexpr可以用来修饰变量，函数，构造函数，一旦被修改便可当作常量看待。  
<pre class="prettyprit lang-指定样式">
	const int Func(){
		return 10;
	}
	int arr[Func()];//非法
	constexpr int Func(){
		return 10;
	}
	int arr[Func()];//合法
	constexpr int Func(){
		return 1+n;
	}
	int a=4;
	Func(4);//合法
	Func(cin.get());//非法，非字面值。
</pre>
##constexpr的优点
1. 是一种很强的约束，更好地保证程序的正确语义不被破坏。  
2. 编译期可以在编译期对constexpr的代码进行非常大的优化，比如将用到的constexpr表达式直接替换成最终结果。  
3. 相对宏来说，没有额外的开销，更安全可靠。  
4. constexpr修饰类的构造函数时，必须用初始化列表的形式，表明该对象是一个constexpr类型的对象，是一个字面值常量在编译期确定。  
<pre class="prettyprit lang-指定样式">
	const int* p=nullptr;//p是一个指向整型常量的指针
	constexpr int* q=nullptr;//q是一个指向整数的常量指针
</pre>
