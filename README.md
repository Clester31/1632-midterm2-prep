# WRITING TESTABLE CODE

### Testable Code Strategies

* Segment Code - Make it modular
  * Methods should perform one well defined functionality
  * Leads to better reusability, better readbility, better maintainability, etc.
  * Can lead to more testable code
 
* DRY - Don't Repeat Yourself
  * Don't copy and paste code
  * Don't have multiple methods with similar functionality
  * Leads to bloated codebase
  * Higher change to encounter bugs
  * Less testable code (more testing needed to be done)
 
* How to avoid repeating yourself
  * For duplicate code, create a new method and call that instead
  * For two or more similar methods, merge them into one
  * Make use of polymorphism, subclasses, inheritence, generics
 
* Move Test Unfriendly Features (TUFs) out of Test Unfriendly Constructs (TUCs)
  * TUFs = features you want to fake using stubs
    * Printing to console, reading/writing to filesystem or database, etc.
  * TUCs = methods that are hard to fake using stubbing or overriding
    * Constructors, private/final/static methods
  * **Don't put code that you want to fake inside methods that are hard to fake**
 
* Make it easy to satisfy preconditions
  * **Dependence on external data is bad for testing**
    * global variables, datbase values/files, anything that isn't passed in as an argument (side-effects)
  * Bad Example:
  ```java
  public float getCatWeight(Cat cat) {
    int fishWeight = Fish.weight;
    // Because the cat ate the fish
    return cat.weight + fishWeight;
  }
  ```
  * Good Example:
  ```java
  public float getCatWeight(Cat cat, int fishWeight) { // pass in fishweight as argument
    return cat.weight + fishWeight; 
  }
  ```

  * Make it easy to reproduce
    * **Dependence on random data is bad for testing**
      * Makes it impossible to reproduce result
  * Bad example:
  ```java
  public Result playOverUnder() {
    // random throw of the dice
    int dieRoll = (new Die()).roll();
    if (dieRoll > 3) {
      return RESULT_OVER;
    }
    else {
      return RESULT_UNDER;
    }
  }
  ```
  * Good example:
  ```java
  public Result playOverUnder() {
    int dieRoll = d.roll();
    if (dieRoll > 3) {
      return RESULT_OVER;
    }
    else {
      return RESULT_UNDER;
    }
  }
  ```
  
* Make it easy to localize
  * Dependency injection: Passing a dependent object as argument (rather than building it internally)
    * Makes testing easier by allowing you to mock that object
    * Allows for decoupling of the two classes
   
### Dealing with Legacy Code   

* Pinning Tests
  * Tests done to pin down existing behavior in legacy code
  * Make sure to pin down all behaviors and bugs before modifying
  * Done during unit testing
 
* Seams
  * Place where two code modules meet where one can be switched for another without having to modify source code
  * Seam is a place where fake objects can be injected to localize the unit tests
 
# Performance Testing

### Different Definitions of Speed

  * Throughput: Given certain *resources*, amount of *workload* system can handle per time unit
    * How many web pages can a web server service per second   
  * Utillization: Given a *workload*, amount of *resources* system uses
    * How many CPU clock ticks are needed to service a web page   
  * Responsiveness: How quickly system responds to a user request
    * How long does a web page take to load? 10ms? 100ms?
   
### Performance Indicators

  * Quantitative measures of the performance of a system under test
    * How long does it take to respond to a button press?
    * How many users can the system handle at one time?
  * Key Performance Indicators (KPI): A performance indicator important to the user
  * Performance Target: Quantitative measure that KPI should reach ideally
  * Performance Threshold: Bare minimum a KPI should reach

### Performance Indicator Sub-Categories

* Service Oriented
  * Measures how well a system is providing a service to the users
    * Measures QOS
    * Often codified in SLA
  * Two subcaregories:
    * Response Time, Availability
   
* Efficiency Oriented
  * Measures how efficiently a system makes use of system resources
    * CPU time, memory space, battery liufe, network bandwith
  * Two sub-categories: Utilization, Throughput
 
