# HttpComponentsAsyncClientHttpRequestFactory
Max HTTP Conn 갯수 만큼 스레드를 생성하고, 서버쪽으로 요청을 진행하며,
Max HTTP Conn 갯수보다 많은 양의 요청이 들어 오게 되면 내부적으로 
this.leasingRequests = new LinkedList<LeaseRequest<T, C, E>>(); 와
this.completedRequests = new ConcurrentLinkedQueue<LeaseRequest<T, C, E>>()를 생성 후
아래와 같이, LeaseRequest객체를 생성하여, 각 요청을 connectionRequestTimeout 만큼 요청을 대기 시킨다.

org.apache.http.impl.nio.conn.PoolingNHttpClientConnectionManager.requestConnection 메서드 일부 내용
```java
/**
 * {@code PoolingNHttpClientConnectionManager} maintains a pool of
 * {@link NHttpClientConnection}s and is able to service connection requests
 * from multiple execution threads. Connections are pooled on a per route
 * basis. A request for a route which already the manager has persistent
 * connections for available in the pool will be services by leasing
 * a connection from the pool rather than creating a brand new connection.
 * <p>
 * {@code PoolingNHttpClientConnectionManager} maintains a maximum limit
 * of connection on a per route basis and in total. Per default this
 * implementation will create no more than than 2 concurrent connections
 * per given route and no more 20 connections in total. For many real-world
 * applications these limits may prove too constraining, especially if they
 * use HTTP as a transport protocol for their services. Connection limits,
 * however, can be adjusted using {@link ConnPoolControl} methods.
 *
 * @since 4.0
 */
@Contract(threading = ThreadingBehavior.SAFE)
public class PoolingNHttpClientConnectionManager
       implements NHttpClientConnectionManager, ConnPoolControl<HttpRoute> {
    @Override
    public Future<NHttpClientConnection> requestConnection(
            final HttpRoute route,
            final Object state,
            final long connectTimeout,
            final long leaseTimeout,
            final TimeUnit tunit,
            final FutureCallback<NHttpClientConnection> callback) {
        Args.notNull(route, "HTTP route");
        if (this.log.isDebugEnabled()) {
            this.log.debug("Connection request: " + format(route, state) + formatStats(route));
        }
        final BasicFuture<NHttpClientConnection> resultFuture = new BasicFuture<NHttpClientConnection>(callback);
        final HttpHost host;
        if (route.getProxyHost() != null) {
            host = route.getProxyHost();
        } else {
            host = route.getTargetHost();
        }
        final SchemeIOSessionStrategy sf = this.iosessionFactoryRegistry.lookup(
                host.getSchemeName());
        if (sf == null) {
            resultFuture.failed(new UnsupportedSchemeException(host.getSchemeName() +
                    " protocol is not supported"));
            return resultFuture;
        }
        final Future<CPoolEntry> leaseFuture = this.pool.lease(route, state,
                connectTimeout, leaseTimeout, tunit != null ? tunit : TimeUnit.MILLISECONDS,
                new FutureCallback<CPoolEntry>() {

                    @Override
                    public void completed(final CPoolEntry entry) {
                        Asserts.check(entry.getConnection() != null, "Pool entry with no connection");
                        if (log.isDebugEnabled()) {
                            log.debug("Connection leased: " + format(entry) + formatStats(entry.getRoute()));
                        }
                        final NHttpClientConnection managedConn = CPoolProxy.newProxy(entry);
                        if (!resultFuture.completed(managedConn)) {
                            pool.release(entry, true);
                        }
                    }

                    @Override
                    public void failed(final Exception ex) {
                        if (log.isDebugEnabled()) {
                            log.debug("Connection request failed", ex);
                        }
                        resultFuture.failed(ex);
                    }

                    @Override
                    public void cancelled() {
                        log.debug("Connection request cancelled");
                        resultFuture.cancel(true);
                    }

                });
        return new Future<NHttpClientConnection>() {

            @Override
            public boolean cancel(final boolean mayInterruptIfRunning) {
                try {
                    leaseFuture.cancel(mayInterruptIfRunning);
                } finally {
                    return resultFuture.cancel(mayInterruptIfRunning);
                }
            }

            @Override
            public boolean isCancelled() {
                return resultFuture.isCancelled();
            }

            @Override
            public boolean isDone() {
                return resultFuture.isDone();
            }

            @Override
            public NHttpClientConnection get() throws InterruptedException, ExecutionException {
                return resultFuture.get();
            }

            @Override
            public NHttpClientConnection get(final long timeout, final TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException {
                return resultFuture.get(timeout, unit);
            }

        };
    }
}
```
org.apache.http.nio.pool.AbstractNIOConnPool.lease 메서드
```java
/**
 * Abstract non-blocking connection pool.
 *
 * @param <T> route
 * @param <C> connection object
 * @param <E> pool entry
 *
 * @since 4.2
 */
@Contract(threading = ThreadingBehavior.SAFE_CONDITIONAL)
public abstract class AbstractNIOConnPool<T, C, E extends PoolEntry<T, C>>
                                                  implements ConnPool<T, E>, ConnPoolControl<T> {
    public Future<E> lease(
            final T route, final Object state,
            final long connectTimeout, final long leaseTimeout, final TimeUnit tunit,
            final FutureCallback<E> callback) {
        Args.notNull(route, "Route");
        Args.notNull(tunit, "Time unit");
        Asserts.check(!this.isShutDown.get(), "Connection pool shut down");
        final BasicFuture<E> future = new BasicFuture<E>(callback);
        this.lock.lock();
        try {
            final long timeout = connectTimeout > 0 ? tunit.toMillis(connectTimeout) : 0;
            final LeaseRequest<T, C, E> request = new LeaseRequest<T, C, E>(route, state, timeout, leaseTimeout, future);
            final boolean completed = processPendingRequest(request);
            if (!request.isDone() && !completed) {
                this.leasingRequests.add(request);
            }
            if (request.isDone()) {
                this.completedRequests.add(request);
            }
        } finally {
            this.lock.unlock();
        }
        fireCallbacks();
        return future;
    }
}
```

