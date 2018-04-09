## Rethrow exceptions when you cannot gracefully recover

- It's OK to be unable to gracefully recover from an exception. Exceptions should _exceptional_, so as programmers we can't be expected to handle every potential edge case.
  - Someone forgot to deploy the config file?
  - The network went down?
  - The datacenter was destroyed by a hurricane?
- We can consider these _not our problem_ as there is nothing we can do directly do about them right there in the code. 
  - Either: don't catch the exception in the first place.
  - Else: catch, handle (log?) and _rethrow_. Give someone further up the callstack the chance to handle it more appropriately.