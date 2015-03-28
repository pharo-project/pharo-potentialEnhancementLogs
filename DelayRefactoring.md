# Delay refactoring completed for Pharo 4 

1. Previously Delay scheduling was implemented on the class-side of Delay, such that any unit tests would interfere with the live system, and the live system would interfere with unit tests - so there were none.  

   For Pharo 4, the delay scheduling was refactored to the instance-side of its own class DelayScheduler, so that isolated instances can be used in the unit tests. [Case 14261](https://pharo.fogbugz.com/default.asp?14261)

2. Previously Delays were scheduled using the millisecond clock that rolls over about every 6 days.  However the clock can drift backwards in OS level virtual machines (e.g. Xen, VirtualBox, VMWare), which interacted poorly with the millisecond clock rollover checks in the delay scheduling.  

   For Pharo 4, DelayMicrosecondScheduler was added which makes use of the microsecond clock that rolls over about every 50,000 years, such that the clock rollover code eliminated.  This was made the default delay scheduler. The old implementation was moved to DelayMillisecondScheduler. [Case 14353](https://pharo.fogbugz.com/default.asp?14353). 

3. Previously when ImageCleaner would stop and start the delay scheduling, it carried forward scheduled Delays.  This was deemed adverse its purpose of reseting the system, and indeed prevented the live transfer from the millisecond scheduler to the microsecond scheduler.  

   In Pharo 4, stopping the delay scheduler expires and signals all outstanding delays.

4. Previously the Morphic UI loop interacted badly with high frequency suspend/resume of background worker threads. The AccessProtect mutex would get stuck waiting in a critical section.  

    In Pharo 4, this can be avoided in by optionally selecting DelayExperimentalSemaphore as the delay scheduler.  However since this was added late, without broad testing, it retains the "Experimental" tag.  Also added were some other possible solutions DelayExperimentalSpinScheduler and DelayExperimentalCourageousScheduler. [Case 13755](https://pharo.fogbugz.com/default.asp?13755). 

5. Previously when the delay scheduler was not running, the UI loop would lock up stuck in WorldState>>interCyclePause waiting for a delay that was never scheduled.  Introduced Delay>>waitOtherwise: which does not block if its delay is not scheduled, instead continuing execution as if there was no delay.

# Further work for Pharo 5

1. Consider the snapshot startup list replacing Delay with DelayScheduler.

2. Consider changing how the delay scheduling is paused during a snapshot. The VM randomly signals the timingSemaphore, and this must not wake up any code, which could interfer with the snapshot. AccessProtect is still referenced in the shutdown/startup sequence, which does not align with the experimental delay schedulers making AccessProtect redundant. Some possibilities:
   * placing a new pausedForSnapshotSemaphore check in the timer event loop 
   * have an inner and outer timer event loop.  The inner loop exits prior to snapshot and then waits on pausedForSnapshotSemaphore. When unpaused the outer loop re-enters the inner loop. 

3. Consider activating the DelayExperimental__Schedulers in turn for periods of time so they get some real life workout.  
