layout: blog
title: C++ RAII
date: 2016-09-30 16:48:26
tags: C++, Memory Management
---

###What is RAII?
The full name is Resource Acquisition Is Initialization. It is a technique to bind the life cycle of a resource to the life time of an object.
The resource includes:
Allocated Memory
Thread
open file/ socket
Database Connection
Lock mutex

###std::unique_ptr
unique_ptr is a smart pointer that keep the sole ownership of an object through a pointer. It will destroyed the object and deallocate the memory. The object is destroyed and its memory is deallocated when
1.	When the unique_ptr is destroyed
2. The unique_ptr is reassigned to another object(operator = or reset())

Code exmaple:

```c++
#include <memory>
#include <iostream>
using namespace std;
struct Test {
	Test(){cout << "This is the constructor of Test" << endl;};
	~Test(){cout << "This is the destructor of Test" << endl;>};
}
int main(){
	unique_ptr<Test> p1(new Test);
}

```

### std::shared_ptr
Several shared_ptr object might share an object.
The object will be deallocated when
1.	When the last remaining shared_ptr is destroyed
2. When the last remaining shared_ptr is assigned to another object.

---
Implementation
Typically, shared_ptr includes two pointer
1.	the stored pointer(get())
2.	a pointer to control block

control block includs 
1.	a pointer to the managed object
2. the deleter
3. the allocater
4. the referece counter
5. the number of weak_ptr that reference the object

```c++
//reference from http://en.cppreference.com/w/cpp/memory/shared_ptr
#include <iostream>
#include <memory>
#include <thread>
#include <chrono>
#include <mutex>
 
struct Base
{
    Base() { std::cout << "  Base::Base()\n"; }
    // Note: non-virtual destructor is OK here
    ~Base() { std::cout << "  Base::~Base()\n"; }
};
 
struct Derived: public Base
{
    Derived() { std::cout << "  Derived::Derived()\n"; }
    ~Derived() { std::cout << "  Derived::~Derived()\n"; }
};
 
void thr(std::shared_ptr<Base> p)
{
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::shared_ptr<Base> lp = p; // thread-safe, even though the
                                  // shared use_count is incremented
    {
        static std::mutex io_mutex;
        std::lock_guard<std::mutex> lk(io_mutex); // RAII for mutax lock
        std::cout << "local pointer in a thread:\n"
                  << "  lp.get() = " << lp.get()
                  << ", lp.use_count() = " << lp.use_count() << '\n';
    }
}
 
int main()
{
    std::shared_ptr<Base> p = std::make_shared<Derived>();
 
    std::cout << "Created a shared Derived (as a pointer to Base)\n"
              << "  p.get() = " << p.get()
              << ", p.use_count() = " << p.use_count() << '\n';
    std::thread t1(thr, p), t2(thr, p), t3(thr, p);
    //p.reset(); // release ownership from main
    std::cout << "Shared ownership between 3 threads and released\n"
              << "ownership from main:\n"
              << "  p.get() = " << p.get()
              << ", p.use_count() = " << p.use_count() << '\n';
    t1.join(); t2.join(); t3.join();
    std::cout << "All threads completed, the last one deleted Derived\n";
}
```