각 요청별로 생성된 LeaseRequest 객체는 connectionRequestTimeout 만큼 대기 하다가 처리가 되지 않으면 다음과 같이 
실패 처리(TimeoutException)된다.
```java
/**
 * Abstract non-blocking connection pool.
 *
 * @param <T> route
 * @param <C> connection object
 * @param <E> pool entry
 *
 * @since 4.2
 */
@Contract(threading = ThreadingBehavior.SAFE_CONDITIONAL)
public abstract class AbstractNIOConnPool<T, C, E extends PoolEntry<T, C>>
                                                  implements ConnPool<T, E>, ConnPoolControl<T> {
    private boolean processPendingRequest(final LeaseRequest<T, C, E> request) {
        final T route = request.getRoute();
        final Object state = request.getState();
        final long deadline = request.getDeadline();

        final long now = System.currentTimeMillis();
        if (now > deadline) {
            request.failed(new TimeoutException());
            return false;
        }

        final RouteSpecificPool<T, C, E> pool = getPool(route);
        E entry;
        for (;;) {
            entry = pool.getFree(state);
            if (entry == null) {
                break;
            }
            if (entry.isClosed() || entry.isExpired(System.currentTimeMillis())) {
                entry.close();
                this.available.remove(entry);
                pool.free(entry, false);
            } else {
                break;
            }
        }
        if (entry != null) {
            this.available.remove(entry);
            this.leased.add(entry);
            request.completed(entry);
            onReuse(entry);
            onLease(entry);
            return true;
        }

        // New connection is needed
        final int maxPerRoute = getMax(route);
        // Shrink the pool prior to allocating a new connection
        final int excess = Math.max(0, pool.getAllocatedCount() + 1 - maxPerRoute);
        if (excess > 0) {
            for (int i = 0; i < excess; i++) {
                final E lastUsed = pool.getLastUsed();
                if (lastUsed == null) {
                    break;
                }
                lastUsed.close();
                this.available.remove(lastUsed);
                pool.remove(lastUsed);
            }
        }

        if (pool.getAllocatedCount() < maxPerRoute) {
            final int totalUsed = this.pending.size() + this.leased.size();
            final int freeCapacity = Math.max(this.maxTotal - totalUsed, 0);
            if (freeCapacity == 0) {
                return false;
            }
            final int totalAvailable = this.available.size();
            if (totalAvailable > freeCapacity - 1) {
                if (!this.available.isEmpty()) {
                    final E lastUsed = this.available.removeLast();
                    lastUsed.close();
                    final RouteSpecificPool<T, C, E> otherpool = getPool(lastUsed.getRoute());
                    otherpool.remove(lastUsed);
                }
            }

            final SocketAddress localAddress;
            final SocketAddress remoteAddress;
            try {
                remoteAddress = this.addressResolver.resolveRemoteAddress(route);
                localAddress = this.addressResolver.resolveLocalAddress(route);
            } catch (final IOException ex) {
                request.failed(ex);
                return false;
            }

            final SessionRequest sessionRequest = this.ioreactor.connect(
                    remoteAddress, localAddress, route, this.sessionRequestCallback);
            final int timout = request.getConnectTimeout() < Integer.MAX_VALUE ?
                    (int) request.getConnectTimeout() : Integer.MAX_VALUE;
            sessionRequest.setConnectTimeout(timout);
            this.pending.add(sessionRequest);
            pool.addPending(sessionRequest, request.getFuture());
            return true;
        } else {
            return false;
        }
    }
}
```


this.leasingRequests 에 대기 하고 있는 요청들은 다음과 같은 메서드를 통해서 메번 소비 된다.
processPendingRequest에서 소비 되고 나면, completedRequests 대기 큐에 저장된다.
```java
@Contract(threading = ThreadingBehavior.SAFE_CONDITIONAL)
public abstract class AbstractNIOConnPool<T, C, E extends PoolEntry<T, C>>
                                                  implements ConnPool<T, E>, ConnPoolControl<T> {
    private void processPendingRequests() {
        final ListIterator<LeaseRequest<T, C, E>> it = this.leasingRequests.listIterator();
        while (it.hasNext()) {
            final LeaseRequest<T, C, E> request = it.next();
            final BasicFuture<E> future = request.getFuture();
            if (future.isCancelled()) {
                it.remove();
                continue;
            }
            final boolean completed = processPendingRequest(request);
            if (request.isDone() || completed) {
                it.remove();
            }
            if (request.isDone()) {
                this.completedRequests.add(request);
            }
        }
    }
}
```