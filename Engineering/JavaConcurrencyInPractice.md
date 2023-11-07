- **Chapter-1: Introduction**       
    - Threads allow multiple streams of program control flow to coexist within a process.
      They share process-wide resources such as memory and file handles, but
      each thread has its own program counter, stack, and local variables.
    - Threads are sometimes called lightweight processes, and most modern operating
      systems treat threads, not processes, as the basic units of scheduling.
    - **Risks of threads**  
        - Safety hazards    
        - Liveness hazards  
        - Performance hazards   
            
##Part I. Fundamentals  
    
- **Chapter-2: Thread Safety**       
    
##Part II. Structuring concurrent applications

- **Chapter-6: Task Execution**       
    - Applications should exhibit both _good throughput_ and _good responsiveness_ under normal load and should exhibit _graceful degradation_ as they become overloaded, rather than simply falling over under heavy load.     
    - Choosing good task boundaries, coupled with a sensible task execution policy can help achieve these goals.    
    - Executor provides a standard means of decoupling task submission from task execution, describing tasks with Runnable. 
    - Using an Executor is usually the easiest path to implementing a producer-consumer design in your application.
    - **The real performance payoff of dividing a program’s workload into tasks comes when there are a large number of independent, homogeneous tasks that can be processed concurrently.**     
    - Many tasks are effectively deferred computations—executing a database query, fetching a resource over the network, or computing a complicated function. For these types of tasks, Callable is a better abstraction: it expects that the main entry point, call, will return a value and anticipates that it might throw an exception.
    - Future represents the lifecycle of a task and provides methods to test whether the task has completed or been cancelled, retrieve its result, and cancel the task. Implicit in the specification of Future is that task lifecycle can only move forwards, not backwards—just like the ExecutorService lifecycle. Once a task is completed, it stays in that state forever.
    - CompletionService combines the functionality of an Executor and a BlockingQueue. You can submit Callable tasks to it for execution and use the queuelike methods take and poll to retrieve completed results, packaged as Futures, as they become available. ExecutorCompletionService implements Completion- Service, delegating the computation to an Executor.

- **Chapter-7: Cancellation and Shutdown**
    -        
    
##Part III. Liveness Performance and Testing

##Part IV. Advanced Topics