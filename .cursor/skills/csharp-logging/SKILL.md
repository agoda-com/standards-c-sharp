---
name: csharp-logging
description: >-
  C# logging practices and log level standards. Covers log message formatting,
  log level selection (Info, Warn, Error, Fatal), when to log. Use when adding
  logging to C# code, choosing log levels, or reviewing logging practices.
---

# C# Logging Standards

## Log message guidelines

- Short but relevant messages
- Easily regexable — don't stuff in too many parameters
- Don't log if you know you'll never need it
- **Bad**: "hotel service got exception" (unhelpful — what exception?)
- **Good**: "HotelBuilderService: Hotel id 18921 got no facility data from API."

## Log levels

### Info

- Useful trace, debug, and information events
- Can reconstruct a customer's journey through the site
- Normally filtered out during investigations
- Includes 404s (expected noise)
- Example: "Calling FacebookAPI"

### Warn

- Something unexpected happened but we recovered
- Initial service timeouts (where retries happen automatically)
- Low business value feature couldn't display but page is still usable
- Not actionable by user
- Investigate if repeated
- Example: "Timed out retrieving recent searches on homepage. Not rendering section."

### Error

- Something bad happened, customer is failed
- Final timeout after all retries exhausted
- High business value feature couldn't display, page is mostly useless
- Actionable by user (e.g., manual retry / F5)
- All errors should be investigated promptly
- 5xx responses hitting the global exception handler
- Example: "Timed out 5/5 times from PAPI. Showing user 'Try Again' message."

### Fatal

- Catastrophic, likely losing significant money
- Stop everything and fix now — war room will be called
- Only pre-determined, known scenarios may be logged as fatal
- Examples:
  - "PAPI client reports all PAPI nodes are down. Dead now."
  - "Response from PayPal gateway: 'Invalid API key'. Cannot process transaction."
