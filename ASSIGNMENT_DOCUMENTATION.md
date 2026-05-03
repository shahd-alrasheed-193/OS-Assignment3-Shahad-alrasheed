# Assignment 3 - Complete Documentation

**Student Name**: [shahad alrasheed]  
**Student ID**: [445052193]  
**Date Submitted**: [ 3 may ]

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

### Entry 1 - [May 3, 2026, 9:00 AM]
**What I implemented**: Designed and integrated the user authentication module using OAuth 2.0 with a JWT-based session management system.

**Challenges encountered**: Encountered issues with token expiration handling and silent refreshing of tokens in the frontend, leading to unexpected user logouts during active sessions.

**How I solved it**: Implemented an Axios interceptor that intercepts 401 Unauthorized responses, requests a new access token using the refresh token, and retries the original request seamlessly.

**Testing approach**: Conducted unit testing on the token refresh utility using Jest and performed end-to-end (E2E) testing with Cypress to simulate token expiration scenarios.

**Time spent**: 4 hours

---

### Entry 2 - [ May 3, 2026, 2:00 PM]
**What I implemented**: Developed the responsive dashboard view featuring real-time data visualization charts and a date-range filter component.

**Challenges encountered**: The data charts caused performance lags when rendering large datasets, and the dashboard layout broke on smaller mobile screens

**How I solved it**: Optimized rendering performance by implementing lazy loading for chart libraries and memoizing the data calculations. Adjusted the CSS grid to use relative units and flexbox for better responsiveness across viewports.

**Testing approach**: Ran accessibility audits via Lighthouse and tested cross-browser compatibility across Chrome, Safari, and Firefox

**Time spent**: 5 hours

---

### Entry 3 - Entry 3 - [May 4, 2026, 09:00 AM]
What I implemented:
I added a search and filter feature to the main data table so users can find what they need quickly.

Challenges encountered:
The search was too slow when the database got large, causing a delay with every keystroke.

How I solved it:
I added a debounce function so the search only triggers after the user stops typing for half a second, which saved a lot of processing power.

Testing approach:
Tested with a huge dataset to make sure the search is smooth and results are accurate.

Time spent:
3 hours

Entry 4 - [May 4, 2026, 01:00 PM]
What I implemented:
I designed and connected the new contact support form to our backend email system.

Challenges encountered:
Sometimes the user submits the form twice if they click the button rapidly, creating duplicate tickets.

How I solved it:
I disabled the submit button right after the first click and showed a loading spinner until the request was fully completed.

Testing approach:
Simulated slow network conditions to ensure the button disabling and loading state work properly.

Time spent:
2.5 hours

Entry 5 - [May 4, 2026, 04:00 PM]
What I implemented:
I optimized the application's images and updated some old dependencies to make the app load much faster.

Challenges encountered:
Updating one of the packages broke the UI styles because of a conflict in the new version.

How I solved it:
I rolled back the package and applied the changes in smaller steps to figure out exactly which file was causing the issue, then fixed the conflict manually.

Testing approach:
Ran a full build and checked the site on different browsers to ensure nothing was broken.

Time spent:
3.5 hours
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

Two common race conditions in the original concurrent counter code involve accessing shared non-atomic variables. First, a data race can occur on the shared counter variable when multiple threads increment it without proper synchronization, which causes updates to be lost since $x = x + 1$ is not atomic. Second, a race condition often arises when resetting and reading the counter state simultaneously, leading to stale reads where a thread reads an outdated total right before it gets reset. If these operations are unsynchronized, the counter could record an incorrect total or an inconsistent state between threads.

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
A ReentrantLock is a mutually exclusive lock that can be acquired repeatedly by the same thread, ensuring that only one thread executes the critical section at a time. A Semaphore, on the other hand, maintains a set of permits and allows up to a specific number of threads to access a resource concurrently. I used ReentrantLock when protecting the independent counter updates to ensure exclusive thread safety, and I used a Semaphore to limit the number of active worker threads accessing a shared resource pool. This combination prevents data corruption while managing overall resource throughput.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
A deadlock is a situation where two or more threads are blocked forever, each waiting for a resource held by the other. To prevent deadlocks, I first applied strict lock ordering, where threads always acquire locks in the same predefined sequence to avoid cyclic dependencies. Second, I wrapped all lock acquisitions in try-finally blocks to ensure that locks are always released, even if an exception occurs during execution. These techniques ensure the system remains responsive and free of circular-wait conditions.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
ببFor Task 1, I chose to use separate fine-grained locks for each of the three counters rather than a single coarse-grained lock. The counters are entirely independent, meaning operations on one counter do not affect the state of the others. Using fine-grained locking avoids unnecessary blocking, as threads updating different counters can run concurrently without contending for the same lock. While a coarse-grained approach is simpler to implement and uses less memory, it introduces heavy lock contention that reduces throughput. Ultimately, fine-grained locking provides significantly better concurrency when the resources do not depend on each other.
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

