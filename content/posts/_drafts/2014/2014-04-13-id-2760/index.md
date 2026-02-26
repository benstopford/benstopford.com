---
title: "TODO: Clock todo"
date: 2014-04-13
draft: true
slug: "id-2760"
---

package com.rbs.odc.core.dao.coherence.config.singleton;

import com.rbs.odc.core.dao.coherence.functions.trig.ReplicatedSchemeMapListener;

import com.rbs.odc.core.dao.coherence.util.MasterChecker;

import com.rbs.odc.core.event.listener.RetryConnectionEventsPublisher;

import com.rbs.odc.core.logging.Log;

import com.rbs.odc.core.logging.LogFactory;

import com.tangosol.io.pof.PofReader;

import com.tangosol.io.pof.PofWriter;

import com.tangosol.io.pof.PortableObject;

import com.tangosol.net.CacheFactory;

import com.tangosol.net.NamedCache;

import java.util.concurrent.Future;

import java.util.concurrent.ScheduledExecutorService;

import java.util.concurrent.ThreadFactory;

import java.util.concurrent.TimeUnit;

import java.util.concurrent.atomic.AtomicBoolean;

import java.util.concurrent.atomic.AtomicInteger;

import static java.lang.Integer.getInteger;

import static java.util.concurrent.Executors.newSingleThreadScheduledExecutor;

public class Clock implements PortableObject {

private static final Log LOG = LogFactory.getLog(Clock.class);

public static final String timeCacheName = "odc-cluster-time-cache";

public static final String later = "time-stamped-onto-all-odc-objects";

public static final String middle = "no-relevence-other-than ";

public static final String earlier = "time-you-read-with-for-repeatable-queries";     /// this should be earlier than current

public static final int tickTime = getInteger("odc.cluster.time.interval", 1000);

public static final int LAG\_BETWEEN\_EARLIER\_AND\_LATER\_TIME = 2 \* tickTime;

private MasterChecker masterChecker = null;

private final ScheduledExecutorService scheduler;

private Future futureTask;

private NamedCache timeCache = null;

private final AtomicBoolean running = new AtomicBoolean(true);

public Clock() {

this(new MasterChecker(), newSingleThreadScheduledExecutor(new ClockThreadFactory()), getCache(timeCacheName));

}

public Clock(MasterChecker masterChecker, ScheduledExecutorService scheduler, NamedCache clusterTimeCache) {

this.masterChecker = masterChecker;

this.scheduler = scheduler;

this.timeCache = clusterTimeCache;

}

public void tickTock() {

startListeners();

resetHands();

startTickScheduler();

}

private void resetHands() {

if (isMaster()) {

defaultTime(later);

defaultTime(middle);

defaultTime(earlier);

}

}

private void defaultTime(String type) {

if (timeCache.get(type) == null) {

timeCache.put(type, new ODCClusterTime());

LOG.info("Initialized %s time", type);

}

}

private void startTickScheduler() {

LOG.info("Starting cluster time scheduler with delay of %d milliseconds", tickTime);

futureTask = scheduler.scheduleAtFixedRate(new Runnable() {

public void run() {

if (isMaster() && running.get()) {

long laterTime = ((ODCClusterTime) timeCache.get(later)).getTime();

long middleTime = ((ODCClusterTime) timeCache.get(middle)).getTime();

long wallClockTime = timeCache.getCacheService().getCluster().getTimeMillis();

if (wallClockTime > laterTime) {

timeCache.put(later, time(wallClockTime));

timeCache.put(middle, time(laterTime));

timeCache.put(earlier, time(middleTime));

}

}

}

}, 0, tickTime, TimeUnit.MILLISECONDS);

}

protected void startListeners() {

if (isMaster()) {

ReplicatedSchemeMapListener.startReplicatedMapListeners();

}

}

private static ODCClusterTime time(long wallClockTime) {

return new ODCClusterTime(wallClockTime);

}

private static NamedCache getCache(String name) {

return CacheFactory.getCache(name);

}

protected void block() {

try {

if (futureTask != null) {

futureTask.get();

}

} catch (Exception e) {

LOG.error("Error on scheduled execution of ODCClusterTime command", e);

throw new RuntimeException(e);

} finally {

stop();

}

}

private boolean isMaster() {

return masterChecker.isMaster();

}

public void stop() {

RetryConnectionEventsPublisher.stop();

running.set(false);

if (!futureTask.isCancelled()) {

futureTask.cancel(false);

}

if (!scheduler.isShutdown()) {

scheduler.shutdown();

try {

if (!scheduler.awaitTermination(1, TimeUnit.SECONDS)) {

scheduler.shutdownNow();

}

} catch (InterruptedException ie) {

scheduler.shutdownNow();

Thread.currentThread().interrupt();

}

}

}

@Override

public void readExternal(PofReader arg0) {

//

}

@Override

public void writeExternal(PofWriter arg0) {

//

}

private static class ClockThreadFactory implements ThreadFactory {

private static final AtomicInteger count = new AtomicInteger(0);

@Override

public Thread newThread(Runnable runnable) {

return new Thread(runnable, "Clock-" + count.getAndIncrement());

}

}

}

