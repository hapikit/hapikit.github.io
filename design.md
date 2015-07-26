---
layout: default
permalink: /design/
---

#Design Principles

## Don't Lock Away the HTTP
Probably the single most important characteristic of SDKs based on HapiKit is that it should always be possible for the consumer of the API to be able to access and manipulate the HTTP requests and responses directly.  The tooling should make it easier to create requests, send requests and handle responses, but never at the expense of control.

## Minimize the Footprint of the Application Semantics
On the other hand, the library can encapsulate the APIs semantics so that a consuming application does not need to be concerned with the details of how to formulate specific HTTP requests to an API. The API specific concerns should be contained as much as possible and should not affect standard HTTP uniform interface interactions where possible.  

## Simplify Request and Response Coupling
Using state that is in scope, when making a request, whilst handling the response is a quick way to create out-of-band coupling between the client and server that is highly susceptible to breaking when servers make changes.  
Automatic dispatching of responses based on status code, and optionally media type and/or the link relation type that was followed, can remove a significant amount of boilerplate and error prone response handling code.

## Centralized Response Handling
Where possible, response handling code should be as DRY as practical.  If multiple links behave the same way in response to a certain status code then they should be able to reuse the same code.  
By taking this approach we are able to easily ensure that all status codes are handled by all requests, which helps make the client library more future-proof.
