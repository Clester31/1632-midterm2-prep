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

# Non-Determinism and Software QA

### Be able to explain what nondeterminisitc software is

* Nondeterministic software is when the output of the program is not determined by its input
* Deterministic programs produce the same output on the same input

### Difficulties of nondeterminism

* Defects not revealed during testing can suddenly pop up during usage
* Defects revealed during testing do not show up when trying to debug them
 * This leads to unreproducable results

### Nondeterminism and debugging issues

* Some bugs can appear randomly on certaint tests, making it difficult to pin down why these bugs are occuring or where they are occuring

### Memory Errors

* malloc() will return a random address when called (to prevent hackers from guessing memory layout)
* Stack addresses are also randomized as well for the same reasons
* ASLR - Address Space Layout Randomization

* Memory errors are errors that access an illegal memory location
 * Buffer overflow or dangling pointer
 * Can lead to memory leaks

### Data Race

* When multiple operations are not executed in the correct order, leading to unexpected outcomes
* The order in which data is access or stored can vary on each iteration of the software

# Static Testing Part 1

### Pros and cons of static testing compared to dynamic testing

* Pros
 * No need to come up with test cases
 * Don't need to set up software or hardware to run the program
 * Can pinpoint defects better than dynamic testing
 * Can find defects that dynamic testing would miss (dynamic tests are limited to their test cases)
* Cons
 * Does not find all defects
 * Can lead to false positives

### Why Language Choice is important for Compiler Static Analysis

 * The more semantic information is exposed, the more effective analysis (i.e. typed vs non-typed languages)
 * Easier to debug if you know what type something is

### Code Coverage

 * Code coverage is how much of the codebase is covered by a particular test suite

 * Method Coverage: Contains the entire method
 * Statement coverage: Contains the statements inside of a method, but not the entire methods
 * Branch Coverage: % of branch directions covered (if statements)
 * Condition Coverage: % of boolean expressions covered
 * Path Coverage: % of paths in the method covered
 * Parameter value coverage: % of (common) parameter values covered
 * Entry/Exit coverage: % of method calls/returns covered

### Why does 100% code coverage != defect free?

* Some things cannot be caught
 * Defects triggered by input values that cover the same paths
 * Defects on a path with covered statements but was never traversed

### Similarities and differences of Linters and Bug Finders

* Linters: Enable a team to use the same coding style (indentation, naming, comment formatting, etc)
* Bug Finders: Looks for patterns that are common signs of defects
 * Catches both false positives and false negatives
 * Pattern Matches can signal defects, confusing code, performance issues, etc.

* Similarities:
 * Can detect patterns in code that can help programmer to make necessary changes
* Differences:
 * Linters are only concerned with styling consistency, while bug finders can point out potential errors in the code

# Static Testing Part 2   

### Formal Verification

* Proving that a program either has no defects, or has defects (and knowing where they all are)

### How Theorem proving seeks to achieve formal verification

* Theorem proving is deducing postconditions form preconditions through math 

### How Model checking seeks to achieve formal verification

* Model checking is when given a finite state model of a system, all states are exhaustively checked to see if model meets a given specification
* We are checking whether implementation satisfies specifications

### Pros / Cons of theorem proving compared to model checking

* Pros:
    * Can prove large programs with many (infinite states)
    * Model checker needs to visit each state to verify property is true
    * Leads to programmer having a deeper understanding of a program
* Cons:
    * Requires a lot of human involvement
    * Highly skilled people with formal methods training is needed  

### Similarities and differences of model checking compared to property based testing

* Similarities
    * Model checking also tests a property instead of an output value
* Differences
    * Stochastic testing makes use of a few randomized input values
    * In Model checking, all states are checkd exhaustively      

### How backtracking and state matching both help in making state space exploration efficient for model checkers

* Backtracking and state matching help ensure that each unique state is vistied once. When the next state matches a previously vistied state (match), we can backtrack to repeat work

### State Explosion

* Non-trivial programs have many more states in their finite state machines that can lead to memory limitations or time limitatiosn

# Static Testing Part 3

### How does symbolic execution reduce size of state space

* Symbolic execution is assigning symbolic expressions instead of actual values to variables during execution
    * Instead of x = 1, y = true... we do x = A + 1, y = A * b
    * Model checker can prove through math that it will always pass for every input value without having to try them one by one 

### Role constraint solver plays in symbolic execution

* Constraint solvers will determine whether a path is infesible, if it is, it will ont continue down that pathj

### Deficiencies of symbolic execution and why it cannot be easily applied to all programs

