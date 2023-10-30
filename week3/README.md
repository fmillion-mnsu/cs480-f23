# Week 3: APIs, Compatibility and Versioning

## Presentations:

### Tuesday:

* [A Brief Review of APIs](#discussion-a-brief-review-of-api)
* [Semantic Versioning](#discussion-semantic-versioning)

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

### A little HTTP background

HTTP is the protocol that drives the World Wide Web. Every time you access a web site using your web browser, you're using HTTP. (HTTPS is just HTTP with encryption. The underlying protocol is still HTTP.) HTTP is a very old protocol that has gone through a couple of revisions, but its core functionality is the same as it's been for years.

An HTTP transaction begins with a **request** - your web browser sends a command to the server that asks for something. The request consists of up to three parts, two of which are required: the **verb**, the **URI** and the **headers**. 

The **verb** is typically represented in all-caps (but it's not strictly required, and most servers will work just fine if you give the verb in lowercase). The most common verbs - which directly relate to operations in REST - are `GET`, `PUT`, `POST`, and `DELETE`. There's a few other ones that have special use cases, such as `OPTIONS`, but REST APIs can be implemented with just those four verbs.

*To be continued...*