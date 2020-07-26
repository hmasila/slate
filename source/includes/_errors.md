# Errors

The Auth Armor API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your Access token is not valid.
402 | Request Payment -- Request for payment.
404 | Not Found -- The specified auth profile does not exist.
405 | Method Not Allowed -- You tried to access an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The auth profile requested has been removed from our servers.
415 | Unsupported Media Type -- You requested a format that isn't json.
418 | I'm a teapot.
429 | Too Many Requests -- You're making too many requests!!! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