* Can sometimes run into issues when dealing with loops, complex math constraints, or complex data structures
* Constraint solving is NP-complete in genreal
* They can also struggle with complex data structures

# Pairwise / Combinatorial Testing

### NIST study on the frequency of defects for various numbers of variable interactions

* Q: Do defects really occur as a result of interactions between variables?
* Q: If so, how many variables are typically involved in a defect

* Defects do occur as a result of interactions between variables
* At max, just six variables are involved in a defect
* Majority of defects are found just by testing all possible pairs

### Define pairwise and combinatorial testing

* Pairwise Testing:
    * Testing all possible pairs of interactions ("all pairs")
    * Testing all possible interactions within a pair
 
* Combinatorial Testing
    * Related to testing interactions between t variables from entire set of variables
    * An instance of combinatorial testing where t = 2

### Covering array

* A set of test cases covering all t-way combinations

# Smoke / Exploratory Testing

### Why smoke testing can help the QA team work more efficiently

* Minimal testing to ensure that the system is ready for serious testing
* Sets up software / hardware testing environemnt
* No need to spend the effort if the system isn't ready for prime time
* Avoids wasting tester's time

### Other names for smoke testing

* Confidence testing, Sanity testing, Build Verification Testing

### Build verification vs unit testing during TDD

* BVT is a form of regression test that checks whether program has regressed due to recent changes
* Includes integration tests, unit tests, and basic quality control

### BVT Triggering and its tradeoffs

* It's run when new code has been commited and a new build created

### Results of BVT pass or fail

* Pass
    * Notify QA team by automated email for further testing
* Fail
    * code repository is in a broken state now
    * Nobody can add code to the repository until something is done about it
    * Developer's responsiblity to immediately revert repository to a safe version and patch the bug    

### Why BVT must be fast

* Critical path of development. A BVT fail leads to all changes needing to be reverted and no commits can be done
* Every minute saved in BVT is a minute saved in the development cycle

### Where exploratory tests may be needed

* Exploratory tests are tests done without a test plan
* Done so the QA team can learn more about the system and influence its development, while also forumulating a test plan

### Pros and cons of explanatory Testing

* Pros
    * Fast: can focus on finding defects quickly
    * Flexible: No overhead in updating tests
    * Improves testers knowledge 
* Cons
    * Unregulated: Quality depends heavily on tester
    * Unrepeatable: Defects may not be reproducible
    * Unknown coverage: Hard to tell coverage after testing
    * Hard to automate: Needs a knowledgable human being to do it

# Security Testing

### Infosec (CIA) triad

* Information Security Triad
    * Three attributes that define a system that provides information security
    * summarized by CIA 

### Be able to tell which of the triad a particular attacks tries to compromise (confidentiality, integrity, availability)

* Confidentiality - No unauthorized users may read data
* Integrity - No unauthorized users may write data
* Availibility - System is availble for reading or writing

* Attacks on condifentialty: Interception
    * Eavesdropping, keylogging, phishing
* Attacks on integrity: Modification / fabrication
    * Malware that formats hard disk of computer, or fills it with garbage data
    * Digital signature forgery: Electronic messages are "signed" using a digital signature to prove authenticity and integrity. The message is modified and the signature is forged
* Attacks on availibility: Interruption
    * DoS (denial of service), power grid attack  

### Vulnerability, exploit, attack

* Vulnerability: Weakness of a system
* Explot: Mechanism for compromising a system
* Attack: Actual compromising of the system

### differences between types of malicious code

* Spyware: surreptitiously monitors your actions
* Adware: Shows you ads when browsing a webpage
* Ransomware: threatens to publish data or block access until a ransom is payed
* Bacteria: Program that consumes system resources (fork bomb)
* DoS program that floods a server with messages to shut it down

### 8 Common types of attacks

* Logic Bomb: hidden come within program that sets off attack when triggered
* Trapdoor: secret undocumented access to a system or app
* Trojan horse: program that pretends to be another program
* Virus: replicates itself WITH human intervention
* Worm: replicates itself WITHOUT human intervention
* Zombie: Computer or program being run by an unauthorized controller
* Bot network - collection of zombies controlled by a master

* Injection attacks
* Cross-Site Scripting
* Insecure Object Refernce Exploit
* Buffer overflow
* Broken authentication exploit
* Security misconfiguration exploit
* Insecure storage exploit
* social engineering

### Same Origin Policy

* Web browser sandboxing architecture
    * Webpage can access data in another webpage only if from same URL origin 

### How cross-site scripting cirucmvents SOP

* Allows malicious website to execute Javascript code
    * Accross site boundaries
    * Ignoring SOP protections 
