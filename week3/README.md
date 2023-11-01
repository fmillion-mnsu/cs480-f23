# Week 3: APIs, Compatibility and Versioning

## Presentations:

### Tuesday:

* [A Brief Review of APIs](#discussion-a-brief-review-of-api)
* [Semantic Versioning](#discussion-semantic-versioning)

### Thursday:

* [REST APIs](#discussion-web-apis-rest)
* [API Security](#discussion-web-api-security)

## Discussion: A Brief Review of API

An Application Programming Interface (API) is a specification for how one program can interact with another program. *That's it.* There are tons of ways this can be actually implemented, but the goal is always the same - to enable software to communicate with other software.

APIs typically define a set of interaction endpoints, which may be methods, Web URLs, or protocols. (See the later discussion on the connection between APIs and protocols.) The API will specify the expected interaction method - for functions or methods it will simply be the required parameters and the return type, whereas for something like a Web API it will consist of the required and optional parameters, their meaning, and the meaning of return value(s). 

### The simplest APIs

At the very simplest level, you're defining an API every time you define a method or function. Consider this:

    def printIndented(string, indent=4):
        print ((" " * indent) + string)
    
This code defines a simple Python function requiring two parameters. Now, if you want to use this function somewhere else, you call the method:

    printIndented("Hello World", 4)

You just made use of your own tiny little API! You defined a function and specified the expected parameters to that function, and you interacted with that function following the expectations of that function's input. (This function technically returns `None`, and recall that in Python we can ignore that if we don't care about the return of a function.)

Moving a step up from this, recall from your programming courses the concept of *interfaces*. An interface, as defined in Java or C#, is a kind of API. Let's take a very simple example in C#:

    public interface ICanRun 
    {
        public void Run();
    }

This interface defines a single method - `Run`. Any program that wants to implement this interface must implement the `Run` method. However, recall that a key point of interfaces (and subclassing as well) is that the *implementation* is up to the programmer - all that an interface does is specify *what* the class must do, not *how* it does it. 

### Where are APIs found?

Software libraries - including the standard library for your programming language - are also examples of APIs. The language's library provides you with a wide set of functions, classes and such. This API allows your code to interact with the library's code. The same applies if you import or use an external library - you're following the API for that library any time you use it.

Operating systems also typically provide a significant collection of APIs for programmers. These APIs range from low-level operations such as handling files and network connections to advanced user-interface APIs for providing standardized user interaction, accessibility, and graphics acceleration support for games and professional applications.

APIs are also used to interact with precompiled software libraries, compiled applications, remote servers, and even hardware devices. However, APIs are distinct from user interfaces, because user interfaces are designed to interact with the *user*, while APIs are designed to interact with *other software*. Of course you "use" an API when you're a developer, but a non-developer will almost never touch an API directly.

> **API vs. Protocol**
>
> There's a bit of a blurry line between an API and a protocol. An API can be viewed more as "what *can* you do" and "how do you need to do it and what should you expect", where a protocol is "what *should* you do".
>
> Even this doesn't un-blur that line, and by some pedantic definitions a protocol *is* an API, and an API *is* a protocol. We'll refer to things as APIs in this discussion, but protocols may come up in related contexts. The typical delineation is that the term *protocol* is usually used in terms of network infrastructure, and *API* typically refers to software running atop that infrastructure.
>
> For example, HTTP is almost always defined as a protocol, because it is a specification indicating how a web browser should communicate with a web server. These programs are considered to be part of the network infrastructure, whereas the *application* running within your browser (such as Gmail or Facebook) is viewed as the application which might offer APIs. However, HTTP still fits the definition of an API - it's a specification for how one program should interact with another. HTTP also offers multiple operations and provides multiple types of return data. However, if you see the term "HTTP API", it's nearly always referring to an API that runs *atop* the HTTP *protocol* such as REST or SOAP. 
>
> For our discussion, we'll focus on the *application* part of API. We won't refer to HTTP, FTP, Ethernet, etc. as *APIs* since those "protocols" are focused on the underlying networking infrastructure and not the applications that run on it. But be aware that there is no clear delineation between API and protocol, and their definitions will often appear very similar.

Another common type of API is the web API. Web APIs define a strategy for interacting with an application running on a Web server. Common Web APIs are REST, SOAP and XMLRPC. REST is one of the easier ones to understand, and we'll be covering that in another presentation.

### Versioning

Now let's talk versioning. Recall back to your programming courses and consider the concept of *overloading*. Recall that overloading lets you call the same method with different sets of parameters (i.e. the signature of the method) and the method that matches the signature of your parameters is the one that will be called. Keep overloading in mind while you consider this question:

> How can I add another distinct method that has the same name as an existing method, without affecting code that uses the existing method?

The solution, of course, is to use overriding. This allows you to provide extra method parameter(s) as needed, *without upsetting any existing code depending on the method's existing signature.*

This procedure is a primitive form of *versioning*. API versioning refers to methods that we can use to ensure that, as we update an API, code that already depends on the existing API is not affected and does not need to be changed. Imagine how hard it would be if, every time you needed to add a new method to your API, *every* program that uses it needed to be rebuilt against that new version? (This is a viable option for very small operations or personal projects, but it gets very quickly impossible on large, widely-distributed projects!)

> **Case Study: Microsoft Windows**
>
> During the last 28 years, there have been 12 major releases of the Windows operating system. Despite 30 years of advancement, a copy of Windows 11 is able to run a program written for Windows 95 - an OS that was released 26 years prior to Windows 11's release!
>
> (Naturally there are some issues with this - complex programs may not work at all or may have a lot of errors - but simpler programs, like Solitaire, will actually run just fine.)
>
> How is this possible? Hasn't Windows changed dramatically since *the year 1995*? Of course it has. But one thing Microsoft has taken very seriously throughout Windows' development is *API versioning*. Each time Microsoft adds a new user interface functionality or even a complete UI redesign, they still ensure that the APIs needed by older Windows applications are still available. 
>
> Often the *implementation* of those APIs will change dramatically. Sometimes major changes are needed for security reasons. Other times new abilities are added to the user interface that older software has no idea even exist. But nonetheless, this older software is able to run on modern Windows, because the APIs are still available!

There's a few different approaches for API versioning. One approach is overloading as we just discussed. This works well for many programming projects and can be essential for software libraries. However, it does present some challenges and limitations:

* You can't re-define the expected behavior of a method or function. You can define the behavior of the *new, overriden* function, but you can only override a function by changing the method signature. What if you need to change the behavior but you don't need to change the method signature?
* A program might have a dependency on a specific version or implementation of your function. There's no way for a program to request or require a specific implementation. 
* The *dependency graph* can get very ugly very quickly. (This does occur in practice - if you've worked on a NodeJS based project, you'll often see tons of dependency problems, outdated packages and other API-related issues. Sometimes it's a surprise Web development even works!)

Another approach to API versioning is to introduce version numbers into each API call. For Web APIs, it may be something like prefixing each endpoint with a `v1/`, `v2/` and so on. For software libraries, you could append `v1` to the end of each method name - for example `MyMethod_v1()`. This approach is somewhat common on Web APIs but is not seen quite as frequently on compiled software, but it is done there too as well. You could also achieve a similar effect in compiled software if your language uses namespaces - for example, you could write your updated API in the namespace `MyCode.MyLibrary.V2`; anyone who wants to use the new version simply needs to change their `using`/`import`/etc. statements to point to the new version. This also has some drawbacks with respect to maintainability however.

### Summary

The key points to take from this discussion are as follows:

* An API is a specification for how software can interact with other software. It's distinct from user interfaces, which is how users interact with software.
* APIs can be as simple as your method signatures in some code or as complex as the entire standard library of a programming language or a vast collection of APIs provided by an operating system.
* APIs define the required input, expected output and expected behavior. They do not specify *how* the expected behavior is accomplished.
  * An API might say "Providing two values to this method will return the result of multiplying the two values." It does not specify *how* the multiplication is done.
* *Versioning* refers to strategies that allow APIs to continue to be compatible through upgrades, additions and changes. 
  * It can be done in multiple ways. Simple methods include naming methods or endpoints with a version number, and using method overloading. These methods have some tradeoffs.

## Discussion: Semantic Versioning

So, you've updated your API to a new version. (Or your application, or your mobile game, or your novel. Yes, some people do version numbers on their books.) How do you decide the next version?

We've all seen version numbers. 1.0 typically denotes "the first finished version". We tend to naturally know that 2.0 means "a major update, likely with new features", 0.5 means "a pre-release/unfinished version", and 1.1 means "some minor bug fix or update".

But this has not always been consistent. For a long time (and still to this day in a lot of cases), a marketing and branding department decides on version numbers, not the development team. There are multiple examples of software programs that skipped over many version numbers because a competitor program had advanced to a "significant" number. If your competitor just put out Version 6.0 and is parading around its new features, how are you (and your potential customers) going to feel about you releasing Version 3.0, even with more features?

> **A prominent example and a fun story**
>
> What happened to Windows 9?
>
> And why didn't we ever see Windows 4, Windows 5 or Windows 6 as the official public names of Windows products?
>
> Back in the 90s, the last major version of Windows that consumers identified by its version number was Windows 3.11. (There was also Windows NT 4.0, but that was typically only seen in commercial environments.)
>
> It was around that time that Microsoft started naming Windows versions after the year they were released (or close to it). Windows 95, Windows 98 and Windows 2000 all shared this naming scheme. But did you know that internally, Windows 95 was actually Version 4.0, Windows 98 was Version 4.2, and Windows 2000 was Version 5.0? 
>
> Windows XP was version 5.1, and Windows Vista was version 6.0. And, just to make things more fun, Windows 7 was actually Version 6.1. Oh, and Windows 8 was actually Version 6.2, and Windows 8.1 was Version 6.3. There's a reason for that though...
>
> There's a common theory (unproven) that Microsoft decided to move to the name "Windows 10" because Apple had long since named its operating system "Mac OS X", pronounced Mac OS "Ten". This brought Windows in line with Apple's "version numbering" (although Apple had stuck with 10 as the major version for over 16 years.)
>
> Now, Windows has moved to Windows 11, and Mac has moved up to macOS 13. For these operating systems, the version numbers have little relevance anymore outside of marketing. Windows doesn't even have an official traditional version number anymore - now the "official" version number is an identifer such as "23H2".

Why did Windows 7 keep version 6 as its major version number? And why did Windows XP keep version 5 as its major number? The reason is because version numbers usually mean something different to software developers. They're not just a marketing term or a branding tool, they actually tell us something about the system. It was actually quite common for software written during the Windows 2000/XP era to look for a major Windows version number other than 5 and cause an error if it was found, refusing to run and assuming the operating system would not be compatible. Since Windows 2000 and XP were actually very compatible, Microsoft's developers decided to keep the major version as 5, so that software that used Windows' version number this way wouldn't simply refuse to run under Windows XP. The same thing occurred when Windows 7 replaced Windows Vista - the major version was retained at 6 so that software wouldn't choke on 7. This carried on through Windows 8.1, with the internal Windows version number maintaining the major version of 6. Windows 10 was when Microsoft finally decided to switch the major version number to match the name of the OS.

Marketing-influenced versioning sounds cool, but it's far from practical for software developers. But there is also plenty of disagreement as to *what* constitutes a version change. There are also version numbers that go beyond two digits - something like 5.0.100.55049 is the kind of version number you might come across these days. 

**Semantic versioning** is a specification that provides us with a consistent, reliable standard for when and how version numbers should be changed. Branding departments might not like it, but as developers, it's a great idea to use it, especially if you are developing software that provides APIs for other software!

The core tenets of semantic versioning are as follows:

* Version numbers are three parts - major, minor, and patch. 1.0.0 is a valid version number under this scheme.
  * Extra pieces can be added to the end for various reasons - for example, build number or time, or specifiers such as "beta", "RC", and so on. However, the three numbers at the start of the version must follow the *semver* standard.
* Semantic versioning is specifically intended to be used with software that exposes an API. The reasons for changing version numbers are directly connected with the fact that the software provides an API for use by outside applications.
* A specific version number must identify a specific release of the program, library or system. In other words, once a version is released, that's it. Code freeze. Immutable. Don't change it. 
  * If there's a serious security flaw, you still don't just replace Version 1.0.0. You release Version 1.0.1 and you deprecate or remove Version 1.0.0.
  * In practice this may not always make sense for a single but extremely critical bug, because it's common for programs to permanently bond to the version installed on the developer's machine. We'll talk more about this later, but this is one case where slightly violating the standard may make sense.
* The major version 0 is used for prototyping, initial development and testing. Major version 0 says to the world "this isn't done yet, but I'm releasing it because it does something useful that you might benefit from even in its unfinished condition." Major version 0 should try to follow the same rules as later versions, but it's also more accepted to break stuff within major version 0 releases. Users should know that using an API with a major version of 0 is a hit-or-miss venture when it comes to version upgrades.
* The major version (starting from 1) defines the public API. All versions that use the same major version must expose a compatible public API. A program that interacts with the API on version 1.0.0 must work flawlessly and with the same results on version 1.6.83 or on version 1.55.4923.
* Changing the major version number indicates that some significant change has been made to the API that would break existing users of the API. So a program that works with Version 1.5.0 is not expected to work with Version 2.0.0 without some intervention.
* The minor version number is incremented if changes to the API are made which are **backward-compatible**. This might include things such as adding a new API endpoint or method that is optional. It might also involve using techniques such as method overloading to provide alternative implementations to an existing API that programs unaware of those new implementations are not affected by.
  * It is expected that software requiring version 1.1.0 of an API may not work with version 1.0.0. However, software that works with API version 1.0.0 must work fully with API version 1.1.0.
* The patch number is incremented for bugfixes, security patches and other changes that do not impact the expected behavior of any API method. A program that works with API version 1.1.0 must work with *any* API with major version 1 and minor version 1 regardless of patch version.

For the definitive source of information on semantic version, see the [semantic versioning website](https://semver.org/)! 

## Discussion: Web APIs: REST

Now that we've established APIs and how to version them, let's actually look at a real-life API standard: REST.

REST is an acronym that stands for **Re**presentational **S**tate **T**transfer. In short, REST uses the HTTP protocol's characteristics to specify what you want to do and what you want to do it with. 

The "state transfer" in REST refers to the fact that a REST API is designed to be *stateless*. This means that the API itself does not need to track user sessions. Each request to a REST API includes all of the information that the server needs to fulfill the request. If any state is required, the state is maintained by the client of the API and is sent along with each request; if the server needs the client to store some state data, it's returned as part of the response. This means that each and every request on the server side is independent of any other request, and each request is atomic - it either finishes or it doesn't. (We'll see an example of why this is important in the next discussion.)

### A little HTTP background

HTTP is the protocol that drives the World Wide Web. Every time you access a web site using your web browser, you're using HTTP. (HTTPS is just HTTP with encryption. The underlying protocol is still HTTP.) HTTP is a very old protocol that has gone through a couple of revisions, but its core functionality is the same as it's been for years.

An HTTP transaction begins with a **request** - your web browser sends a command to the server that asks for something. The request consists of up to three parts, two of which are required: the **verb**, the **URI** and the **headers**. 

The **verb** is typically represented in all-caps (but it's not strictly required, and most servers will work just fine if you give the verb in lowercase). The most common verbs - which directly relate to operations in REST - are `GET`, `PUT`, `POST`, and `DELETE`. There's a few other ones that have special use cases, such as `OPTIONS`, but REST APIs can be implemented with just those four verbs.

The **URI** is the path to a resource. You see the URI as the part of the Web URL that follows the domain name. For example, the URI for `https://github.com/fmillion-mnsu` is `/fmillion-mnsu`.

The **headers** are information provided along with the request by your browser. Headers are a simple set of key-value pairs. The browser will always add some default headers, such as a `User-Agent` header which gives information about your browser's version and capabilities, and `Referrer` which tells the browser which page you were on that directed you to access this URL, either by clicking on it or by it being referred to on the page in some fashion. 

A request can also optionally contain some arbitrary data. This can technically be done for any verb, but is most commonly seen with `PUT` and `POST`.

The response from a Web server consists of a **response code**, and optional response message, the **response headers**, and the **body**. There are several response codes, but the most common ones that you have likely seen at some point are:

* `200`: OK. The request succeeded, and the content of the request follows.
* `206`: Partial Content. The request succeeded and a portion of the response follows. This typically happens if your request included a `Range` header which indicated you only wanted a portion of the content, such as a part of a file.
* `400`: Bad request. This is defined as the server telling you something was wrong with your request. Maybe the headers included some invalid data, or the URL included an invalid parameter. 
* `401`: Unauthorized. This occurs if you are not permitted to access the resource, but you may be able to do so if you provide some type of authentication. Convention says that `401` is usually returned for unauthenticated requests, where...
* `403`: Forbidden ...is returned when you provide authentication but you are not permitted to access the resource with the authentication you provided.
* `404`: The famous Not Found error. 
* `500`: Server error. Something went wrong on the server's end - nothing was necessarily wrong with your request, but the server couldn't do anything with it. In other words, it's not your fault. 

And we musn't forget:

* `418`: I'm a teapot, and I can't brew coffee. [Read about error code 418](https://datatracker.ietf.org/doc/html/rfc2324) or [see a page that returns it](https://www.google.com/teapot). :)

After the headers, a blank line is sent, and then the actual response body is sent as-is. 

> **A sample HTTP transaction**
>
> The following is a simple HTTP transaction. The data in question is exactly what is sent between the client (your browser) and the web server.
>
> To the server:
>
>     GET /ip HTTP/1.1
>     Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
>     Accept-Encoding: gzip, deflate, br
>     Accept-Language: en-US,en;q=0.8
>     Cache-Control: max-age=0
>     Upgrade-Insecure-Requests: 1
>     User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36
>
> From the server:
>
>     200 OK
>     Access-Control-Allow-Origin: *
>     Content-Length: 13
>     Content-Type: text/plain
>     Date: Mon, 30 Oct 2023 06:08:05 GMT
>     Server: istio-envoy
>     Strict-Transport-Security: max-age=2592000; includeSubDomains
>
>     10.100.100.10
>
> In this example, a request is made to some server, with the URI being `/ip`. Some headers are included, such as the `User-Agent` header along with some other headers that indicate what the web browser expects the server to send back. The response includes the status code - `200` - meaning the request succeeded. The server provides headers as well, providing information about the server, the response itself, and so on. Finally, the response itself appears - the IP address.

### How REST works

Now that we've established the HTTP protocol, we can clearly explain how the REST API specification works. With REST, you can perform the most common CRUD operations (create, read, update, delete) by directly mapping them to four HTTP verbs:

* `Create` -> `POST`
* `Read` -> `GET`
* `Update` -> `PUT`
* `Delete` -> `DELETE`

And the way that you specify *what* operation you want to perform is via the URI itself. 

As an example, suppose we have programmed the very latest platform for video game distribution. The latest game has just dropped from our favorite publisher, and we want to list it for sale on our store so we can sell it to our millions of eager buyers.

The REST API doesn't specify the *entities* themselves - that's up to us and our application. But it does give us a *framework* for how our HTTP verbs and URIs map to operations on the server. 

So, for this example, we'll assume we have a REST API for our store. Let's list our new game! What might that request look like?...

    POST /api/v1/game
    Content-Type: application/json
    User-Agent: BoilingHotGames/1.0

    {"name":"Baldur's Gate 3","description":"The best game of 2023, amIright...","price":69.99}

As we saw, the `POST` target specifies that we want to *create* a new entity. The server replies to us...

    403 Forbidden
    Content-Type: application/json
    Server: BoilingHotServer/1.0

    {"error":"You are not logged in."}

Oops. We can't do that because we haven't given the server any authentication. If this were allowed, *anyone* could just start listing games on the store, or even worse, change the prices. Not a good thing, right? 

Let's try again...

    POST /api/v1/game
    Content-Type: application/json
    Authorization: Bearer SWYgeW91IGZpbmQgdGhpcyBtZXNzYWdlLCB5b3UgYXJlIGF3ZXNvbWUh
    User-Agent: BoilingHotGames/1.0

    {"name":"Baldur's Gate 3","description":"The best game of 2023, amIright...","price":69.99}

And the server says...

    201 Created
    Content-Type: application/json
    Server: BoilingHotServer/1.0

    {"id",42,"name":"Baldur's Gate 3","description":"The best game of 2023, amIright...","price":69.99,saleCount:0}

This particular API returned us a copy of the object we posted, along with the ID that the object was assigned in the database. This isn't strictly required, but it's a really good idea because that way the program that made the request can discover the ID that the game was given in the database. More importantly, this ID will be used later if you want to get this record back. Speaking of, let's do that...

    GET /api/v1/game/42
    User-Agent: BoilingHotGames/1.0

Server replies...

    200 OK
    Content-Type: application/json
    Server: BoilingHotServer/1.0

    {"id",42,"name":"Baldur's Gate 3","description":"The best game of 2023, amIright...","price":69.99,saleCount:61934}

*Wow!* This game is *really* selling! We only posted it for sale, like, 2 minutes ago!

Since we're a profit-focused game reseller, we definitely want to take advantage of the fact that this game is selling hot. Let's update the price...

    POST /api/v1/game/42
    Content-Type: application/json
    Authorization: Bearer SWYgeW91IGZpbmQgdGhpcyBtZXNzYWdlLCB5b3UgYXJlIGF3ZXNvbWUh
    User-Agent: BoilingHotGames/1.0

    {"price":99.99}

Server says:

    202 Accepted
    Content-Type: application/json
    Server: BoilingHotServer/1.0

    {"id",42,"name":"Baldur's Gate 3","description":"The best game of 2023, amIright...","price":99.99,saleCount:102342}

Nice! We'll have to see if that price hike keeps sales alive!

But now that we think about it, we definitely want to make sure some other popular games we might have on our store aren't affecting the sales of this great new game. And hey, those other games have been listed for *far too long!* Let's remove a game...

    DELETE /api/v1/game/32
    Authorization: Bearer SWYgeW91IGZpbmQgdGhpcyBtZXNzYWdlLCB5b3UgYXJlIGF3ZXNvbWUh
    User-Agent: BoilingHotGames/1.0

Server says:

    202 Accepted
    Server: BoilingHotServer/1.0

No response. That's good! That other *inferior* game has now been vanquished to the realm of *not for sale*! More customer money that can go towards our price-inflated game!

### What about things that don't map directly to entities?

REST APIs represent data entities as part of the URL path (the URI) and the action on the data with the HTTP verb. A typical convention is that the entity on its own represents "all" or "no ID" (e.g. `api/v1/game`) while adding an ID to the URI represents "this specific entity by ID" (e.g. `api/v1/game/42`). 

REST is just a convention - much like semantic versioning - and it's not uncommon for APIs to not follow the REST philosophy to the letter. APIs also may need other functions that aren't represented by data entities alone, and it can be argued that these uses are not strictly conforming to the REST philosophy. However, you'll often see such "violations", and they are typically necessary due to the rather limited scope of REST itself.

For example, suppose that, in our game API, we want an endpoint for *purchasing* the game. THere is no HTTP verb  called `PURCHASE`. While you technically can use any verb, and many API engines such as Python's Flask have ways to allow this, it's uncommon because that actually violates the HTTP protocol, and many proxy servers or backend load-balancing servers will not pass these requests through.

A common technique is to use an "entity" for the action itself. For example, you might have an endpoint called `api/v1/game/purchase` that accepts `PUT` requests. (In this example, note that the `purchase` endpoint is located underneath the `game` entity - this allows you to be clear which entity the action refers to. Assuming your IDs are numeric you can safely do this, because your API engine should be able to separate "all numbers" (refers to an entity) and "some string" (refers to this endpoint).)

> Of course, `purchase` may end up being its own entity in a database, so you might also just see `api/v1/purchase`. 

Two more examples are actions that may not directly impact or refer to any data entity in the database, and actions that have special meaning and/or validation steps where you don't want a direct update of a data entity but want to affect multiple entities. There's a few ways you might implement such actions. For example:

* `api/v1/game/42/download`
* `api/v1/user/resetpassword`
* `api/v1/game?sort=highest-profit`
* `api/v1/game/search?q=baldurs+gate`

And so on...

## Discussion: Web API Security

The REST API discussion talked a bit about how to secure a Web API from unauthorized access. This final discussion on APIs, specifically web APIs, will focus on the most common ways we handle authenticating to APIs.

For non-Web APIs, you might see something like "provide a license key to the class initializer" or "the class will look for a configuration file in a handful of documented places which should contain credentials." Web APIs also may do this for *backend* configurations (such as credentials for accessing a database server), but there's a few strategies that are very typical for how a *client* (such as a browser) authenticates to the API. 

Recall that REST is a *stateless API*. Therefore, there's no concept of "logging in" to a REST API. Instead, *each* request to the API must include credentials that the API can use to validate and authorize the request. Just because you sent a fully authenticated request one second ago does not mean your next request will succeed, unless you also include that authentication again.

Authentication is one piece of state that the client tracks when interacting with a REST API. We saw in the last discussion that the `Authorization` header can be used as a method for transferring these credentials. We also learned that a `Bearer` authentication token is considered sufficient authorization on its own, so a bearer token must be cryptographically secure - it must not be possible for an end user to *forge* a bearer token. 

> **Firesheep**
>
> Back in the 2000s, a Firefox extension was released that allowed you to perform "session hijacking". The extension, known as *Firesheep*, allowed you to *sniff* HTTP traffic on a local network and extract bearer tokens. The extension could then inject those tokens into your browser. The result was that you could hijack and take over someone else's Facebook session, Twitter login, and so on, without needing to know their password. It was only temporary since bearer tokens are generally designed to expire quickly, but even a few minutes of authenticated access to another person's Facebook account can result in some awful things...! (APIs also typically use a "refresh token" scheme where you can request a new token using the current token right before it expires. Therefore, this rapid expiration didn't always make things any more secure!)
>
> This was possible because, as we said, bearer tokens serve as authentication on their own. If you can get the bearer token from someone else's browser and inject it into your browser, for all intents and purposes, you *are* that person to that website. 
>
> This no longer works today, because Firesheep, along with many other cybersecurity incidents from years ago, pushed the industry to move to HTTPS. By securing and encrypting HTTP connections, it's no longer possible to steal and hijack bearer tokens by just listening to traffic on a network. However *we still use bearer tokens in the same way today*. While you can't just passively sniff tokens, and HTTPS even protects active man-in-the-middle hijacks, it's still theoretically possible to extract tokens from one browser and inject them into another on a different machine. Keep this in mind if you're a fan of using "remember my login" on websites, and other people use your computer and would have a way to access your files...!!

So what's actually in a bearer token? It looks like a jumbled mess of letters. What's actually in there is a blob of encrypted and signed data that the server generates. The idea is that the client doesn't need to, and *shouldn't*, be able to actually interpret the *contents* of the token, nor should the client be able to alter the contents of the token. The client only needs to know that the token is the authentication key, and should include the token any time it makes a request to the API. The API decides what goes into the token, and only the API can read the data back from the token (because it encrypts the token with its own private key). **This is one way that the API offers state to the client, and that the client** ***transfers state*** **back to the API!**

At a minimum, a token will typically contain some sort of user identification and an expiration time. It might be as simple as "The user `fmillion` is logged in, and this token is valid until midnight tonight." Since the token itself is considered to be secure, and thanks to encryption the client can't alter the token, the server considers this token to be a valid credential for the user `fmillion`.

> **Making a JWT**
>
> You can actually easily generate a bearer token in Python. A very common scheme is known as JSON Web Tokens. These tokens let you encode a set of key-value pairs encrypted and signed with a given passphrase. That passphrase is known only to the web server, and can in fact change dynamically and regularly on the web server's end for increased security.
>
> You can use `pip` to install the PyJWT library which lets you create and validate JSON Web Tokens.
>
> Once you have PyJWT installed, try running this code:
>
>     import jwt
>     key = "TheMostSecurePasswordEver!"
>     encoded = jwt.encode({"user": "fmillion"}, key, algorithm="HS256")
>     print(encoded)
>
> You'll get back a jumble of characters that looks something like this:
>
>     eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZm1pbGxpb24ifQ.ZLy0B2jmj5Dw9Bs-gUjRHJ3xIcvjhuq40zwPZIQdRkM
>
> That string is your bearer token! The API can decode it back to the key-value pairs provided as long as it knows the same secret key:
>
>     jwt.decode(encoded,key,algorithms="HS256") 
>     (returns "{'user': 'fmillion'}")
>
> In JWT lingo, each key-value pair is known as a *claim*. There are a small number of standardized claims that JWT implementations consider special cases. One such claim is `exp`, which represents the token's expiration time. The Python JWT library supports this directly:
>
>     jwt.encode({"exp": datetime.datetime.now(tz=timezone.utc) + datetime.datetime.timedelta(seconds=30)}, key)
>
> This creates a JWT that expires 30 seconds from now. When you use the `decode` function, and if an `exp` claim is present, PyJWT will check to see if the token is expired, and if so, an exception gets thrown.

### Other ways to use the Authorization header, and other ways to authenticate

The `Authorization` header is a de-facto standard for providing authentication and authorization information to a Web server - not just for an API, but for a Web page in general. The value of the header starts with the *type* of authentication data that is being provided. So far we've seen the `Bearer` type. Another less-common type is the `Basic` type, which is the more "traditional" approach of providing a username and password as the authentication data. You can probably already think of why this might not be the best idea today - providing a copy of your username and password with *every* request to the web server gives that many more opportunities for your password to be exfiltrated. (Bearer tokens are generally designed to expire rather quickly and refreshed, so even the bearer tokens will change often, unlike your password!)

There's a few other ways that you can authenticate to an API as well, but they're less commonly used today when compared to the bearer token:

* **Digest authentication**: This is a challenge-response design where the server includes a *challenge token* with the `401 Unauthorized` response. The client is expected to respond to this challenge by providing a cryptographic *response* (hence "challenge-response") in such a way that only a browser with knowledge of proper credentials would be able to produce. The downsides of digest authentication are that it requires the browser to retain knowledge of the user's credentials, since there's no real standard as to how often the challenge-response sequence is given. Thus, unless the browser wants to constantly and randomly ask the user for their password, it needs to remember the user's actual credentials. Bearer tokens have defined expiration times, can be automatically refreshed and do not require the browser to know the user's credentials.
* **API Key authentication**: This method uses the `Authorization` header in a custom fashion. It can be used similarly to bearer tokens, or the browser might include a pre-generated API key that does not change over time. This is almost never seen used in interactive API access, such as via a web browser. However, API keys are very common with APIs that offer up information, services or similar functionality. For example, a weather service might allow you to request an API key so that you can design a desktop or mobile application that retrieves the weather. The API key does identify you to the server, and like a bearer token the client need not be able to parse the token, however API keys are generally much longer lived than bearer tokens. 
  * If you have ever needed to use a feature with titled "app-specific password", "app token" or anything similar, you're working with a type of API key. Most commonly, API keys provide an alternative method of authenticating other than your password - it means you don't have to give your *password* to an application or automated service that might want to access an API on your behalf.
  * There are other ways the API key can be provided other than via the `Authorization` header. It might be given via a custom HTTP header such as `X-API-Key`, or it might be included in the request itself as part of the JSON body or as part of the request URI.
* **OAuth authentication**: OAuth is a modern strategy for single-sign-on authentication. If you have ever used the "Login with Facebook/Google/Apple" button on a website, you've used OAuth. OAuth is a protocol and API standard that allows a service to authenticate you based on someone else's authentication - basically, your web API can 
"trust" Google, Facebook, etc. to handle the actual verification of who you are.
  * The main advantage of OAuth is that the *web application* does not need to know any of your private credentials. Similar to how a bearer token hides your credentials from the browser or client, OAuth hides the credentials from the *server*. Conceptually, you can think of it as "a bearer token, but for the server".
  * We won't be implementing OAuth in this course, but if you're curious as to how it's done (or if you're suffering from insomnia and need to get to sleep so you can be awake for our class), check out [RFC 5849](https://datatracker.ietf.org/doc/html/rfc6749) which documents the OAuth 2.0 standard.

We're going to do an in-class exercise where you'll implement JWT authentication in our Web application today!