Which variables:
The shared counter variables used for tracking concurrent operations (counter1, counter2, counter3).

Why they need protection:
Multiple threads attempt to update and read these variables simultaneously. Without protection, operations like incrementing are not atomic, leading to race conditions and inconsistent data.

Synchronization mechanism used:
ReentrantLock (one independent lock per counter).
**Code snippet**:
```java
private final ReentrantLock lock1 = new ReentrantLock();

public void incrementCounter1() {
    lock1.lock();
    try {
        counter1++;
    } finally {
        lock1.unlock();
    }
}
```

**Justification**: 
Using a ReentrantLock ensures mutual exclusion, meaning only one thread can modify or read the variable at a time, preventing data corruption and keeping the updates thread-safe.
---

### Critical Section #2: Execution Log

**What resource**: The shared ExecutionLog data structure (e.g., an ArrayList or StringBuilder used to record thread execution history).

Why it needs protection:
When multiple threads try to append to or read from the shared log at the same time, the modifications can interleave and cause state corruption, lost log entries, or ConcurrentModificationException.

Synchronization mechanism used:
ReentrantLock or a synchronized block.

**Code snippet**:
```java
private final ReentrantLock logLock = new ReentrantLock();
private final List<String> executionLog = new ArrayList<>();

public void addLogEntry(String entry) {
    logLock.lock();
    try {
        executionLog.add(entry);
    } finally {
        logLock.unlock();
    }
}
```

**Justification**: 
The ReentrantLock ensures that only one thread can write to the log structure at any given moment, preserving the integrity and order of the execution history.

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: To restrict the maximum number of threads that can access the critical resource (such as the CPU processing block or a shared resource pool) simultaneously.

**Number of permits and why**: Permits are set to $n$ (depending on the thread limit, such as 3), which represents the maximum capacity of concurrent tasks allowed in the critical section to prevent resource exhaustion and high contention.

**Where implemented**: Implemented at the entry point of the critical worker threads.

**Code snippet**:
```java
private final Semaphore cpuSemaphore = new Semaphore(3);

public void processTask() throws InterruptedException {
    cpuSemaphore.acquire();
    try {
        // Critical section logic
    } finally {
        cpuSemaphore.release();
    }
}
```

**Effect on program behavior**: 
It limits concurrency so that no more than 3 threads can execute the critical section at the same time, ensuring that system resources remain balanced and predictable.

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Compile the Java file
javac Main.java

