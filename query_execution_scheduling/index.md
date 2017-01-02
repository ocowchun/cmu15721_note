# query_execution_scheduling
lecture #10

A query plan is comprised of query operator
relational operator like scan, join, insert ,delete ...
physical operator allow us to have control flow in our query plan


### process model
defines how the system is architected to suppoet concurrent requests from a multi-user application.

### porcess models
#### Approach #1: Process per DBMS Worker

#### Approach #2: Process Pool

#### Approach #3: Thread per DBMS Worker
* DBMS have to manage its owen scheduling. (Which mean we can have more fine control to organize tasks in our threads)
* May or may not use a dispatcher thread.
* Examples: IBM DB2, MSSQL, MySQL, Oracle (Newer)

less overhead per context switch/
don't have to manage shared memory.

The thread per worker model does not mean that you have intra-query parallelism (MySQL has multi threads worker but the whole query is complete by one thread)

PG on the other hand can use mu

## SCHEDULING

For each query plan, the DBMS has to decide where, when and how to execute it.
* how many tasks should it use?
* how many CPU cores should it use?
* what CPU core should the tasks execute on ?
* where should task store its output?

The DBMS always knows more than the OS. so it can always make better decision.
## INTER-QUERY PARALLELISM
### Improve overall performance by allowing multiple queries to execute simultaneously.
* provide the illusion of isolation through concurrency control scheme.
### The difficulty of implementing a concurrency control scheme is not significantly affected by the DBMS’s process model.

## INTRA-QUERY PARALLELISM
### Improve the performance of a single query by executing its operators in parallel.

### Approach #1: Intra-Operator (Horizontal)
* operators are decomposed into independent instances that perform the same function on different subsets of data
* for example,  you have a scan operator to scan the whole table, we can split the operator into pieces and they can work in parallel
* It is common is database that support intra-query parallelism
![](https://www.evernote.com/l/AJHXNJxgf0xIqqhbYlu5U3AC0J1gcgzpHsAB/image.png)

### Approach #2 Inter-Operator (Vertical)
Operations are overlapped in order to pipeline data from one stage to the next without materialization.
The speak actually don’t know how many db implement this feature.
![](https://www.evernote.com/l/AJE64apJle9A6JSqZ_PjR7ycwafly5n0KfQB/image.png)

## WORKER ALLOCATION
### Approach #1: One Worker per Core
* Each core is assigned one thread that is pinned to that core in the OS
### Approach #2 MultipleWorkers per Core
* Use a pool of workers per core
* Allows CPU cores to be fully utilized tin case one worker at a core blocks.

## TASK ASSIGNMENT
### Approach #1:Push
* A centralized dispatcher assigns tasks to workers and monitors their progress.
* When the worker notifies the dispatcher that it is finished, it is given a new task.
### Approach #2: Pull
* Workers pull the next task from a queue, process it , and then return to get the next task.

## Regardless of what worker allocation or task assignment policy the DBMS uses, it’s important that workers operate on local data.
The DBMS’s scheduler has to be aware of it’s underlying hardware’s memory layout
* Uniform vs. non-Uniform Memory Access

## UNIFORM MEMORY ACCESS
* 當一個 CPU 需要特定位置的 memory 時，他會向 Bus 丟出請求，接著請求會導向目標的 memory 位置。
* 因為每個 CPU 對 memory 的請求都有相同的成本，所以稱之為 uniform memory access
![](https://www.evernote.com/l/AJFekA75t5xPc5gycHAh2Na0MnhE7l4gggkB/image.png)

## NON-UNIFORM MEMORY ACCESS
* 每個 CPU 會有對應的 local memory,讀取速度會很快，如果要讀取其他的 memory 就需要透過 inter connect 去向其他 CPU 請求，速度會慢一點。

* 對一個 CPU 而言， 讀取 memory 的 cost 會有差異，所以叫做 NON-UNIFORM MEMORY ACCESS
![](https://www.evernote.com/l/AJEoBDZ4mvJHuq8iF-TzT2BwUHsodxgrdnUB/image.png)


## question
### what’s the different between intra and inter ?
Inter: 之間 (i.e. international)
intra: 內部的 (i.e. intra-trading)

### what is DBMS scheduler