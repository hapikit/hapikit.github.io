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

## ResponseHandler

## HttpResponseMachine

## HTTP Cache

## Credential Service

## Redirect Handler

## Retry Handler

## Link Hints

## Link Factory

## Link Headers
