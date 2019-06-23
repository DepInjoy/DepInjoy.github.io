[使用 Boost 的 IPC 和 MPI 库进行并发编程](https://www.ibm.com/developerworks/cn/aix/library/au-concurrent_boost/index.html)

[Boost介绍](https://www.kancloud.cn/wizardforcel/the-boost-cpp-libraries/121987) [Boost IPC共享内存的使用总结](<https://my.oschina.net/lday/blog/724458>) 

```C++
#if 0
	const static uint16_t mapSize = 64;
	const char* memName = "Hello";
	shared_memory_object::remove(memName);
	try{
		//创建共享内存，并设置其大小
		shared_memory_object sharedMem1(create_only, memName, read_write);
		//set size of shared memory
		sharedMem1.truncate(mapSize);
		//map the shared memory to current process
		mapped_region mapRegion(sharedMem1, read_only);
		//char* data = "Hello World\n";
		//strcpy_s(static_cast<char*>(mapRegion.get_address()), strlen(data) + 1, data);
	}
	catch (interprocess_exception & e){
		std::cout << "Creating shared memory object failed : " << e.what() << std::endl;
		shared_memory_object::remove(memName);
	}


	//销毁共享内存
	if (shared_memory_object::remove(memName))
		std::cout << "shared memory " << memName << "removed successfully !" << std::endl;
	else
		std::cout << "shared memory " << memName << "removed failed !" << std::endl;
#endif
```

