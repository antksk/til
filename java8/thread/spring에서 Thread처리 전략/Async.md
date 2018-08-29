# @Async
org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor
- jdk 1.5에서 처음 등장한 ThreadPoolExecutor를 사용함 
- @Async가 붙은 메소드를 멀티스레드 처리 대상이 됨
- 기본 설정은 다음과 같음
```java
public class ThreadPoolTaskExecutor extends ExecutorConfigurationSupport
		implements AsyncListenableTaskExecutor, SchedulingTaskExecutor {

	private final Object poolSizeMonitor = new Object();

	private int corePoolSize = 1;

	private int maxPoolSize = Integer.MAX_VALUE;

	private int keepAliveSeconds = 60;

	private int queueCapacity = Integer.MAX_VALUE;
}
```
- queueCapcity 값이 지정되면 다음과 같이 내부적으로 LinkedBlockingQueue를 사용함
```java
public class ThreadPoolTaskExecutor extends ExecutorConfigurationSupport
		implements AsyncListenableTaskExecutor, SchedulingTaskExecutor {
	/**
	 * Create the BlockingQueue to use for the ThreadPoolExecutor.
	 * <p>A LinkedBlockingQueue instance will be created for a positive
	 * capacity value; a SynchronousQueue else.
	 * @param queueCapacity the specified queue capacity
	 * @return the BlockingQueue instance
	 * @see java.util.concurrent.LinkedBlockingQueue
	 * @see java.util.concurrent.SynchronousQueue
	 */
	protected BlockingQueue<Runnable> createQueue(int queueCapacity) {
		if (queueCapacity > 0) {
			return new LinkedBlockingQueue<Runnable>(queueCapacity);
		}
		else {
			return new SynchronousQueue<Runnable>();
		}
	}
}
```
- 내부적으로 ```ThreadPoolExecutor```클래스를 참조 함
- 처리 프로세스는 다음과 같음
    1. 처음엔 큐 사이즈(queueCapacity)만큼 스레드 생성을 대기
    2. 큐 사이즈를 넘어 서면
    3. corePoolSize 기준으로 최대 maxPoolSize 만큼 스레드를 생성 지속적으로 생성함
```java
public class ThreadPoolExecutor extends AbstractExecutorService {
    /**
     * Sets the core number of threads.  This overrides any value set
     * in the constructor.  If the new value is smaller than the
     * current value, excess existing threads will be terminated when
     * they next become idle.  If larger, new threads will, if needed,
     * be started to execute any queued tasks.
     *
     * @param corePoolSize the new core size
     * @throws IllegalArgumentException if {@code corePoolSize < 0}
     * @see #getCorePoolSize
     */
    public void setCorePoolSize(int corePoolSize) {
        if (corePoolSize < 0)
            throw new IllegalArgumentException();
        int delta = corePoolSize - this.corePoolSize;
        this.corePoolSize = corePoolSize;
        if (workerCountOf(ctl.get()) > corePoolSize)
            interruptIdleWorkers();
        else if (delta > 0) {
            // We don't really know how many new threads are "needed".
            // As a heuristic, prestart enough new workers (up to new
            // core size) to handle the current number of tasks in
            // queue, but stop if queue becomes empty while doing so.
            int k = Math.min(delta, workQueue.size());
            while (k-- > 0 && addWorker(null, true)) {
                if (workQueue.isEmpty())
                    break;
            }
        }
    }
    
       /**
         * Checks if a new worker can be added with respect to current
         * pool state and the given bound (either core or maximum). If so,
         * the worker count is adjusted accordingly, and, if possible, a
         * new worker is created and started, running firstTask as its
         * first task. This method returns false if the pool is stopped or
         * eligible to shut down. It also returns false if the thread
         * factory fails to create a thread when asked.  If the thread
         * creation fails, either due to the thread factory returning
         * null, or due to an exception (typically OutOfMemoryError in
         * Thread.start()), we roll back cleanly.
         *
         * @param firstTask the task the new thread should run first (or
         * null if none). Workers are created with an initial first task
         * (in method execute()) to bypass queuing when there are fewer
         * than corePoolSize threads (in which case we always start one),
         * or when the queue is full (in which case we must bypass queue).
         * Initially idle threads are usually created via
         * prestartCoreThread or to replace other dying workers.
         *
         * @param core if true use corePoolSize as bound, else
         * maximumPoolSize. (A boolean indicator is used here rather than a
         * value to ensure reads of fresh values after checking other pool
         * state).
         * @return true if successful
         */
        private boolean addWorker(Runnable firstTask, boolean core) {
            retry:
            for (;;) {
                int c = ctl.get();
                int rs = runStateOf(c);
    
                // Check if queue empty only if necessary.
                if (rs >= SHUTDOWN &&
                    ! (rs == SHUTDOWN &&
                       firstTask == null &&
                       ! workQueue.isEmpty()))
                    return false;
    
                for (;;) {
                    int wc = workerCountOf(c);
                    if (wc >= CAPACITY ||
                        wc >= (core ? corePoolSize : maximumPoolSize))
                        return false;
                    if (compareAndIncrementWorkerCount(c))
                        break retry;
                    c = ctl.get();  // Re-read ctl
                    if (runStateOf(c) != rs)
                        continue retry;
                    // else CAS failed due to workerCount change; retry inner loop
                }
            }
    
            boolean workerStarted = false;
            boolean workerAdded = false;
            Worker w = null;
            try {
                w = new Worker(firstTask);
                final Thread t = w.thread;
                if (t != null) {
                    final ReentrantLock mainLock = this.mainLock;
                    mainLock.lock();
                    try {
                        // Recheck while holding lock.
                        // Back out on ThreadFactory failure or if
                        // shut down before lock acquired.
                        int rs = runStateOf(ctl.get());
    
                        if (rs < SHUTDOWN ||
                            (rs == SHUTDOWN && firstTask == null)) {
                            if (t.isAlive()) // precheck that t is startable
                                throw new IllegalThreadStateException();
                            workers.add(w);
                            int s = workers.size();
                            if (s > largestPoolSize)
                                largestPoolSize = s;
                            workerAdded = true;
                        }
                    } finally {
                        mainLock.unlock();
                    }
                    if (workerAdded) {
                        t.start();
                        workerStarted = true;
                    }
                }
            } finally {
                if (! workerStarted)
                    addWorkerFailed(w);
            }
            return workerStarted;
        }
}
```