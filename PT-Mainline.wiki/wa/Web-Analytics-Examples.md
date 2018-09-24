# Writing a Statistics Session

## Create a Statistics Session

~~~
Collection<String> features = Arrays.asList("com.ibm.rational.test.lt.feature.http", "com.ibm.rational.test.lt.feature.lt");
StatsSessionMetadataBuilder builder = new StatsSessionMetadataBuilder();
builder.setStartTimestamp(System.currentTimeMillis());
builder.setTestPath("/Project/test.testsuite");
builder.setFeaturesWithLatestVersion(features, ExecutionStatsCore.INSTANCE.getCounterDescriptorRegistry());
ILiveStatsSession session = ExecutionStatsCore.INSTANCE.getSessionManager().createLiveStatsSession(file, builder.build(), IStatsSessionStorageStrategy.PER_AGENT);
// Add stores and populate data
session.close();
~~~

## Create an agent and a raw store

~~~
IWritableRawStatsStore wstore = session.createWriter(hostName, /*hostTags*/null, agentType, 0L, true);
try {
    // Write data
} finally {
    wstore.close();
}
~~~

## Write observations to a store

### Simple counter
This example adds a counter with path `/Run/Active Users`, of type `INCREMENT_EXTENT`.
The value type for this counter type is `LongValue`.
~~~
ICounterFolderHandle run = store.addCounterFolder("Run", /*parent*/null/*means root*/);
ICounterHandle activeUsers = store.addCounter("Active Users", AggregationType.INCREMENT_EXTENT, /*parent*/run);
store.addObservation(System.currentTimeMillis(), new LongValue(12), activeUsers);
~~~

### Instance counters
This example creates a dictionary named `PAGE`, and adds a term `Page1` to it.  
It creates a folder with path `/Pages/Page1`, where `Page1` is the term previously created.  
It creates two counters under this folder named `Response Time` and `Hits`, of respective 
types `VALUE_RANGE` and `COUNT_BASIC`  .
The value type for both theses counter type are `PositiveLongValue`.

~~~
IDictionaryHandle pageDictionary = store.addDictionary("PAGE", /*parent*/null/*means root*/);
ICounterFolderHandle pages = store.addCounterFolder("Pages", /*parent*/null/*means root*/);
ITermHandle page1Term = store.addTerm("MyPage1", pageDictionary, /*parent*/null/*means root*/);
ICounterFolderHandle page1Folder = store.addCounterFolder(page1Term, pages);
ICounterHandle page1ResponseTime = store.addCounter("Response Time", AggregationType.VALUE_RANGE, /*parent*/page1Folder);
ICounterHandle page1Hits = store.addCounter("Hits", AggregationType.COUNT_BASIC, /*parent*/page1Folder);
store.addObservation(System.currentTimeMillis(), new PositiveLongValue(2435), page1ResponseTime);
store.addObservation(System.currentTimeMillis(), new PositiveLongValue(12), page1Hits);
~~~