### Statistical Reasoning

* Reponse time needs statistical reasoning because other processes can run while testing, taking up both CPU Time and memory. This can lead to varying results when testing

### Types of Time

* Real Time (Wall Clock) - "Actual" amount of time taken
* User Time - Amount of time user code executes on CPUI
* System Time - Amount of time kernel (OS) code executes on CPU
* Total Time - User time + system time = **CPU utilization**

* High proportion of user time?
  * Lots of time spent running user (application) code. Need to optimize algorithm or use efficient data structure
* High proportion of kernel time?
  * Lots of time spent in OS to handle system calls or interrupts. Reduce frequency of system calls or investigate source of interrupts
* High proportion of idle time?
  * Alot of time spent wating for I/O
 
### Nines Notation

* Relates to how many nines of reliability is provided
* 99% = 2 nines, 99.9% = 3 nines, 99.99% = 4 nines...

### Why availability is hard to measure directly

* Not feasible to run a few "test years" before developing
* Modeling usage and estimating uptime is the only feasible approach

### Baseline, soak, stress, loading

* Baseline Test - Test run under normal conditions
* Soak / Stability Test - Typical usage for extended periods of time
* Stress Test - High levels of activity typically in short bursts
* Load Test - Given a load, how long can a system run without failing

* We can use these tests to estimate mean time between failures
  * If 90% of time is typical usage, 10% of time is peak useage
  * MTBF = Soak Test MTBF * 0.9 + Stress Test MTBF * 0.1
 
### Values of Efficiency-oriented indicators

* CPU Utilization
  * Number of CPU clock ticks needed to handle request
  * Translates to response time, given a certain number of CPUs with a certain clock rate
* Memory Utilization
  * Bytes of memory needed to handle request
  * Translates to availability, given a certain amount of memory and a flood of requests
* Server throughput
  * Maximum number of requests handled per second 
  * Translates to both response time and availability, given a certain number of servers
 
### General Purpose vs Program

* General Purpose
  * Windows System - Task Manager, perform
  * OS X - Activity Monitor, Instruments, top
  * Unix systems - top, iostat, sar, perf, time
 
* Protocol Analyzers
  * Wireshark or tcpdump
* Profiler
  * JProfiler, VisualVM, gprof
 
# Stochastic / Property-Based Testing

### Sochastic Testing

* Testing using randomly generated input values

### Property-based Testing

* Testing correctness of properties of observed values
  * A property is something that must invariably hold true in observed values
  * **Properties** aloow testing of any random input value
 
* Can check behavior without being provided expected output values
  * This enables stochastic testing
 
### PBT Pros and Cons

* pros
  * can check behavior without beig provided expected output values
  * Leades coder to think about invariants and improve code
* Cons
  * Does not always guarantee that each output value will be correct
 
### Invariant

* Another name for a property, or something that must invaiably hold true in observed values
* example: a = b + c;
  ```
  Assuming b > 0 && c > 0, a > 0 always holds true
  Assuming b < 0 && c < 0, a < 0 always holds true
  ```


### Shrinking

* FInding the smallest possible input that triggers a failure
* Helps track down the actual issue

```
[9, 0, -6, -5, 14] -> [0, -6, -5, 9, 14] FAIL
[9, 0, -6] -> [0, -6, 9] FAIL
[-6, -5, 14] -> [-6, -5, 14] OK
[9, 0] -> [0, 9] OK
[0, -6] -> [0, -6] FAIL
[0] -> [0] OK
[-6] -> [-6] OK
Shrunk Failure: [0, -6] -> [0, -6]
```

### Fuzz Testing

* A form of stochastic testing with a focus on "byte stream" inputs
* Feeds a program with "fuzzy" or "noisy" byte streams to see if it exposes a defect or security vulnerability

### Why completely random input is ineffective

* 99.99% of random files will fail a certain check since they have some form of integrety and structure
