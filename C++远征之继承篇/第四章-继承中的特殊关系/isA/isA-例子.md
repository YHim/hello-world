## isA-例子

![](./要求.png)

test1(Person p)它的参数是Person的一个对象。
test2(Person &p)它的参数是Person的一个引用。
test3(Person *p)它的参数是Person的一个指针。

Person.h
```h
#include <string>
using namespace std;

class Person
{
public:
	Person(string name = "Jim");
	~Person();
	void play();
protected:
	string m_strName;
};
```

Person.cpp
```cpp
#include "Person.h"
#include <iostream>
using namespace std;

Person::Person(string name)
{
	m_strName = name;
	cout << "Person()" << endl;
}

Person::~Person()
{
	cout << "~Person()" << endl;
}

void Person::play()
{
	cout << "Person -- play()" << endl;
	cout << m_strName << endl;
}
```

Soldier.h
```h
#include "Person.h"

class Soldier : public Person
{
public:
	Soldier(string name ="James", int age = 20);
	~Soldier();
	void work();
protected:
	int m_iAge;
};
```

Soldier.cpp
```cpp
#include <iostream>
#include "Soldier.h"
using namespace std;

Soldier::Soldier(string name, int age)
{
	m_strName = name;
	m_iAge = age;
	cout << "Soldier()" << endl;
}

Soldier::~Soldier()
{
	cout << "~Soldier()" << endl;
}

void Soldier::work()
{
	cout << m_strName << endl;
	cout << m_iAge << endl;
	cout << "Soldier -- work()" << endl;
}
```

demo.cpp
```cpp
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"

int main()
{
	Soldier soldier;
	Person p = soldier;
	p.play();
	
	system("pause");
	return 0;
}
```

运行结果：

![](./运行结果1.png)

可以看到，因为soldier是子类，所以在执行子类的构造函数之前先执行父类的构造函数Person()。

### ----------------------------------------------------------------
将demo.cpp代码改成：
```cpp
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"

int main()
{
	Soldier soldier;
	Person p;
	p.play();
	
	system("pause");
	return 0;
}
```

运行结果：

![](./运行结果2.png)

### ----------------------------------------------------------------
将demo.cpp代码改成：
```cpp
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"

int main()
{
	Soldier soldier;
	Person p;
	p = soldier;
	p.play();
	
	system("pause");
	return 0;
}
```

运行结果：

![](./运行结果1.png)

说明，无论是用soldier去初始化p这个对象还是将soldier直接赋值给p这个对象，都能够将soldier当中的m_strName赋值给p的m_strName。

### ----------------------------------------------------------------
将demo.cpp代码改成：
```cpp
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"

int main()
{
	Soldier soldier;
	Person *p = &soldier;
	p->play();
	
	system("pause");
	return 0;
}
```

运行结果：

![](./运行结果1.png)

可见，无论是用对象赋值的方式还是指针指向的方式，如果是用父类去接收或者指向子类对象的值，那么打印出来的都是子类对象所拥有的值。

### ----------------------------------------------------------------
将demo.cpp代码改成：
```cpp
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"

int main()
{
	Soldier soldier;
	Person *p = &soldier;
	p->play();
	p->work();
	
	system("pause");
	return 0;
}
```

程序报错，可见使用Person的对象或指针只能调用Person自有的数据成员和成员函数，无法调用它子类独有的数据成员和成员函数。

### ----------------------------------------------------------------
将demo.cpp代码改成：
```cpp
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"

int main()
{
	Person *p = new Soldier;//通过Person的指针指向从堆中分配来
	                                        //的Soldier的对象。
	p->play();

	delete p;
	p = NULL;
	
	system("pause");
	return 0;
}
```

运行结果：

![](./运行结果3.png)

可以看到，当我们用父类的指针去指向子类的一个对象的时候，那么子类的这个对象会去实例化，所以实例化的过程会先调用父类的构造函数，再调用子类的构造函数。

但是当销毁的时候，可以发现，只执行了父类的析构函数，子类的析构函数没有被执行，就有可能造成内存的泄漏。

在这种情况下，如何避免内存的泄漏呢？

使用虚的析构函数。当存在继承关系的时候，我们使用父类的指针去指向堆中的子类的对象，并且还想使用父类的指针去释放这块内存，此时需要虚析构函数。写作virtual ~Person();当写上关键字virtual之后，这个关键字是可以被继承下去的。

将Person.h修改为：
```h
#include <string>
using namespace std;

class Person
{
public:
	Person(string name = "Jim");
	virtual ~Person();
	void play();
protected:
	string m_strName;
};
```

也就是说，Soldier.h中~Soldier()此时即使不写成virtual ~Soldier()也是虚析构函数。希望都写上。
这样，就可以成功释放，避免内存泄漏了。

运行结果：

![](./运行结果4.png)