package com.rbs.odc.core.dao.coherence.config.singleton;

import com.rbs.odc.core.dao.coherence.config.ClusterBasedClock;

import com.tangosol.net.NamedCache;

import org.joda.time.DateMidnight;

import java.util.Date;

import static com.rbs.odc.core.dao.coherence.config.singleton.Clock.LAG\_BETWEEN\_EARLIER\_AND\_LATER\_TIME;

import static com.rbs.odc.core.dao.coherence.config.singleton.Clock.earlier;

import static com.rbs.odc.core.dao.coherence.config.singleton.Clock.later;

import static com.rbs.odc.core.dao.coherence.config.singleton.Clock.timeCacheName;

import static com.tangosol.net.CacheFactory.getCache;

import static java.lang.String.format;

import static java.lang.Thread.sleep;

public class ClusterClock implements ClusterBasedClock {

public static final long maxTimeInLong = new DateMidnight(9999, 12, 31).getMillis();

private NamedCache timeCache;

private static final int MAX\_RETRY\_COUNT = 5;

public ClusterClock() {

this(null);  //lazy initialize the cache...

}

public ClusterClock(NamedCache timeCache) {

this.timeCache = timeCache;

}

@Override

public long getLaterTimeInLong() {

return getTime(later);

}

@Override

public Date getLaterTimeAsDate() {

return new Date(getLaterTimeInLong());

}

@Override

public Date getEarlierTimeAsDate() {

return new Date(getEarlierTimeInLong());

}

@Override

public long getEarlierTimeInLong() {

return getTime(earlier);

}

private long getTime(String timeKey) {

ODCClusterTime currentTime = (ODCClusterTime) getTimeCache().get(timeKey);

if (currentTime == null) {

sleepSilentlyForLagBetweenEarlierAndLaterClusterTime();

currentTime = (ODCClusterTime) getTimeCache().get(timeKey);

}

return currentTime == null ? -1 : currentTime.getTime();

}

@Override

public long getMaxTimeInLong() {

return maxTimeInLong;

}

@Override

public void waitForLagBetweenEarlierAndLaterClusterTime() {

ODCClusterTime earlierClusterTime = getClusterTime();

sleepSilentlyForLagBetweenEarlierAndLaterClusterTime();

ODCClusterTime currentClusterTime = getClusterTime();

int currentRetryCount = 0;

while(!ticksAreFarEnoughApart(earlierClusterTime, currentClusterTime) && currentRetryCount++ < MAX\_RETRY\_COUNT){

sleepSilentlyForLagBetweenEarlierAndLaterClusterTime();

currentClusterTime = getClusterTime();

}

if(!ticksAreFarEnoughApart(earlierClusterTime, currentClusterTime)){

throw new RuntimeException(format("Unable to move the ticks forwards after %d tries", MAX\_RETRY\_COUNT));

}

}

private boolean ticksAreFarEnoughApart(ODCClusterTime earlierClusterTime, ODCClusterTime currentClusterTime) {

return currentClusterTime.getTime() - earlierClusterTime.getTime() >= LAG\_BETWEEN\_EARLIER\_AND\_LATER\_TIME;

}

private ODCClusterTime getClusterTime() {

ODCClusterTime odcClusterTime = (ODCClusterTime) getTimeCache().get(earlier);

int i = 0;

while(odcClusterTime == null && i++ < MAX\_RETRY\_COUNT){

sleepSilentlyForLagBetweenEarlierAndLaterClusterTime();

}

if(odcClusterTime == null) {

throw new RuntimeException("Cluster time was null. This probably isn't good!");

}

return odcClusterTime;

}

private void sleepSilentlyForLagBetweenEarlierAndLaterClusterTime() {

try {

sleep(LAG\_BETWEEN\_EARLIER\_AND\_LATER\_TIME);

} catch (InterruptedException e) {

}

}

private NamedCache getTimeCache() {

if (timeCache == null) {

timeCache = getCache(timeCacheName);

}

return timeCache;

}

}

package unit.com.rbs.odc.core.dao.coherence.config.time;

import com.rbs.odc.core.dao.coherence.config.singleton.Clock;

import com.rbs.odc.core.dao.coherence.config.singleton.ODCClusterTime;

import com.rbs.odc.core.dao.coherence.util.MasterChecker;

import com.tangosol.net.NamedCache;

import org.testng.annotations.BeforeMethod;

import org.testng.annotations.Test;

import util.NamedCacheStub;

import java.util.concurrent.ScheduledExecutorService;

import static com.rbs.odc.core.dao.coherence.config.singleton.Clock.\*;

import static org.hamcrest.MatcherAssert.assertThat;

import static org.hamcrest.Matchers.is;

import static org.hamcrest.Matchers.nullValue;

import static org.mockito.Mockito.mock;

import static org.mockito.Mockito.when;