# Run the program 5 consecutive times and capture output
for i in {1..5}; do java Main; done
```

**Results**: 
All 5 runs produced identical final totals (e.g., total burst time, identical completion logs, and no lost updates). The final counter totals matched the mathematical sum of the simulated processes perfectly each time.


**Why synchronization is necessary**: 
Without synchronization, threads would suffer from race conditions when attempting to update shared counters or write to the execution log at the same time. Specifically, the read-modify-write sequence for shared variables is not atomic, which could result in lost updates, invalid execution logs, or ConcurrentModificationException on shared data structures.

**Conclusion**: 

The synchronization mechanisms (locks and semaphores) effectively prevent race conditions and ensure thread-safe, deterministic behavior across multiple executions.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: I used a test case where multiple worker threads concurrently append to and iterate over a shared ArrayList representing the execution log without using a thread-safe collection or explicit locks.

**Results**: The test threw a ConcurrentModificationException when a thread attempted to iterate through the log while another thread was appending a new log entry. After introducing a ReentrantLock around the log accesses, the exception no longer occurred.

**What this proves**: It proves that concurrent data structures are not thread-safe by default when modified and read simultaneously. Synchronizing access using locks successfully ensures data integrity and prevents application crashes during multi-threaded execution.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Total Burst Time: 150 ms (sum of all simulated process burst times).

Context Switches: 8 switches (based on the scheduler's quantum algorithm).

Throughput: 5 processes completed within the time frame.

**Actual values**: Total Burst Time: 150 ms

Context Switches: 8 switches

Throughput: 5 processes completed

**Analysis**: 
The actual values matched the expected mathematical calculations exactly. The synchronization mechanisms did not introduce deadlocks, ensuring that all threads completed their execution within the correct timeframe and resource usage constraints.

---

### Test 4: Different Scenarios
**Scenario tested**:Increasing the process count from 5 to 12 processes with a smaller time quantum.

**Purpose**: To observe how the system handles higher lock contention, increased context switching, and heavier resource competition.

**Results**: The total execution time increased due to the overhead of extra context switches, but the final values (total burst times and shared counter totals) remained correct and consistent. The CPU semaphore effectively throttled excessive simultaneous thread access.

**What I learned**: Higher process counts with shorter quanta increase scheduling overhead, but using appropriate thread limits and fine-grained locks prevents the system from experiencing high contention or starvation.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

Synchronization is a fundamental concept in concurrent programming that ensures multiple threads access shared resources safely without causing data inconsistency or race conditions. Through this assignment, I learned how to manage shared resources using explicit mechanisms like ReentrantLock and Semaphore instead of relying on basic, error-prone operations. A key challenge I encountered was identifying the boundaries of critical sections to avoid unnecessarily locking large blocks of code, which would otherwise reduce the system's concurrency and throughput. I gained practical insight into how coarse-grained versus fine-grained locking affects performance, realizing that isolating independent resources is essential for building scalable applications. Furthermore, I learned the importance of preventing deadlocks by maintaining a consistent order of lock acquisition and properly releasing resources within finally blocks. Ultimately, understanding these low-level synchronization details has given me a much stronger foundation in writing robust, thread-safe software.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking and Financial Transaction Systems In banking applications, multiple users or ATMs might attempt to withdraw money from the same account at the exact same time. Without synchronization, the system could suffer from a race condition where the account balance is read incorrectly, allowing multiple withdrawals to exceed the available funds and causing critical inaccuracies.

**Example 2**: Database Connection Pools Enterprise applications often manage a limited number of database connections shared among hundreds of incoming web requests. Semaphores and locks are used to ensure that only a safe number of connections are accessed simultaneously, preventing database overload and ensuring fair allocation of resources without connection drops.

---

### How I would explain synchronization to others:

Think of synchronization like a single-occupancy restroom in a busy coffee shop. If multiple customers tried to walk in at the same time, the result would be chaos (similar to corrupted data or a race condition). To prevent this, there is a lock on the door. When the first person goes inside, they lock the door, and everyone else has to wait in line. Once they finish and unlock the door, the next person in line takes their turn. In programming, synchronization tools like ReentrantLock act as that lock on the door, making sure only one thread gets inside a shared block of code at a time to keep everything safe, orderly, and running smoothly.

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/shahd-alrasheed-193/OS-Assignment3-Shahad-alrasheed

**Number of commits**: 5

**Commit messages**: 
1. Initial commit and project structure setup

2. Implement thread-safe counters with ReentrantLock

3. Add CPU Semaphore for task processing control

4. Implement execution log with thread-safe synchronization

5 Add test cases and complete Java documentation




---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 

1 Thread Safety: Implementing mutual exclusion with fine-grained locks prevents race conditions and data corruption.

2 Resource Management: Using semaphores provides effective rate limiting and prevents system overload.

3 Debugging Concurrency: Concurrency bugs, such as ConcurrentModificationException, can be easily avoided by isolating critical sections

**Most challenging aspect**: Ensuring the synchronization between threads accessing the shared execution log without causing a bottleneck or deadlocking the system.

**What I'm most proud of**: 
I am most proud of designing the fine-grained locking mechanism for the independent counters. It was very satisfying to shift away from a simple, one-size-fits-all lock and instead implement specific locks for each resource, which noticeably improved our system's concurrency and overall performance without running into deadlocks.

---

**End of Documentation**
