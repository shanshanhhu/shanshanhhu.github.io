---
title: Flink Concepts And Theories
date: 2021-03-12 09:30:22 +0800
categories: [bigdata, flink]
tags: [flink]
---

# Flink Architecture
[Official Documents](https://ci.apache.org/projects/flink/flink-docs-stable/concepts/flink-architecture.html)
## Flink Cluster
![](../assets/img/sample/flink-cluster.png)
The Flink runtime consists of two types of processes: a JobManager and one or more TaskManagers.
The Client is not part of the runtime and program execution, but is used to prepare and send a dataflow to the JobManager. After that, the client can disconnect (detached mode), or stay connected to receive progress reports (attached mode).

### JobManager
The JobManager coordinates the distributed execution of Flink Applications: it decides when to schedule the next task (or set of tasks), reacts to finished tasks or execution failures, coordinates checkpoints, and coordinates recovery on failures, among others. This process consists of three different components:
- ResourceManager
The ResourceManager is responsible for resource de-/allocation and provisioning in a Flink cluster — it manages task slots, which are the unit of resource scheduling in a Flink cluster.
- Dispatcher
The Dispatcher provides a REST interface to submit Flink applications for execution and starts a new JobMaster for each submitted job.
- JobMaster
A JobMaster is responsible for managing the execution of a single JobGraph. Multiple jobs can run simultaneously in a Flink cluster, each having its own JobMaster.
There is always at least one JobManager. A high-availability setup might have multiple JobManagers, one of which is always the leader, and the others are standby
### TaskManagers
The TaskManagers (also called workers) execute the tasks of a dataflow, and buffer and exchange the data streams.
There must always be at least one TaskManager. The smallest unit of resource scheduling in a TaskManager is a task slot. The number of task slots in a TaskManager indicates the number of concurrent processing tasks. Note that multiple operators may execute in a task slot.

### Tasks and Operator Chains
For distributed execution, Flink chains operator subtasks together into tasks. Each task is executed by one thread. Chaining operators together into tasks is a useful optimization: it reduces the overhead of thread-to-thread handover and buffering, and increases overall throughput while decreasing latency.
The sample dataflow in the figure below is executed with five subtasks, and hence with five parallel threads.
![](../assets/img/sample/tasks_chains.png)

### Task Slots and Resources
Each worker (TaskManager) is a JVM process, and may execute one or more subtasks in separate threads. To control how many tasks a TaskManager accepts, it has so called task slots (at least one).
Each task slot represents a fixed subset of resources of the TaskManager.  Slotting the resources means that a subtask will not compete with subtasks from other jobs for managed memory, but instead has a certain amount of reserved managed memory.
Tasks in the same JVM share TCP connections (via multiplexing) and heartbeat messages. They may also share data sets and data structures, thus reducing the per-task overhead.
#### benefits of slot sharing
- A Flink cluster needs exactly as many task slots as the highest parallelism used in the job. No need to calculate how many tasks (with varying parallelism) a program contains in total.
- It is easier to get better resource utilization. Without slot sharing, the non-intensive source/map() subtasks would block as many resources as the resource intensive window subtasks. With slot sharing, increasing the base parallelism in our example from two to six yields full utilization of the slotted resources, while making sure that the heavy subtasks are fairly distributed among the TaskManagers.
![](../assets/img/sample/slot_sharing.png)

## Flink Job Submission Process
![](../assets/img/sample/flink-job-submission.png)
An Application Master (AM) is a per-Yarn - Application (app) daemon to look after the lifecycle of the Yarn - Job.

# Stateful Stream Processing
## State Persistence
Flink implements fault tolerance using a combination of stream replay and checkpointing. A checkpoint marks a specific point in each of the input streams along with the corresponding state for each of the operators.
A streaming dataflow can be resumed from a checkpoint while maintaining consistency (exactly-once processing semantics) by restoring the state of the operators and replaying the records from the point of the checkpoint.
The fault tolerance mechanism continuously draws snapshots of the distributed streaming data flow.
### Checkpointing
The central part of Flink’s fault tolerance mechanism is drawing consistent snapshots of the distributed data stream and operator state. These snapshots act as consistent checkpoints to which the system can fall back in case of a failure.

#### Barriers
A core element in Flink’s distributed snapshotting are the stream barriers. These barriers are injected into the data stream and flow with the records as part of the data stream. Barriers never overtake records, they flow strictly in line. A barrier separates the records in the data stream into the set of records that goes into the current snapshot, and the records that go into the next snapshot. Each barrier carries the ID of the snapshot whose records it pushed in front of it.
![](../assets/img/sample/stream_barriers.png)
Stream barriers are injected into the parallel data flow at the stream sources. The point where the barriers for snapshot n are injected (let’s call it Sn) is the position in the source stream up to which the snapshot covers the data. For example, in Apache Kafka, this position would be the last record’s offset in the partition. This position Sn is reported to the checkpoint coordinator (Flink’s JobManager).
The barriers then flow downstream. When an intermediate operator has received a barrier for snapshot n from all of its input streams, it emits a barrier for snapshot n into all of its outgoing streams. Once a sink operator (the end of a streaming DAG) has received the barrier n from all of its input streams, it acknowledges that snapshot n to the checkpoint coordinator. After all sinks have acknowledged a snapshot, it is considered completed.
Once snapshot n has been completed, the job will never again ask the source for records from before Sn, since at that point these records (and their descendant records) will have passed through the entire data flow topology.
![](../assets/img/sample/stream_aligning.png)
#### Snapshotting Operator State
![](../assets/img/sample/checkpointing.png)
The resulting snapshot contains:
For each parallel stream data source, the offset/position in the stream when the snapshot was started
For each operator, a pointer to the state that was stored as part of the snapshot
#### Unaligned Checkpointing
![](../assets/img/sample/stream_unaligning.png)
ABS（Asynchronous Barrier Snapshotting）
The figure depicts how an operator handles unaligned checkpoint barriers:

The operator reacts on the first barrier that is stored in its input buffers.
It immediately forwards the barrier to the downstream operator by adding it to the end of the output buffers.
The operator marks all overtaken records to be stored asynchronously and creates a snapshot of its own state.
Consequently, the operator only briefly stops the processing of input to mark the buffers, forwards the barrier, and creates the snapshot of the other state.

Unaligned checkpointing ensures that barriers are arriving at the sink as fast as possible. It’s especially suited for applications with at least one slow moving data path, where alignment times can reach hours. However, since it’s adding additional I/O pressure, it doesn’t help when the I/O to the state backends is the bottleneck.
### State Backends
- **The MemoryStateBackend**
The MemoryStateBackend holds data internally as objects on the Java heap. Key/value state and window operators hold hash tables that store the values, triggers, etc.
Upon checkpoints, this state backend will snapshot the state and send it as part of the checkpoint acknowledgement messages to the JobManager, which stores it on its heap as well.
1. Limitations of the MemoryStateBackend:
The size of each individual state is by default limited to 5 MB. This value can be increased in the constructor of the MemoryStateBackend.
Irrespective of the configured maximal state size, the state cannot be larger than the akka frame size (see Configuration).
The aggregate state must fit into the JobManager memory.

2. The MemoryStateBackend is encouraged for:
Local development and debugging
Jobs that do hold little state, such as jobs that consist only of record-at-a-time functions (Map, FlatMap, Filter, …). The Kafka Consumer requires very little state.
- **FsStateBackend**
The FsStateBackend holds in-flight data in the TaskManager’s memory. Upon checkpointing, it writes state snapshots into files in the configured file system and directory. Minimal metadata is stored in the JobManager’s memory (or, in high-availability mode, in the metadata checkpoint).
The FsStateBackend is encouraged for:
Jobs with large state, long windows, large key/value states.
All high-availability setups.
```
// Setting the Per-job State Backend
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
env.setStateBackend(new FsStateBackend("hdfs://namenode:40010/flink/checkpoints"));
```
- **RocksDBStateBackend**
The RocksDBStateBackend holds in-flight data in a RocksDB database that is (per default) stored in the TaskManager data directories. Upon checkpointing, the whole RocksDB database will be checkpointed into the configured file system and directory. Minimal metadata is stored in the JobManager’s memory (or, in high-availability mode, in the metadata checkpoint).
The RocksDBStateBackend always performs asynchronous snapshots.

1. Limitations of the RocksDBStateBackend:
As RocksDB’s JNI bridge API is based on byte[], the maximum supported size per key and per value is 2^31 bytes each. IMPORTANT: states that use merge operations in RocksDB (e.g. ListState) can silently accumulate value sizes > 2^31 bytes and will then fail on their next retrieval. This is currently a limitation of RocksDB JNI.
The RocksDBStateBackend is encouraged for:
2. Jobs with very large state, long windows, large key/value states.
All high-availability setups.
```
# The backend that will be used to store operator state checkpoints
state.backend: filesystem

# Directory for storing checkpoints
state.checkpoints.dir: hdfs://namenode:40010/flink/checkpoints
```
### Savepoints
All programs that use checkpointing can resume execution from a savepoint. Savepoints allow both updating your programs and your Flink cluster without losing any state.
Savepoints are manually triggered checkpoints, which take a snapshot of the program and write it out to a state backend. They rely on the regular checkpointing mechanism for this.
Savepoints are similar to checkpoints except that they are triggered by the user and don’t automatically expire when newer checkpoints are completed.

### Exactly Once vs. At Least Once
[An Overview of End-to-End Exactly-Once Processing in Apache Flink](https://flink.apache.org/features/2018/03/01/end-to-end-exactly-once-apache-flink.html)
When we say “exactly-once semantics”, what we mean is that each incoming event affects the final results exactly once. Even in case of a machine or software failure, there’s no duplicate data and no data that goes unprocessed.

# Timely Stream Processing
## Event Time and Processing Time
- **Processing time**: Processing time refers to the system time of the machine that is executing the respective operation.
Processing time is the simplest notion of time and requires no coordination between streams and machines. It provides the best performance and the lowest latency. However, in distributed and asynchronous environments processing time does not provide determinism, because it is susceptible to the speed at which records arrive in the system (for example from the message queue), to the speed at which the records flow between operators inside the system, and to outages (scheduled, or otherwise).

- **Event time**: Event time is the time that each individual event occurred on its producing device. Event time programs must specify how to generate Event Time Watermarks, which is the mechanism that signals progress in event time.
In a perfect world, event time processing would yield completely consistent and deterministic results, regardless of when events arrive, or their ordering. However, unless the events are known to arrive in-order (by timestamp), event time processing incurs some latency while waiting for out-of-order events. As it is only possible to wait for a finite period of time, this places a limit on how deterministic event time applications can be.
Assuming all of the data has arrived, event time operations will behave as expected, and produce correct and consistent results even when working with out-of-order or late events, or when reprocessing historic data. For example, an hourly event time window will contain all records that carry an event timestamp that falls into that hour, regardless of the order in which they arrive, or when they are processed. (See the section on late events for more information.)
Event time has several benefits over processing time. First of all, it decouples the program semantics from the actual serving speed of the source and the processing performance of system. Hence you can process historic data, which is served at maximum speed, and continuously produced data with the same program. It also prevents semantically incorrect results in case of backpressure or delays due to failure recovery. Second, event time windows compute correct results, even if events arrive out-of-order of their timestamp which is common if a data stream gathers events from distributed sources.

## Event Time and Watermarks
Out of order messages can be caused by delay, backpressure.
The mechanism in Flink to measure progress in event time is `watermarks`.
Watermarks flow as part of the data stream and carry a timestamp t. A Watermark(t) declares that event time has reached time t in that stream, meaning that there should be no more elements from the stream with a timestamp t’ <= t (i.e. events with timestamps older or equal to the watermark).
![](../assets/img/sample/stream_watermark_in_order.png)
Watermarks are crucial for out-of-order streams, as illustrated below, where the events are not ordered by their timestamps. In general a watermark is a declaration that by that point in the stream, all events up to a certain timestamp should have arrived. Once a watermark reaches an operator, the operator can advance its internal event time clock to the value of the watermark.
![](../assets/img/sample/stream_watermark_out_of_order.png)

## Watermarks in Parallel Streams
Watermarks are generated at, or directly after, source functions. Each parallel subtask of a source function usually generates its watermarks independently. These watermarks define the event time at that particular parallel source.
As the watermarks flow through the streaming program, they advance the event time at the operators where they arrive. Whenever an operator advances its event time, it generates a new watermark downstream for its successor operators.
Some operators consume multiple input streams; a union, for example, or operators following a keyBy(…) or partition(…) function. Such an operator’s current event time is the minimum of its input streams’ event times. As its input streams update their event times, so does the operator.
![](../assets/img/sample/parallel_streams_watermarks.png)

## Windowing
[Introducing Stream Windows in Apache Flink](https://flink.apache.org/news/2015/12/04/Introducing-windows.html)
- tumbling windows (no overlap)

![](../assets/img/sample/window-tumbling-window.png)

- sliding windows (with overlap)

![](../assets/img/sample/window-sliding-window.png)
- session windows (punctuated by a gap of inactivity).
Windows on a full stream are called *AllWindows* in Flink. In Flink, we call such partitioned windows simply Windows, as they are the common case for distributed streams.

![](../assets/img/sample/windows-keyed.png)

### Allowed Lateness
When working with event-time windowing, it can happen that elements arrive late, i.e. the watermark that Flink uses to keep track of the progress of event-time is already past the end timestamp of a window to which an element belongs.
By default, late elements are dropped when the watermark is past the end of the window. However, Flink allows to specify a maximum allowed lateness for window operators. Allowed lateness specifies by how much time elements can be late before they are dropped, and its default value is 0. Elements that arrive after the watermark has passed the end of the window but before it passes the end of the window plus the allowed lateness, are still added to the window.

### Dissecting Flink’s windowing mechanics

![](../assets/img/sample/window-mechanics.png)
Elements that arrive at a window operator are handed to a *WindowAssigner*. The WindowAssigner assigns elements to one or more windows, possibly creating new windows. A Window itself is just an identifier for a list of elements and may provide some optional meta information, such as begin and end time in case of a TimeWindow.

Each window owns a *Trigger* that decides when the window is evaluated or purged. The trigger is called for each element that is inserted into the window and when a previously registered timer times out.

When a Trigger fires, the list of window elements can be given to an optional Evictor. The evictor can iterate through the list and decide to cut off some elements from the start of the list, i.e., remove some of the elements that entered the window first. The remaining elements are given to an evaluation function. If no Evictor was defined, the Trigger hands all the window elements directly to the evaluation function.

The evaluation function receives the elements of a window (possibly filtered by an Evictor) and computes one or more result elements for the window. The DataStream API accepts different types of evaluation functions, including predefined aggregation functions such as sum(), min(), max(), as well as a ReduceFunction, FoldFunction, or WindowFunction.

## BackPressure
[Backpressure](https://flink.apache.org/2020/10/15/from-aligned-to-unaligned-checkpoints-part-1.html) refers to the behavior where a slow receiver (e.g. of data/requests) makes the senders slow down in order to not overwhelm the receiver, something that can result in possibly dropping some of the processed data or requests. This is a crucial and very much desirable behavior for systems where completeness/correctness is important. Backpressure is implicitly implemented in many of the most basic building blocks of distributed communication, such as TCP Flow Control, bounded (blocking) I/O queues, poll-based consumers, etc.

Apache Flink implements backpressure across the entire data flow graph. A sink that (temporarily) cannot keep up with the data rate will result in the source connectors slowing down and pulling data out of the source systems more slowly. We believe that this is a good and desirable behavior, because backpressure is not only necessary in order to avoid overwhelming the memory of a receiver (thread), but can also prevent different stages of the streaming application from drifting apart too far.


# Interview Question
- How does Flink fault tolerant work?
Periodically checkpoint the Oprator State and KeyedState to the StateBackend.
- flink的时间形式和窗口形式有几种？有什么区别，你们用在什么场景下的？

- 如何保证Exactly Once
1. 开启checkpoint
2. source支持数据重发
3. sink支持事务，可以分2次提交，如kafka；或者sink支持幂等，可以覆盖之前写入的数据，如redis
幂等性：就是用户对于同一操作发起的一次请求或者多次请求的结果是一致的，不会因为多次点击而产生了副作用。

- flink on yarn执行流程
