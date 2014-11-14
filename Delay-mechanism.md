The existing Delay class has a few issues.

1. When processes are being suspended/resumed the Morphic interCyclePause: can get stuck in 
Delay's AccessProtect mutex critical section [Case 13755](https://pharo.fogbugz.com/default.asp?13755). 

  A fix has been found which changes inter-thread synchronisation from mutex to semaphore based.

1. Delay works intermittently when invoked from the command line on a virtual 
machine [Case 14353](https://pharo.fogbugz.com/default.asp?14353). 

  A fix has been found that removes clock rollover checks by changing from milliseconds to microseconds.

1. The restart of the timer event loop by ImageCleaner carries forward old suspended delays. 

  This makes it harder to change the implementation from milliseconds to microseconds. Also, lack of clock rollover means a Delay scheuled in several weeks time can remain after an image clean (e.g. and end up in a Release.)  

1. Delay uses all shared class variables for the timer event loop, making it harder to create tests 
that don't affect the live system.

To hopefully make it easier to follow, I propose to implement this in parts... 

1. [Case 14261](https://pharo.fogbugz.com/default.asp?14261) - To refactor the timer event loop code away from the class-side of Delay, to the instance-side of 
its own class DelayScheduler.  

  When the timer event loop exits (e.g. by ImageCleaner), any outstanding delays should be expired by signalling those delays.  
2. [Case 14353](https://pharo.fogbugz.com/default.asp?14353) - Change the timer event loop from milliseconds to microseconds. Change the semantics of the instance variable **resumptionTime** from millisconds to microseconds. Not sure whether instance variable **delayDuration** should be  similarly changed, or remain as milliseconds.

3. [Case 14344](https://pharo.fogbugz.com/default.asp?14344) - Further refactor DelayScheduler to pull the mutex code out to a subclass DelayMutexScheduler.

  Provide additional thread safety mechanisms subclassed from DelayScheduler (e.g. DelaySemaphoreScheduler, DelaySpinlockScheduler) with a way to switch between them on the fly. Suspended delays should not be moved across a change of schedulers (since this should be infrequent) just expire them when the event loop of the previous scheduler exits.

4. Maybe replace Delay with DelayScheduler in the snapshot startup list.  Not sure yet. 
For now just forward  #startUp/#shutDown calls received by Delay onto DelayScheduler.
