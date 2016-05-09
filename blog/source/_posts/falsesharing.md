# How to Avoid False Sharing

### What is false sharing?
-------
False sharing is a situation when mutiply threads tries to modify the adjacent data in the same cache line in CPU or the local copy in one CPU core since the entire system tries to keep the cache consistent cross multicore. At most of time, those global data is located in closer to each other. Although two threads updates the different memory but they are fitted into a same cache line, therefore if one of them is updates, the entire cache line should be outdated and forced to be updated.

![alt text](https://software.intel.com/sites/default/files/m/d/4/1/d/8/5-4-figure-1.gif "False sharing")
Image from [https://software.intel.com/](
https://software.intel.com/en-us/articles/avoiding-and-identifying-false-sharing-among-threads
)

### Experiment
------
```C++
#include <pthread.h>
/* Declare the global variable*/
struct data {
    unsigned long long x;
    unsigned long long y;
};
static struct data f;
#define NUMLOOP 100000000
```
```C++
void *sum_a(void* v){
    unsigned int s = 0;
    int i;
    for (i = 0; i < NUMLOOP; ++i){
        s += f.x;
    }
    pthread_exit(NULL);
}

void *inc_b(void* v){
    int i;
    for (i = 0; i < NUMLOOP; ++i){
        ++f.y;
    }
    pthread_exit(NULL);
}
int main(){
  pthread_t threads[2];
  /* make two threads to calculate the sum and update simultaneously */
  int a = pthread_create(&threads[0], NULL, sum_a, NULL);
  int b = pthread_create(&threads[1], NULL, inc_b, NULL);
  pthread_join(threads[0],NULL);
  pthread_join(threads[1],NULL);
  //sum_a();
  //inc_b();
  pthread_exit(NULL);
}
```
``` c
/* pthread version:*/
./a.out  1.82s user 0.01s system 189% cpu 0.963 total
/* Sequential version: */
./a.out  0.27s user 0.00s system 98% cpu 0.275 total
```
We can see the two threads is slower than the sequential version. The reason is that x and y is located in a same cache line. If one variable is changed and the local cache is changed as well, the entire cache line will be expired, which will trigger the cache synchronization mechanism between cores.

### How to Solve it
------
1.	keep thread-local copy and make multiple update on it and save it to global only once.
2. Add padding to the global varibla. For gcc/Clang, we can add
```
__attribute__((__aligned__(NUM_BYTE)))
``` 
to the end of declaration. For example
```
struct data {
unsigned long long x;
int padding __attribute__((__aligned__(64)));
unsigned long long y;
};	
```
Or we can add
```
int padding[12]; // 12 * 4 bytes
```
#### But too large padding will lead waste of cache.