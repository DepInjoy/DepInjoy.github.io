---

---



```C++
#include <iostream>
#include <thread>
class MyThread {
public:
	void operator()() {
		while (true){
			std::cout << "class MyThread " << std::endl;
		}
	}
};

void creatMyThread(void){
	while (true){
		std::cout << "func MyThread " << std::endl;
	}
}

void main(void)
{
	MyThread mt;
	std::thread t1(mt);				//创建线程并执行，要求mt可复制
	std::thread t2(creatMyThread);
	system("pause");
}
```

