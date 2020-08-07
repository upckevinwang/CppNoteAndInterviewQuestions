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
	}
	CxString string1(24);//合法
	CxString string2=10;//合法，此时会调用转换操作，实际等于CxString string2(10);
	但当构造函数前加上explicit关键字时，该操作便变成非法的，取消了类构造函数的隐式自动转换。
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
	cout<<p<<endl;//此时，输出a，而不是p的地址，因为运算符重载要打印地址。  
	//只针对char类型的指针，对于其他类型的指针可以正确输出地址。  
	cout<<reinterpret_cast<void*>(p)<<endl;//此时便可以输出p的地址。
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
	}
	class Ellipse:public Shape{
		public:
			Ellipse(float majr,float minr);
			virtual void render();
		protected:
			float major_axis;
			float minor_axis;
	}
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
		}
</pre>
合法，类的非static成员函数可以访问static成员函数。  
5）类的static成员变量在使用前必须初始化。  
例：在类中有定义： private: static int m;  
类外初始化： int Point::m=10;//初始化时不用加static，而且必须要在类外进行初始化。  
静态成员变量不属于这个类，即sizeof(className)的值不包括此m的大小。  
总结：对象与对象之间的成员变量是相互独立的，要想共享，则需要使用静态成员函数和静态成员变量。