public class ClockTest {

private NonReplicatingODCClusterTimeCommand clock;

private NamedCacheStub timeCache;

private MasterChecker masterChecker;

@BeforeMethod

public void beforeMethod() {

timeCache = NamedCacheStub.createInstance();

masterChecker = mock(MasterChecker.class);

when(masterChecker.isMaster()).thenReturn(true);

clock = new NonReplicatingODCClusterTimeCommand(masterChecker, new RunItNowScheduler(), timeCache);

}

@Test

public void readTimeShouldBeTwoTicksBehindWriteTime() throws Exception {        //Given

timeCache.put(later, time(5));  //write

timeCache.put(middle, time(4));

timeCache.put(earlier, time(3)); //read

timeCache.setCoherenceTime(6); //wall clock in future

//When

clock.tickTock();

//Then

assertThat(getFromCache(later), is(time(6)));

assertThat(getFromCache(earlier), is(time(4)));

}

@Test

public void shouldNotUpdateClusterTimeIfTimeHasNotMoved() throws Exception {

//Given

timeCache.put(later, time(5));

timeCache.put(middle, time(4));

timeCache.put(earlier, time(3));

timeCache.setCoherenceTime(5);// wall clock time is the same as later time

//When

clock.tickTock();

//Then - nothing should have changed

assertThat(getFromCache(later), is(time(5)));

assertThat(getFromCache(earlier), is(time(3)));

}

@Test

public void shouldNotUpdateClusterTimeIfTimeIsInThePast() throws Exception {

//Given

timeCache.put(later, time(5));

timeCache.put(middle, time(4));

timeCache.put(earlier, time(3));

timeCache.setCoherenceTime(4);// wall clock time in the past

//When

clock.tickTock();

//Then - nothing should have changed

assertThat(getFromCache(later), is(time(5)));

assertThat(getFromCache(middle), is(time(4)));

assertThat(getFromCache(earlier), is(time(3)));

}

private ODCClusterTime getFromCache(String type) {

return ((ODCClusterTime) timeCache.get(type));

}

@Test

public void shouldNotUpdateClusterTimeWhenRunningOnDR() throws Exception {

//Given

when(masterChecker.isMaster()).thenReturn(false);

//When

clock.tickTock();

//Then nothing happens

assertThat(getFromCache(later), is(nullValue()));

assertThat(getFromCache(earlier), is(nullValue()));

}

private static ODCClusterTime time(long time) {

return new ODCClusterTime(time);

}

static class NonReplicatingODCClusterTimeCommand extends Clock {

public NonReplicatingODCClusterTimeCommand(MasterChecker masterChecker, ScheduledExecutorService scheduler, NamedCache clusterTimeCache) {

super(masterChecker, scheduler, clusterTimeCache);

}

protected void startListeners() {

//Don't want to call these from unit tests

}

}

}

package functional.com.rbs.odc.core.dao.coherence.config;

import com.rbs.odc.core.dao.coherence.config.singleton.Clock;

import com.rbs.odc.core.dao.coherence.config.singleton.ODCClusterTime;

import com.tangosol.net.NamedCache;

import functional.com.rbs.odc.core.ODCFunctionalTest;

import org.testng.annotations.BeforeClass;

import org.testng.annotations.Test;

import static com.rbs.odc.core.dao.coherence.config.singleton.Clock.\*;

import static org.hamcrest.MatcherAssert.assertThat;

import static org.hamcrest.Matchers.greaterThan;

import static org.hamcrest.Matchers.greaterThanOrEqualTo;

public class ClockFunctionalTest extends ODCFunctionalTest {

@Test

public void shouldEnsureTheCurrentODCClusterTimeIsGreaterThanTheConfirmedODCClusterTime() throws Exception {

NamedCache odcClusterTimeCache = getCacheFactory().ensureCache(timeCacheName, getClass().getClassLoader());

int times = 10;

for (int i = 0; i < times; i++) {

ODCClusterTime readTime = (ODCClusterTime) odcClusterTimeCache.get(Clock.earlier);

ODCClusterTime writeTime = (ODCClusterTime) odcClusterTimeCache.get(later);

assertThat(writeTime.getTime(), greaterThan(readTime.getTime()));

Thread.sleep(tickTime \* 2);

}

}

@Test

public void shouldCheckTheODCClusterTimeNeverGoesBackward() throws Exception {

System.out.println("Foobarfoo");

NamedCache cache = getCacheFactory().ensureCache(timeCacheName, getClass().getClassLoader());

ODCClusterTime writeTime = (ODCClusterTime) cache.get(later);

int times = 2;

long timeInFuture = writeTime.getTime() + (tickTime \* times);

// Ensure that future time is written in cluster

while (((ODCClusterTime) cache.get(later)).getTime() < timeInFuture) {

Thread.sleep(tickTime / 5);

}

// Cluster time moving forward

long actualTime = ((ODCClusterTime) cache.get(later)).getTime();

assertThat(actualTime, greaterThanOrEqualTo(timeInFuture));

}

@BeforeClass

public void beforeClass(){

}

}

**Benjamin Stopford** ODC Architect Markets & International Banking RBS Bankside 3, 90-100 Southwark Street, London SE1 0SW, UK Office: +44 20 3361 2344  |  Mobile: +44 7968 702589
