# Http and Scalable Software Systems

If you think about the World Wide Web, it's easy to imagine it as a single software system. Once
you do, you realize it's the largest software system the world has ever created -- probably by
hundreds of orders of magnitude. It contains trillions of lines of code, hundreds of millions of
servers, and billions of clients, running thousands of different programming languages. Still,
it works more or less as we expect it to work. So, what made it possible for humans to create
such an enourmous software system? The answer is simple: **HTTP**!

The HTTP protocol allows you to create perfect encapsulation. The client and server don't need
to know anything about each other, besides the URL, HTTP verb, and what parameters to pass in
and expect as output. This allows billions of clients to interact with each other, without knowing
(almost) anything about each other. If you reduce it down to its basic components, it becomes
painfully obvious that the following is a recipe for winning.

1. Magic Strings (URLS)
2. Generic and untyped payloads (JSON)
3. A DNS transforming URLs to a physical address of some client/server.

## They Lied to You!

The funny thing is that this contradicts some 60 years of software development theory, with
strong typing, rigid classes, OOP, etc., where everything should be known at compile time.
If anything, the Web's success is based upon completely ignoring every single *best practice*
we as software developers have taught ourselves over the last 60+ years.

The paradox is that the above recipe, can also easily be implemented internally within our own
systems. Just throw away OOP, forget all about static and strong typing, and invoking your methods
as "method strings". You'll never again experience complexity problems, with entangled dependencies,
making it impossible to create a software system above some threshold of complexity, without reducing
it to a big ball of mud.

All of a sudden, there are no dependencies between the place in your code where you are invoking a
function and the place where you have implemented the function. There are no strong types transferred
between the caller and the method, and nothing is shared, except a mutual agreement of what data
to provide and return.

All of a sudden, an in-process method invocation has been completely decoupled from the underlying
method, and you have all the scalability features internally within your process, as you have
with the HTTP standard. 

### Isn't this SOA?

No. Service Oriented Architecture is based upon having multiple servers, and/or processes. This is
completely in-process. There's no need to fiddle with socket connections, server configurations,
or anything that creates added complexity. In fact the "DNS" of the above "Magic string method
invocations" is a simple dictionary, resembling the following:
```c#
Dictionary<string, Type> _dns;
```
Then you look up a type from your "DNS" like the following, and instantiate an instance of an
interface, making it possible to invoke your loosely coupled function.
```c#
var type = _dns["magic-string"];
var instance = services.GetService(type) as IMagicStringType;
instance.InvokeLooselyCoupledMethod(/* ... arguments ... */)
```
At this point, all you need is a Node class, capable of passing around arguments -- basically the
equivalent of JSON for C#, something resembling the following:
```c#
class Node {
    public string Name {get; set;}
    public object Value {get; set;}
    IEnumberable<Node> Children {get; set;}
}
```
Your dictionary becomes your "DNS," the magic string becomes your "URL," and the Node class becomes
your "JSON." This allows for completely independent modules to interact with each other in the same
way that the HTTP standard allows completely independent servers to interact with each other, completely
eliminating every single dependency between your two components in the process. And as to the speed of
this? Well, it's a simple dictionary lookup and an IoC dependency injection invocation. It's more
or less the same speed as anything you're already doing in your .NET applications.