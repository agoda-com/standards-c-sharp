## Logging

- Make messages short but relevant.
- Make them easily regexable to allow similar messages to be found, eg. refrain from stuffing in too many parameters.
- Do not log if you know you will never need it. 
- Bad: "hotel service got exception" is not helpful when trying to debug. What was the exception?
- Good: "HotelBuilderService: Hotel id 18921 got no facility data from API."