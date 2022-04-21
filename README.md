[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-f059dc9a6f8d3a56e377f745f24479a46679e63a5d9fe6f495e02850cd0d8118.svg)](https://classroom.github.com/online_ide?assignment_repo_id=7278570&assignment_repo_type=AssignmentRepo)
# CECS 326 Lab 2: Concurrent Processing and Shared Memory

## Assignment Description

The goal of this assignment is to become familiar with concurrent processing in Unix/Linux using [shared memory](https://man7.org/linux/man-pages/man7/shm_overview.7.html), become familiarized with man pages, and improve your ability to work with low-level operating system commands.

You will write a program that consists of three entirely separate executables:

1. A swim mill

2. A fish

3. A pellet

The `swim mill` keeps track of all of the processes, launches all of them, and is in charge of ending the program after 30 seconds.

The `fish` exists on the bottom row of the swim mill and only moves left and right. It constantly tries to find a pellet, and when it sees one that it believes it can reach, it will try to sit under that pellet until the pellet reaches the fish. If the fish eats a pellet, that pellet is removed from the swim mill. If the fish eats a pellet, please print the pellet's pid to stderr along with an appropriate message. If the fish does not see any pellets, it will sit idly in the center of the bottom row, waiting for pellets.

The `pellet` spawns at a random X and Y location in the swim mill (Although never on the same row as the fish). Every tick, it goes down one unit. If it reaches the bottom of the swim mill, it is removed from the swim mill. Print an appropriate message to stderr with the pellet's pid. You may choose to respawn the pellet after an arbitrary amount of time has passed from it being eaten or reaching the bottom of the swim mill.

 * HINT: If you have only one .c file, and not at least three, you will need to change your paradigm by the end.

In addition, you will create a working makefile, both for my convenience and your own. Ideally you should only type your cc/gcc commands ONCE for this entire project, and only inside of your makefile. After that, you should only need to type "make" into your console to compile all three executables.

Your different executables (processes) when run will all communicate important information about their states to each other. Our means of communication will be through a segment of shared memory. Bear in mind that if you do this assignment properly, you will only run ONE of these executables from the terminal, and it will launch all of your other processes.

In order to have all of your processes run concurrently, you will need to be able to both [launch](https://man7.org/linux/man-pages/man3/exec.3.html) and [kill](https://www.man7.org/linux/man-pages/man1/kill.1.html) all of them. To do this, you will need to set up a few things:
 - I should be able to terminate your program at any time *_gracefully_* by sending an [interrupt](https://man7.org/linux/man-pages/man3/siginterrupt.3.html) to your program. (Command/Ctrl + C on most computers, this will appear as a ^C in a terminal). I do recommend leaving this step for last, however, as until your program is able to handle interrupts cleanly, interrupting in this way may lead to unintended and possibly unpleasant results (Namely, unclean memory, or processes still running in the background).
 - Your program should end on its own after 30 seconds. The methods for doing this range from silly to elegant, but as long as it ends after approximately 30 seconds without leaving any processes still running, or shared memory segments hanging around, this requirement will be fulfilled.

Just printing text descriptions of what's happening is boring. For full credit, print an actual swim mill to the screen. While it is not necessary to implement it *exactly* like this, it should resemble this sort of visual design:

`----------`

`-----*----`

`----------`

`--*-------`

`-----F----`

Your swim mill must also spawn at least 18 pellet processes that run concurrently.

## What you need to know to succeed

Please read at a *MINIMUM* the following pages. You don't need to be meticulous about your reading, but at a minimum read the information that seems important, and be familiar with the pages. Then, answer the following questions. This is not graded, but knowing the answers to these questions will help you immensely when you actually start to code this assignment:
1. [shared memory](https://man7.org/linux/man-pages/man7/shm_overview.7.html) (and its related pages, at least the first three in the description. You may either use the POSIX shared memory implementation, or the [System V](https://man7.org/linux/man-pages/man2/shmget.2.html) implementation. If you choose to use the System V implementation, please read the pages linked in the [SEE ALSO](https://man7.org/linux/man-pages/man2/shmget.2.html#SEE_ALSO) section of [shmget](https://man7.org/linux/man-pages/man2/shmget.2.html).)

2. [fork()](https://man7.org/linux/man-pages/man2/fork.2.html)

3. [exec](https://man7.org/linux/man-pages/man3/exec.3.html) (This has many different functions that do effectively the same thing, but through different means. Pick your favorite.)

4. [sigaction](https://man7.org/linux/man-pages/man2/sigaction.2.html) (You may choose to use [signal](https://man7.org/linux/man-pages/man7/signal.7.html) instead)

5. [Makefile](https://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/) (You are only required to make a Makefile that uses what you learn up to the first Makefile iteration, but it is worth a read to go a bit further. You may either use multiple gcc compile commands in your first make rule, or you may create multiple make rules that are called by your first make rule. [More on that here.](https://makefiletutorial.com/#targets))

### questions you should answer: 
* Q1. In what order should you perform the actions to create [shared memory](https://man7.org/linux/man-pages/man7/shm_overview.7.html)? (HINT: A minimum of three functions must be used the first time you create shared memory)

* Q2. What is the return type of mmap, and what can you do with it? (If you are unfamiliar with C/C++, you might need to [do some personal research](https://en.cppreference.com/w/c) in order to understand this data type.)

* Q3. What does [fork()](https://man7.org/linux/man-pages/man2/fork.2.html) return, and how can that information be used?

* Q4. If [exec](https://man7.org/linux/man-pages/man3/exec.3.html) works as intended, what happens to the process that calls it?

* Q5. Do all three functions for shared memory need to be called in every single process after the first? If yes, why? If no, which ones are needed, and why would you not need to call all of them?

* Q6. If I have a pointer, what happens if I increment/decrement the pointer itself? What even is a pointer? How can I use it? Is there any difference between incrementing a char pointer and an int pointer?

* Q7. What kind of information is appropriate to put into shared memory? (Keep in mind that while you can have a decent amount of information in shared memory, it will be static (unchanging) in size, and should not include the kitchen sink)

## Timeline

While adherence to this timeline is not graded, you will be on-track if you meet or beat these deadlines:

Week 1: Create your makefile, and have your swim mill, pellet, and fish proccesses able to be compiled. They don't need to be finished yet, but at least have them in a runnable state. Also have some infrastructure in place to print to stderr.

Week 2: All three processes should be able to access shared memory and communicate any information to each other. Ensure that you're using fork and exec properly. You should also be able to spawn all of the required processes at this point.

Week 3: You should have figured out what kind of information you need to pass between processes, and have a working implementation of that communication.

Week 4: You should be able to print a visual representation of your swim mill, all processes should be able to read and write in shared memory, and all of the remaining requirements should be met.


## Grading scale:
### If the code is not commented, there is no makefile, there are no commits to github, or there are no source files, this is an automatic zero. You must have comments, you must have a Makefile, you must commit the assignment to GitHub, and you must have at minimum three source files.

+ 10% - Your code compiles and runs
+ 20% - You created shared memory correctly, and each of your processes is able to read from and write to the shared memory
+ 20% - You created at least 20 total processes
+ 15% - You print the grid correctly and output pellet information correctly
+ 20% - The program ends after 30 seconds, and can be interrupted with a signal interrupt
+ 15% - The program cleans up after itself, not leaving any shared memory behind

Partial credit may be given based on degree of success for any of the above

## A quick C refresher

Pointers - A pointer is only an address, on its own it does not contain any information. It must be given memory in some way. This is most often seen with [malloc](https://man7.org/linux/man-pages/man3/free.3.html), or its variations. Until initialized, pointers tend to seg fault when used. The information at the end of pointers is accessed with one of the following:
 - (*myPointer)
 - myPointer[index]
 - myPointer->someValue (this is mostly seen with structs)

Arrays - Arrays in C/C++ exist in two forms. Either pre-allocated, or dynamic. You may reassign individual values within pre-allocated arrays, but if you try to assign directly to a pre-allocated array, your program will in the best case either not compile or crash, and worst case will perform undefined behavior. Dynamic memory can be reassigned to, but you risk memory leaks if you do not [free](https://man7.org/linux/man-pages/man3/free.3p.html) your memory.

Pre-allocated arrays:
 - int myArr[10];

Dynamically allocated arrays:
 - int *myArr = malloc(sizeof(int) * 10);

[printf](https://man7.org/linux/man-pages/man3/printf.3.html) - This prints to the terminal by default. It uses string substitutions with %'s to format your string. It will look something like this:

`printf("my string: %s, my int: %d, my address: %p, my char: %c", someString, someInt, somePointer, someChar);`


## Recommendations:
 - I highly recommend that you do not try to create a 2D array in shared memory. 2D dynamic arrays are somewhat weighty to deal with in local memory, let alone shared memory.
 - Do not put your entire swim mill character array in shared memory. There are plenty of implementations that are acceptable, but this is not one of them.
 - Do not wait to start working. Sleep clears your mental state and allows you to look at your code with a fresh mind. You will likely need to refactor this assignment two or three times at least. This takes time, and is best not left until the day before the assignment is due.
 - While you can have all of these processes run without breaks, it won't lead to very interesting results. It might be worth experimenting with [sleep](https://man7.org/linux/man-pages/man3/sleep.3.html) or [usleep](https://man7.org/linux/man-pages/man3/usleep.3.html) to see what would make your swim mill look more animated. (Think like a game, and frame rates).
 - Really put some thought into what you might need to pass as information between processes. Try to think in terms of minimalism here. What is the BARE MINIMUM information my fish needs to know about a pellet to act? Where in my shared memory segment should I put a fish? A pellet? 10 pellets? Etc.
 - If you're having trouble using [srand](https://man7.org/linux/man-pages/man3/srand.3p.html) and [rand](https://man7.org/linux/man-pages/man3/rand.3.html) with multiple processes, meditate upon the following: How much time passes between processes being launched, and how can we introduce additional entropy to our random seed?

## Some miscellanous useful information:
### Helpful Linux/Unix terminal commands:
- [touch](https://man7.org/linux/man-pages/man1/touch.1.html) - to create your files
- [top](https://man7.org/linux/man-pages/man1/top.1.html) - for if you want to see if any errant processes are still running
- [kill](https://man7.org/linux/man-pages/man1/kill.1.html) - for if you find an errant process running
- [ipcs](https://man7.org/linux/man-pages/man1/ipcs.1.html) - to check for any shared memory not freed
- [ipcrm](https://man7.org/linux/man-pages/man1/ipcrm.1.html) - to clear any shared memory left after running your program
### Useful information if you get stuck:
- In order to compile on Unix/Linux machines, you may need to specify some compiler flags. Specifically -lrt needs to be near/at the end of your compile commands for working with shared memory.
- It's worth checking that you have included any headers that you need at the top of your source files. If you find yourself being told that you are using functions implicitly without defining them, this is probably the culprit.
- Order of function definitions matters in C. You can get around this, however, by *declaring* functions before using them. [More on that here](https://en.cppreference.com/w/c/language/functions). This is where a header file might come in handy.


 # previous content

 # this existed before my edits

 # this is left for comparison only












You will write a program that uses multiple processes to simulate a swim mill to show the behavior of a fish swimming upstream. The swim mill is represented as a one-dimensional array such that each element indicates the distance from the shore. The fish will occupy one and only one of these elements. The presence of a fish is indicated by changing the integer in the specified element. For example, we can represent the fish by the character `F`. By default, we will have the fish swimming right in the middle of the stream.

Somewhere upstream, a pellet is dropped into the stream. Our fish sees this pellet traveling towards it and moves sideways to enable its capture. If the pellet and the fish are in the same position at any point, the fish is said to have eaten the pellet. Of course, it is possible for our fish to miss the pellet if the fish cannot move close to it in time.

Different types of processes needed for this project are as follows:

* A set of `pellet` processes: Each pellet process drops a pellet at a random distance from the fish. We will call this process pellet. The pellet will start moving towards the fish with the flow of the river. For simplicity, we will assume that the pellet moves in a straight line downstream.
* A process called `fish`: the fish sees the pellet as it is dropped and moves one unit left or right to start aligning itself with the pellet. As the pellet moves towards the fish and the fish is at the same distance from the shore as the pellet, it stops changing its sideways position and devours the pellet when they are coincident. After eating the pellet, or if the pellet is missed, the fish returns to swimming midstream.
* A coordinator process named `swim_mill`: It is responsible for creating the fish process, the pellet processes, and coordinating the termination of pellets. We could have more than one pellet at any time. Note that the position of pellet and fish are maintained by their respective processes.

The `swim_mill` process also sets a timer at the start of computation to 30 seconds. If computation has not finished by this time, `swim_mill` kills all the children and grandchildren, and then exits. Make sure that you print appropriate message(s).

In addition, `swim_mill` should print a message when an [interrupt](https://man7.org/linux/man-pages/man3/siginterrupt.3.html) [signal](https://man7.org/linux/man-pages/man7/signal.7.html) `(^C)` is received. Make sure that all the children/grandchildren are [killed](https://www.man7.org/linux/man-pages/man1/kill.1.html) by `swim_mill` when this happens, and all the [shared memory](https://man7.org/linux/man-pages/man7/shm_overview.7.html) is deallocated. The grandchildren [kill](https://www.man7.org/linux/man-pages/man1/kill.1.html) themselves upon receiving [interrupt](https://man7.org/linux/man-pages/man3/siginterrupt.3.html) [signal](https://man7.org/linux/man-pages/man7/signal.7.html) but print a message on [stderr](https://man7.org/linux/man-pages/man3/stdout.3.html) to indicate that they are dying because of an [interrupt](https://man7.org/linux/man-pages/man3/siginterrupt.3.html), along with the identification information. Make sure that the processes handle multiple interrupts correctly. As a precaution, add this feature only after your program is well debugged.

### Implementation

The code for `pellet`, `fish`, and `swim_mill` processes should be compiled separately and the executables be named `pellet`, `fish`, and `swim_mill`, respectively. The program should be executed by calling: `swim_mill`.

Each `pellet` process prints its process id, its position and whether it was eaten or missed, before exiting. Each time the coordinator gets a result from a `pellet`, it prints the pid of the pellet.

`swim_mill` will set up the two-dimensional array in [shared memory](https://man7.org/linux/man-pages/man7/shm_overview.7.html), and write the result into a file after each child is finished. The fish will be restricted to move in the last row of the 2D array. The `swim_mill` process will create a pellet at random intervals, with the pellet being dropped into the swim mill at a random distance from the fish. The process pellet will increment its position and will terminate after the pellet has reached the last row, whether it is eaten or not.

The pellet can be represented by `0x80` and can be moved to location `(x,y)` by the statement `L[x][y]|= 0x80`. This will allow the `pellet` and `fish` to be able to coincide. The process of eating is performed by getting rid of the most significant bit to return the fish to original state.

The process fish will scan the entire array and will focus on a pellet if it detects one, to arrange itself in its line of drift. Meanwhile, if another pellet is found that happens to be closer, and eatable, the fish will go for it.

### Other points to remember

You are required to use [fork](https://man7.org/linux/man-pages/man2/fork.2.html), [exec](https://man7.org/linux/man-pages/man3/exec.3.html) (or one of its variants), [wait](https://man7.org/linux/man-pages/man2/wait.2.html), and [exit](https://man7.org/linux/man-pages/man3/exit.3.html) to manage multiple processes. Use [shmctl](https://man7.org/linux/man-pages/man2/shmctl.2.html) suite of calls for shared memory allocation. Also make sure that you do not have more than twenty processes in the system at any time. You can do this by keeping a counter in `swim_mill` that gets incremented by [fork](https://man7.org/linux/man-pages/man2/fork.2.html) and decremented by [wait](https://man7.org/linux/man-pages/man2/wait.2.html).

I’m also requiring the use of a [*makefile*](http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor) for your compilation. It will save you a lot of time compiling and re-compiling all three source code files and is very common for source code downloaded from FOSS projects and larger-scale, complex coding. A [*makefile*](http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor) is a simple script that contains all of the compilation executions and the options for each file’s compilation.

### Hints

You will need to set up shared memory in this project to allow the processes to communicate with each other. Please check the man pages for [shmget](https://man7.org/linux/man-pages/man2/shmget.2.html), [shmctl](https://man7.org/linux/man-pages/man2/shmctl.2.html), [shmat](https://man7.org/linux/man-pages/man2/shmat.2.html), and [shmdt](https://man7.org/linux/man-pages/man3/shmdt.3p.html) to work with shared memory.

You will also need to set up signal processing, and to do that, you will check on the functions for [signal](https://man7.org/linux/man-pages/man7/signal.7.html) and [abort](https://man7.org/linux/man-pages/man3/abort.3.html). If you abort a process, make sure that the parent cleans up any allocated shared memory before dying. In case you face problems, please use the shell command [ipcs](https://man7.org/linux/man-pages/man1/ipcs.1.html) to find out any shared memory allocated to you and free it by using [ipcrm](https://man7.org/linux/man-pages/man1/ipcrm.1.html).

You may also want to break down this assignment into parts in order to make it more manageable. First, focus on creating a coordinator process that sets up and can read and write to a shared memory location. Second, focus on getting a fish process that reads/writes to shared memory and can update its own location. Third, work on the pellet and its creation of random locations (pellet drops) and have it decrement its location through the shared memory. Finally, work on getting all three processes to coordinate and work in sync.

### Timeline

When I have given similar projects in the past, students often wait until the last minute to work on the assignment given that I give around four weeks to finish. **I highly caution against waiting on this one**: this project can be deceptively difficult in certain parts. While I am not strictly requiring a checkpoint for your project, you should have the coordinator process able to read and write to the shared memory within a week. The fish and pellet processes within another week, and finally the coordination of all three within a week or two.

## Deliverables

I will require the following items for grading:

* Your `*.c` source code files
* Your *makefile*
* At least one screenshot of your executed code, in `*.png` or `*.jpg` format

Submit your files through your git repository. Your submission must follow the following rules, else *I will not grade it and you will receive a zero for the submission*:

* Do not use compression on your files
* Make sure that all significant code is *commented* with your own explanations
