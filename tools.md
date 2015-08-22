---
layout: default
permalink: /tools/
---

#Tools

## URI Template
A common activity in clients is the process of creating URLs to be used in HTTP requests.  In order to enable libraries to be built to make this process more standardized, [RFC 6570](http://tools.ietf.org/html/rfc6570) defined a template syntax for use in URL construction.  

HapiKit contains a class called [UriTemplate](https://github.com/hapikit/hapikit.net/blob/master/src/Hapikit.net/Templates/UriTemplate.cs) which is based on the class in [Tavis.UriTemplates](https://github.com/tavis-software/Tavis.UriTemplates).

More details on how this class can be used are shown in the blog post [Constructing URLs the easy way](http://www.bizcoder.com/constructing-urls-the-easy-way).

{% highlight csharp %}

var url = new UriTemplate("http://example.org/{tenant}/customers")
       .AddParameter("tenant", "acmé")
       .Resolve();

{% endhighlight %}

## RequestFactory

The most fundamentally different approach that Hapikit SDKs take is that they explicitly expose the ability to create HTTP request messages and process HTTP response messages in the native types of the platform being supported.  Most API SDKs hide these steps behind a procedure call and due to this are forced to make many decisions on behalf of the client application.  This severely limits the re-usability of the SDK.

The IRequestFactory interface is the C# implementation of the abstraction used to expose the ability to create a HTTP request.  In C# the native type is a HttpRequestMessage class.

{% highlight csharp %}

    public interface IRequestFactory
    {
      string LinkRelation { get; }
      HttpRequestMessage CreateRequest();
    }

{% endhighlight %}

The LinkRelation property is a type identifier that is used to distinguish one HttpRequestMessage from another.  The identifiers are either standardized or specific to the API being consumed.

The IRequestFactory interface is implemented by the Link class.

## Link
In order to encapsulate the API semantics required to build HTTP requests, HapiKit SDKs create specialized link classes as factories for HTTP requests.  A class named [Link](https://github.com/hapikit/hapikit.net/blob/master/src/Hapikit.net/Links/Link.cs) is used a base for the specialized Links.

{% highlight csharp %}

namespace haveibeenpwnd.net.hapisdk
{
    public class BreachedAccountLink : Link
    {
        public string Account { get; set; }
        public string Domain { get; set; }

        [LinkParameter("truncateResponse",Default=false)]
        public bool TruncateResponse { get; set; }

        public BreachedAccountLink()
        {
            Template = new UriTemplate("https://haveibeenpwned.com/api/v2/breachedaccount/{account}{?truncateResponse,domain}");
        }
    }
}

{% endhighlight %}



## RequestBuilder
RequestBuilder is an implementation of the [chain of responsibility pattern](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern) for allowing link classes to compose the construction behaviour of request messages.  Usually the construction of HTTP header values are independent from each other and frequently applicable to multiple link types.  Encapsulating the behaviour in chainable RequestBuilder classes allows the code to be isolated and reused.

DefaultRequestBuilder is the default implementation of the factory class used by Link classes to create request messages.  The DefaultRequestBuilder performs the following functions:

* Update the request message with the HTTP body assigned to the link, if there is one
* Update the request message with the HTTP method assigned to the link
* Reflect over the link class looking for LinkParameter attributes and attempt to resolve the UriTemplate in the Link class using those parameters.

The default requestbuilder can be replaced completely or new requestbuilder classes can be chained to together to compose the construction mechanism.

RequestBuilders can be used to build Authorization headers when authorization scheme parameters include signatures of the request body.  They can be used to decide on whether a body should be chunked or compressed based on the size of the request body.  They can add custom headers that are specific to a particular application or API.

Currently, the Link class only allows someone who is using a Link to add an additional RequestBuilder.  There is no way to remove RequestBuilders that have been defined in the derived Link class.  Having said that, there is nothing stopping an added RequestBuilder from undoing changes made by built in Requestbuilders.

There are three levels at which RequestBuilder behaviour can be added to Link classes:

* In the constructor of the derived link class
* RequestBuilders can be registered in the LinkFactory
* Client code can add RequestBuilders to the chain

The first option is used when the definition of the Link Relation Type defines a particular request behaviour. The second option is used when a particular client application always wants a particular behaviour whenever a the link type is discovered in a hypermedia document.  The third option is when a particular instance of following a link needs to add a behaviour due to the current client state.  

## ResponseHandlers
Response handlers are based around the standard interface IResponseHandler

{% highlight csharp %}

   public interface IResponseHandler
   {
       Task<HttpResponseMessage> HandleResponseAsync(string linkRelation, HttpResponseMessage responseMessage);
   }

{% endhighlight %}

The intent of this interface is to decouple the code that makes the HTTP request from the code that handles the response. The only context that can be used to process the response is type of link that was followed, the response message and the current state of the response handler. This de-coupling encourages clients to be more capable of dealing with changes that occur on the server as fewer assumptions are made about the response to a request.

There are several extension methods that enable consise use of a ResponseHandler to process the response to a request. For example, the following code uses the IRequestFactory interface on the _link_ object to create a _HttpRequestMessage_ and then the IResponseHandler interface on the _clientState_ object to handle the HttpResponseMessage.

{% highlight csharp %}

     await client.FollowLinkAsync(link)
              .ApplyRepresentationToAsync(clientState);

{% endhighlight %}

In this case we are still selecting a specific response handler to deal with the response from a specific request.

## HttpResponseMachine
To further de-couple the processing of responses to allow plugging in standard behaviours for many standard responses we can turn to the HttpResponseMachine.


## HTTP Cache

## Credential Service

## Redirect Handler

## Retry Handler

## Link Hints

## Link Factory

## Link Headers
