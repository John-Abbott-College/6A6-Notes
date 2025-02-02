# Asynchronous programming



By default, C# code is run in a synchronous way or on a single thread. This means that each line of code is executed one at a time in a sequential manner. The execution of one line is blocking for the subsequent line. By default only one thread, which is the smallest sequence of programmed instructions that the OS will allocate processor time. 

The idea behind asynchronous programming is to let the execution continue even if one particular step is taking time. 

The idea behind multithreading is to use multiple threads to execute computationally intensive code in parallel.

## Starting analogy: Baking a cake

The CPU is a little cook in box who is trying to bake a cake. The cook will perform the steps in sequence and waits for each task to be done before moving on to the next one:

<img src="images/async/baking_cake_sync.png"/>



You can see that this strategy is probably not the most efficient because of the idle time waiting until the oven's job is done. 

A better strategy would be to do things in parallel: 

<img src="images/async/baking_cake_async.png" height=300/>

While waiting for the oven to warm up, the little cook can continue working on his cake. This reduces the idle time where the book isn't doing much. 

## Multithreading

When using multithreading, multiple code units that are running independently. 

- Ideal for computationally intensive applications ("CPU bound operations")
- Makes use of the processing powers of modern CPUs which have multiple cores.
- Example of applications: Image Analysis, Video games scene rendering,  recursive calculations, etc.

| Figure1: Single-threaded  Programming                        |
| ------------------------------------------------------------ |
| <img  src="images/async/ch1_Singlethreaded.png"  Width=800/> |

| Figure2: Multi-threaded Programming                      |
| -------------------------------------------------------- |
| <img  src="images/async/ch1_Mutlthread.png"  Width=800/> |

**Single core CPUs:**

- Single cores are still popular in virtual machines, embedded systems, IoT devices or lower power devices. 

# Asynchronous Programming

- Asynchrony indicates that two or more processes are able to operate independently, and in some cases simultaneously, without any constraints on the relative timing of their execution.

- Asynchronous programming is a programming paradigm that allows for the execution of multiple `tasks` simultaneously.

  - The paradigm is particularly important in client-server architectures.

- This enables programmers to write parallel code, which can take advantage of multiple processors or processor cores.

  

| Figure1: Synchronous Programming                             |
| ------------------------------------------------------------ |
| <img  src="images/async/ch1_Singlethreaded.png"  Width=800/> |



| Figure1: Asynchronous Programming                           |
| ----------------------------------------------------------- |
| <img  src="images/async/ch1_Asynchronous.png"  Height=450/> |



### When to use Async programming over multi-threading?

If the app is waiting for a value because of a ***I/O-bound*** operation, a value to from a webserver, filesystem or a database, then there is no need for a dedicated thread to sit around and wait for the data when it can be servicing other requests. 

- Creating threads in a program is often undesirable and unnecessary.
- Threads are the most low-level constructs of multithreading and working with them could be challenging and creates an overhead. 
- Threads take time to be started and use a lot of memory (in the order of MB).
- If badly implemented, multi-threading can increase the complexity and cause race conditions (parts of the code executed more than expected). Bugs can be very hard to debug and seem almost random. 
- It is much more expensive to create a thread than re-using an existing thread from the thread pool. 

**So, is asynchronous programming single threaded but uses parallelism? How is it possible?**

- It's not necessarily single threaded, but it mostly is.
- It will favour the use of a single thread unless a task is complete while the main thread is busy running something else, then it might use any other available task to signal the completion of the async task. 
- It the background implementation of .NET C#, the keyword `async` is actually converting the asynchronous method into a state machine. The `await` acts as a checkpoint of the state of this machine. 



### Demo: Console App for Baking a Cake

One major difference between synchronous and asynchronous function calls is the nature of blocking threads.  Let's take the example of baking a cake in roughly 4 steps

1. Gathering the ingredients
2. Heating the oven
3. Mixing the ingredients, 
4. Baking the cake. 



```c#
namespace DemoAsync
{
    internal class Program
    {
        static void Main(string[] args)
        {
            var ingredients = GatherIngredients();
            MixIngredients(ingredients);
            HeatOven(250);
            BakingCake(30);
            CoolingCake(15);
        }
    
        // The rest of the code is in the Demos code repo
```

Now each one of those steps is going to take time although conceptually, we do not need to wait until all ingredients are gathered before starting to heat up the oven, nor do we need to wait until the oven is heated up to start mixing the ingredients. The problem with synchronous programing is the blocking nature of each execution. The complete code can be found in the Demos. 

