## Logging

- Make messages short but relevant.
- Make them easily regexable to allow similar messages to be found, eg. refrain from stuffing in too many parameters.
- Do not log if you know you will never need it. 
- Bad: "hotel service got exception" is not helpful when trying to debug. What was the exception?
- Good: "HotelBuilderService: Hotel id 18921 got no facility data from API."

### Log levels

 
We use 4 log levels at Agoda.

#### Info
- All useful trace, debug and information events. Can be used to reconstruct a customer's journey through the site.
- Normally would be filtered out during an investigation.  
- 404s (people around the world are mashing their URL bars at us; we don't need to be warned of this)
- eg: "Calling FacebookAPI" 

#### Warn
- Something unexpected happened, but we managed to muddle through.
- Initial timeouts from services (where retries will happen automatically).
- A low business value feature could not be displayed, but the page was still usable for its primary purpose.
- The condition is not actionable by the user, ie. they cannot and do not need to do anything to recover.
- If it happens repeatedly then should be investigated.
- eg: "Timed out when retrieving recent customer searches on homepage. Not rendering section." 

#### Error
- Something quite bad happened, and we have failed the customer.
- The final (or only) timeout from a service after a series of retries.
- A high business value feature could not be displayed, making the page pretty much useless (eg. could not load property data on property page).
- The conditional is actionable by the user, in that they might be able to manually retry (even if it's just hitting F5)
- All errors should be investigated, sooner rather than later.
- If persistent, a war room may be called.
- Anything hitting the global exception handler in the 5xx response code range should be an error.
- eg: "Timed out 5/5 times from PAPI. Showing user 'Try Again' message."

#### Fatal
- Something catastrophic happened that is likely losing the company significant amounts of money.
- Stop everything and fix now. A war room will be called.
- Do not create fatal errors arbitrarily. Only pre-determined, known scenarios may be logged as fatal. We should analyze the various conditions that can constitute a fatal error. 
- eg:
    - "PAPI client reports all PAPI nodes are down. Dead now."
    - "Response from PayPal gateway: 'Invalid API key'. Cannot process transaction."

### Seq

Consider logging to Seq instead of Kibana for new projects. Advantages are:

- SQL-like query language (Seq) is more familiar to most people than Elastic Search based language (Kibana)
- more...?