- ## OracleDataSource#setConnectionCacheProperties_java_util_Properties_



- [ ] **MinLimit**
  Sets limit on Minimum number of connections. Defaults to 0.
- [ ] **MaxLimit**
  Sets limit on Maximum number of connections. No default.
- [ ] **InitialLimit**
  Sets limit on the number of initial connections populated, when the cache is first created. Defaults to 0.
- [ ] **MaxStatementsLimit**
  Sets the Maximum number of statements that each connection keeps open, for statement caching. Defaults to 0.
- [ ] **InactivityTimeout**
  Sets the Maximum time, in seconds, a connection in the cache can remain idle (that is not checked out of the cache). Defaults to 0.
- [ ] **TimeToLiveTimeout**
  Sets that maximum time, in seconds, a checked out connection can remain idle. Defaults to 0.
- [ ] **AbandonedConnectionTimeout**
  Sets the maximum time a checked out connection can remain unused (no SQL activity) before the connection is closed and returned to the cache. Defaults to 0.
- [ ] **ConnectionWaitTimeout**
  Specifies the time limit to wait, when a new connection request arrives, and there are no connections to check out. Defaults to 0.
- [ ] **PropertyCheckInterval**
  Sets the time interval for the cache daemon thread to enforce the time out limits. Defaults to 900 seconds (15 minutes).
- [ ] **ValidateConnection**
  Verifies each connection for validity, before a checked out connection is returned to the caller. Defaults to false.
- [ ] **ClosestConnectionMatch**
  Causes the connection cache to retrieve the connection with the closest approximation to the specified connection attributes. Defaults to false.
- [ ] **AttributeWeights**
  Sets the weight (integer) for each connection attribute. This is used when ClosestConnectionMatch is set to true, and enables retrieval of a connection with the highest combined weight of all its connection attributes. Defaults to a weight of 1.
- [ ] **LowerThresholdLimit**
  Sets the lower threshold limit on the cache. This is used for the releaseConnection() cache callback method is registered. Defaults to 20%.