Now there's multiple ways of improving the efficiency of this code. Let's firstly explore asynchronous programming, which allows the **execution** of some functions to be **non-blocking** of the main thread, so for example starting to heat up the oven, then moving up to the next step of mixing the ingredients while waiting for the oven to finish heating up. It's important to understand that it's still **single-threaded**, unless another thread is really needed. 



### `Task` Class and `async` and `await` Keywords

The Class `Task` and/or `Task<T`> were introduced in .NET to wrap any method into a unified interface that is useful for the method caller to monitor the **completion** of whatever operation the method was intended to do. What's essential to understand is that from the point of view of the caller, a task is giving us a "promise" of a variable but it won't have control over how and when the execution is completed. 

- A `Task` is a "running"  job that acts as a promise 

  - A `Task<T>` promises to return an object of type `T`, but not right away. 
  - Belong to the `System.Threading.Tasks` namespace.

- A `Task` is a higher level abstraction 

  - Capable of returning a value.
  - Compositional in nature. 
  - Can use continuations and chain any amount of tasks together. 
  - Very useful for I/O bound operations. 

- NOTE: A `Task` is not a `Thread`, it's run inside a thread but it's not the same concept. It's much more light weight and typically runs one method.

- Tasks provides error handling  

  - Each `Task` once completed will have a state:
    - Ran to Completion
    - Cancelled
    - Faulted. 
  - Uses `AggregateException` to wrap  up any other exception that might occur.
    - `AggregateException` provides a Flatten  method to check all exceptions.

  

The ***async*** and ***await*** keywords were created in C# to makes creating asynchronous code almost as easily as you create synchronous code. You can use resources in .NET framework in the same exact way. 

- `async` and `await` keywords were introduced in version 5 of C# (2012).
- The `async` keyword is used with method definitions and indicates an asynchronous method. It transforms the method into a class implementing the `IAsyncStateMachine` 
- The `await` keyword is used with method class and suspends a method until the awaited asynchronous operation is completed.

#### `Task.Wait()`:

The `.Wait()` method of a task is **blocking** and causes the async method to run synchronously. There are a few use cases where this is useful, for example in the case of running an operation on a background thread and needed to wait until it's done. This is not what we typically want to accomplish in application development, because we don't want our UI thread to get blocked.





Going back to our cake, the asynchronous method of baking a cake would have the following signature:

```C#
namespace DemoAsync
{    
    internal class Program
    {
        static async Task Main(string[] args)
        {
            var ingredientsTask = GatherIngredientsAsync();
            Task ovenTask = HeatOvenAsync(250);

            var ingredients = await ingredientsTask;
            var mixingTask = MixIngredientsAsync(ingredients);

            await mixingTask;
            await ovenTask;
            await BakingCakeAsync(30);
            await CoolingCakeAsync(15);

            Console.WriteLine("Baking async completed");
            await Task.Delay(1000);
        }
```



### Example of HTTP Clients 

The `async` and `await` keywords makes creating asynchronous code almost as easily as you create synchronous code. You can use resources in .NET framework in the same exact way. Here's an example from Microsoft, that we will focus on: 

```csharp
public async Task<int> GetUrlContentLengthAsync()
{
    var client = new HttpClient();
    /*
    GetStringAsync is an asynchronous methed to get a string value from the provided URL.
    The value will be provided sometime in the future. Any reserved resouces will be released
    continue processing.
    */
    Task<string> getStringTask = client.GetStringAsync("https://docs.microsoft.com/dotnet");

    FinishOtherOperations();
	/*
	This is the future time in my program where I will need to wait for the my promised values.
	Suspends a thread until a specific operation is completed
	*/
    string contents = await getStringTask;
    return contents.Length;
}

private void FinishOtherOperations()
{
    Console.WriteLine("Working...");
}
```







### Sources

[1] [Speed up your python program with concurrency](https://realpython.com/python-concurrency/)

[2] [What is asynchronous?](https://www.linkedin.com/learning/async-programming-in-c-sharp/what-is-asynchronous)

[3] [Threading in C#](https://www.linkedin.com/learning/threading-in-c-sharp/)

[4] [C# Async Programming - Part 1: Conceptual Background](https://www.youtube.com/watch?v=FIZVKteEFyk)

[5] [C# Async/Await/Task Explained (Deep Dive)](https://www.youtube.com/watch?v=il9gl8MH17s&t=1129s)

[6] [10 Best Practices in Async-Await Code in C# ](https://dateo-software.de/blog/best-practices-async-await).