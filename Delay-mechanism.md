The existing Delay class has a few issues.

1. When processes are being suspended/resumed the Morphic interCyclePause: can get stuck in 
Delay's AccessProtect mutex critical section [Case 13755](https://pharo.fogbugz.com/default.asp?13755). 
<br><br>
It seems changing thread safety from mutex based to semaphore based fixes this problem.

1. Delay works intermittently when invoked from the command line on a virtual 
machine [Case 14353](https://pharo.fogbugz.com/default.asp?14353).  
<br><br> 
It seems removing the clock rollover check by moving the timer event loop from 
milliseconds to microseconds fixes this problem.

1. The restart of the timer event loop by ImageCleaner carries forward old suspended delays.  
This makes it harder to change the implementation, such as from milliseconds to microseconds. 
<br><br>
It seems to me that after ImageCleaner, suspended delay should be empty.

1. Delay uses all shared class variables for the timer event loop, making it hard to create tests 
that don't affect the live system.

So I propose... 

1. When the delay scheduling timer event loop exits, the suspended delays heap should 
be cleared by signalling all those delays.

1. To refactor the timer event loop code away from the class-side of Delay, to the instance-side of 
its own class DelayScheduler.  

1. Further refactor DelayScheduler to pull the mutex code out to a subclass DelayMutexScheduler.

1. Provide additional thread safety mechanisms subclassed from DelayScheduler (e.g. DelaySemaphoreScheduler, DelaySpinlockScheduler)
with a way to switch between them on the fly (but not move suspended delays between them, just clearing
them when the event loop of the previous scheduler exits)

1. Change the timer event loop from milliseconds to microseconds. Change the semantics of the instance 
variable **resumptionTime** from millisconds to microseconds. Not sure whether instance variable 
**delayDuration** shouldbe  similarly changed, or remain as milliseconds.

1. For now, don't add DelayScheduler to the snapshot startup list.  Just forward calls received by 
Delay onto DelayScheduler.
