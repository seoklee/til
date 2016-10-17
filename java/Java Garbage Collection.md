# Java Garbage Collection

When Java collects, it 'stops' the world. It stops every thread except the thread that's executing the garbage collection.

-------

###Pro tip

- assigning a 'null' value to an object should not be a big deal. 
- System.gc() is evil like 'goto' command. Do not use it. 

-------

Garbage collection happens without explicit calls from the developer. Garbage collection, will be referred as GC from now on, makes two big 'assumptions' about code in order to carry out the clean-up

- Most objects soon become unreachable.
- References from old objects to young objects only exist in small numbers.

Those two hypotheses are called 'weak generational hypothesis.' In order to utilize this hypotheses fully, two areas called 'young' and 'old' are created.

- 'Young' Generation consists of newly created objects. As it is stated before, most objects become unreachable very soon. So majority of objects are placed here and thrown away here. GC happening in this area is called 'Minor GC.'
- 'Old' Generation consists of all of these objects that survived from minor GC. This area is bigger than Young Generation, so GC in old generation happens infrequently. GC happening in here is called Major GC (or Full GC)
- 'Permanent' Generation is also called method area. Objects and intern string literal info are stored here. It contains metadata required bhy the JVM to describe the classes and methods used in the application. Just because an object 'survives' GC in Old Generation long time, it will not be placed in Permanent Generation. 

Whenever an object in Old Generation refers to an object in Young Generation, Old Generation contains 'card table' which consists of 512 bytes of chunk called 'card'

Card table records information when an object in Old Generation refers to an object in Young Generation. When weak GC happens, it does not go through the objects in Old Generation, but it goes through its card table.

Card table is managed with write barrier*. Write barrier is a mechanism that speeds up minor GC. Though it creates a slight overhead, but generally reduces the GC time.

*Write barrier is a mechanism that is enforced in order to carry out the correct order of 'write'.

## Young Generation

Young generations are consists of Eden area and two Survivor areas.

- Most of the newly created objects are placed in Eden
- The objects that survives the GC in Eden at least one time is placed at one of the Survivor section
- This keeps happening...
- When one of Survivor is full, it moves all of the 'alive' objects into the other survivor section. The full survivor area is emptied.
- This keeps happening.. After a while, those objects are still alive are moved to the old generation.

One can see that one of the survivor is always empty. If both survivors section is empty, or they both contains data at the same time, your system has something wrong.

HotSpot VM uses two technologies for fast memory allocations. One of them is bump-the-pointer and the another is called TLABs(Thread-Local Allocation Buffers).

Bump-the-pointer tracks the last obejct that were allocated in Eden. The last object allocated in Eden is on the top. When another object needs to be allocated, it checks whether the newly created object will be big enough to insert into Eden. If it is big enough, it is put onto Eden. So in order to create new object, it only needs to check the lastly created object.

But if one considers the performance at multi-threaded environment, it's a different story. In order to compromise the multi-threaded environment, one needs to use lock, which will impair the performance. TLABs solves this problem. Each thread is responsible for the small chunk of area at Eden. Each thread is only allowed to go to its own TLAB, so memory can be allocated without TLAB.


## GC in Old Generation

GC happens in Old Generation when it is full. There are 5 ways to perform GC.

- Serial GC
- Parallel GC
- Parallel Old GC(Parallel Compacting GC)
- Concurrent Mark & Sweep GC(CMS)
- G1(Garbage First) GC

One should NEVER run serial GC in a running server. It's a technique that should be only used when there is only one available core at the desktop's CPU. It decreases the application's performace significantly.

### Serial GC (-XX:+UseSerialGC)

Young generation uses serial GC. Old Generation GC uses mark-sweep-compact algorithm. The first step of this algorithm 'marks' the surviving objects in Old Generation. Then, it starts checking the heap from the head and leave alone those that are 'surviving'. (Sweep) Then, it stacks the heap from the start so it will be devided into area with objects and the area without objects

Serial GC is appropriate when there is small amount of memory and CPU cores are limited

### Parallel GC (-XX:+UseParallelGC)

Parallel GC is very similar to Serial GC. However, Parallel GC uses multiple threads while Serial GC uses only one. Therefore, it can take care of the objects in a fast fashion. It's advantageous when it has multiple cores and ample memmory.

### Parallel Old GC (-XX:+UseParallelGC)

Same thing as Parallel GC, but Old Generation GC's algorithm is different. This uses Mark-Summary-Compaction. Summery step is different by additionally checking surviving objects from  the areas that just finished GC. (?) This is different than Sweep part of Mark-Sweep-Compaction algorithm, and a bit more complicated.

### CMS GC (-XX:+UseConcMarkSweepGC)

CMS GC is more complicated than the other GC techniques that were explained from above. At Initial Marks step, it only finds the surviving objects among the closest objects from the class loader. Thus, it only stops the world for a short amount of time. Then, at Concurrent Mark step, it finds all those objects that are referencing those that were identified as surviving from the previous step. This happens concurrently.

The Remark step identifies objects that were newly added or objects with 'lost' references. 

The Concurrent Sweep step cleans up the garbage.

Due to concurrency, stop-the-world time is short. When all application's response time is paramount, this GC technique is used. CMS GC is also known as Low Latency GC.

However, it is more CPU and memory intensive than the other GC technique, and there is no compaction step.

Thus, one must be careful in using CMS GC. If there are too much fragmented memory and compaction occurs, it tends to increase stop-the-world time significantly that the other tecniques' stop-the-world time will be less than those increased stop-the-world time. So one must be careful in using CMS GC and check how many times and how frequently compaction occurs.

### G1 GC

G1 GC ignores Young Generation and Old Generation. G1 GC allocates multiple squares inside a big square, then allocates objects inside those smaller squares. If a small square becomes full, it allocates a new smaller square. This elimnates Young Generation's three parts and the movement of the objects from Young Generation to the Old Generation. G1 GC is devised because of troublesome nature of CMS GC.

G1 GC's main advantage is its performance. It is faster than all of GC techniques that are listed. One should refrain using it if the environment is JDK 6. It's pretty unstable in JDK 6 and might crash JVM.

Also be mindful that one service's GC option's performance might be wildly different. If you can say confidently that every object will be in same size and same kind, you can use the same GC option. However, the object created from service's WAS will be in different size and it's survival time will be all different. Hardwares will be different. One must find the best value by monitoring numbers of threads and WAS's instance per machine. 


(http://d2.naver.com/helloworld/1329)