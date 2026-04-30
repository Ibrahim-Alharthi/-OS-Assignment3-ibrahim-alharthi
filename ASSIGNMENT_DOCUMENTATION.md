# Assignment 3 - Complete Documentation

**Student Name**: [Ibrahim Abdulrahman Alharthi]  
**Student ID**: [445050024]  
**Date Submitted**: [2026\4\30]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [April 30, 9:00]
**What I implemented**: 
adding ReentrantLock to protect counter variables (contextSwitchCount,
completedProcessCount, totalWaitingTime)

**Challenges encountered**: 
impoting libaries
**How I solved it**: 
writing the variable with the class type and Force to import the libary 
**Testing approach**: 
i was ensure that is logical and as the giudeline
**Time spent**: 30-25 minute

---

### Entry 2 - [April 30, 9:30]
**What I implemented**: 
apply all locks to the counter varibles in the increment methods
**Challenges encountered**: 
none, very easy
**How I solved it**: 

**Testing approach**: 

**Time spent**: 
10-15 minutes
---

### Entry 3 - [April 30, 9:40-10:00]
**What I implemented**: 
apply semaphore acquiring and releasing to run , run to complete methods
**Challenges encountered**: 
diffuclt to figure the scope "{}" of try and catch , every apply to semaphore need to be in try and catch 
**How I solved it**: 
reading the code well , asked Ai to help me figure the scope of try and catch
**Testing approach**: 
run the code 
**Time spent**: 
30-45 minute
---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[There are two race conditions in the code: one in contextSwitchCount where multiple threads call SharedResources.incrementContextSwitch() at the same time which can cause incorrect counting, and another in executionLog where multiple threads call SharedResources.logExecution(name + "...") concurrently, which may lead to missing or corrupted log entries because the ArrayList is not thread-safe]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock is used in SharedResources to protect shared variables like contextSwitchCount in incrementContextSwitch() and executionLog in logExecution(), so only one thread can modify them at a time and avoid race conditions while Semaphore is used as CpuSemaphore in the run() and runToCompletion() methods, where we call SharedResources.CpuSemaphore.acquire() before a process starts and release() after it finishes, to limit how many processes can use the CPU at the same time]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock is when threads get stuck forever because each one is waiting for a resource that another thread is holding. This makes the program freeze,
One prevention technique is using try-finally blocks, in my code, I always release locks and semaphores inside finally, like CpuSemaphore.release(), so even if an error happens, the resource is always free
Another technique is consistent locking order and avoiding nested locks. In my SharedResources methods, each lock (like contextSwitchLock and LogLock) is used separately in a simple way, so threads don’t wait on multiple locks at the same time, This helps prevent deadlocks and keeps the program running smoothly.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used separate locks for each counter like contextSwitchLock completedProcessCountLock and totalWaitingTimeLock because each counter is independent and does not affect the others

This gives better performance because more than one thread can update different counters at the same time without waiting for one lock

If I used one lock for all counters then only one thread can update anything at a time even if it is a different counter which makes it slower

So using separate locks is better because it allows more concurrency and faster execution]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount completedProcessCount totalWaitingTime
**Why they need protection**: 
Because they are shared between multiple threads and if more than one thread updates them at the same time the values can become wrong
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
contextSwitchLock.lock();
try {
    contextSwitchCount++;
} finally {
    contextSwitchLock.unlock();
}
completedProcessCountLock.lock();
try {
    completedProcessCount++;
} finally {
    completedProcessCountLock.unlock();
}
totalWaitingTimeLock.lock();
try {
    totalWaitingTime += time;
} finally {
    totalWaitingTimeLock.unlock();
}
```

**Justification**: 
I used locks so only one thread can update each counter at a time which prevents race conditions and keeps the values correct
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList)
**Why it needs protection**: 
Because multiple threads can write to it at the same time which can cause missing or corrupted log entries since ArrayList is not thread safe
**Synchronization mechanism used**: 
ReentrantLock (LogLock)
**Code snippet**:
```java
LogLock.lock();
try {
    executionLog.add(message);
} finally {
    LogLock.unlock();
}
```

**Justification**: 
I used a lock to make sure only one thread can add to the log at a time this prevents data loss or corruption in the execution log and keeps all log entries correct and in order
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To control how many processes can use the CPU at the same time
**Number of permits and why**: 
1 permit because we want only one process to execute in the CPU at a time to simulate real CPU scheduling safely
**Where implemented**: 
In Process.run() and Process.runToCompletion() using CpuSemaphore.acquire() and CpuSemaphore.release()
**Code snippet**:
```java
SharedResources.CpuSemaphore.acquire();

try {
    //execution code
} finally {
    SharedResources.CpuSemaphore.release();
}
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Run program multiple times
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
The output was consistent in all runs. The number of completed processes, context switches, and total waiting time were always correct and did not show random or incorrect values.
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Without synchronization, shared variables like contextSwitchCount, completedProcessCount, totalWaitingTime, and executionLog could be updated by multiple threads at the same time. This could cause wrong values, lost updates, or missing log entries because threads would interfere with each other.
**Conclusion**: 
Synchronization using locks and semaphores is necessary to ensure correct and consistent results when multiple threads are running at the same time.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
Run the program normally multiple times and observe execution log and shared counters during multithread execution
**Results**: 
No ConcurrentModificationException occurred during execution and the program ran successfully without crashes or errors.
**What this proves**: 
This proves that the shared resources are properly synchronized using locks and semaphores so multiple threads can access them safely without causing runtime exceptions or data corruption.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Total burst time = sum of all process burst times
Context switches = based on number of quantum switches
Completed processes = same as number of processes created
**Actual values**: 
The program output shows correct total context switches and all processes completed successfully, and the waiting time values are consistent across runs.
**Analysis**: 
The results match the expected behavior, which proves that synchronization using locks and semaphores is working correctly. Shared variables are updated safely without race conditions, so the final values are consistent and correct.
---

### Test 4: Different Scenarios
**Scenario tested**: [I tested the program using different number of processes and different time quantum values to see how the scheduler behaves under different conditions.]

**Purpose**: 
To check how the system performs when workload changes and to verify that synchronization still works correctly in all cases.
**Results**: 
The program worked correctly in all scenarios. When the number of processes increased, the execution time increased but the program still completed without errors. Changing the time quantum affected how often context switches happened, but the final results stayed correct.
**What I learned**: 
I learned that the scheduler behavior changes depending on time quantum and number of processes, but locks and semaphores keep the results correct and prevent race conditions even under different loads.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that synchronization is very important when working with multiple threads because they share the same data. Without locks or semaphores, threads can interfere with each other and cause wrong results. I understood how race conditions happen when two threads update the same variable at the same time. I also learned how ReentrantLock helps protect shared resources like counters and logs. Semaphores were useful to control how many processes can access the CPU at the same time. One challenge I faced was forgetting to release locks or semaphores which can cause the program to hang. Overall, I learned how proper synchronization makes multithreaded programs safe and reliable.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems where multiple users can access the same account at the same time like deposits and withdrawals so synchronization is needed to prevent wrong balances
**Example 2**: 
Online ticket booking systems like concerts or flights where many users try to book the same seat at the same time so synchronization is needed to avoid double booking
---

### How I would explain synchronization to others:

I would explain synchronization like a bathroom with only one key. If many people try to use it at the same time, they must take the key first so only one person can enter. In programming, threads are like those people and shared data is like the bathroom. Locks and semaphores are the key that controls who can access the shared data. Without this control, two threads might change the same data at the same time and cause wrong results. So synchronization is just a way to make threads take turns so everything stays correct.

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
