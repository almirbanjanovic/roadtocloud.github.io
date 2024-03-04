---
title:  "Brewing Healthy Cloud Applications"
header:
  #teaser: "/assets/images/platform-agnostic-ci-cd-flow.png"
excerpt: "Let's dive into a coffee shop metaphor to illustrate building healthy cloud application."
classes: wide
toc: true
tags:
  - Cloud
  - Programming
  - Architecture
---

# Introduction
One common misconception is that highly-available, resilient and elastic cloud architectures represent a silver bullet.  This is not necessarily the case.  Cloud architecture is tightly coupled to healthy programming techniques - a healthy cloud architecture and healthy code hosted on those architectues are two sides of the same coin.  This fundamental concept often gets lost.  Consider having the most resilient, multi-AZ (Availability Zone), multi-Region cloud architecture that unscrupulously autoscales additional nodes horizontally to handle additional load caused by sudden spikes of REST API calls.  Now also consider that these additional calls are caused by unhealthy code. This is not only inefficient and can crash a REST API, but it can also crash an entire cloud infrastructure and cause runaway costs incurred by additional horizontal scaling.  In this post, let's examine healty programming techniques such as reusing HTTP connections and building thread-safe code with a coffee shop metaphor.

In the world of software development, optimizing cloud application performance, especially RESTP APIs, is similar to managing a highly efficient coffee shop. Just as a coffee shop tries to serve as many customers as efficiently as possible, cloud applications try to handle requests and tasks effectively. What can coffee shops teach us about reusing HTTP connections, thread-safe programming, avoiding deadlocks, and employing the singleton pattern for better performance and resource management? Let's dive in!

# Thread-Safe Programming: The Coordination of Multiple Baristas
In a busy coffee shop, multiple baristas (threads) work in parallel to serve customers efficiently. However, without proper coordination, they might bump into each other or duplicate efforts, leading to wasted resources and time. Similarly, in software applications, thread-safe programming ensures that multiple threads access shared resources (like a shared coffee machine) in a manner that prevents conflicts and ensures consistency.

## Key Concepts
First, some key concepts, simplified:

- **Synchronization Context**: Imagine the coffee shop has a rule: When your coffee is ready, it must be handed to you personally, and you must receive it where you placed the order. This "personal handover" rule is like the synchronization context in programming, ensuring some tasks are completed in a specific "place" or thread.
- **Asynchronous Task (`async/await`)**: Think of an asynchronous task like ordering a coffee at a busy café. You place your order (async) and then wait (await) for your name to be called when the coffee is ready. While waiting, you can do other things instead of standing still and blocking the line of other people wanting to order.
- **Blocking Calls (`.Result` or `.Wait()`)**: This is like insisting on standing at the counter, staring at the barista until your coffee is ready, not doing anything else, and not letting anyone else order.

## Deadlock Scenario: The Uncomfortable Stare
This example demonstrates how using `.Result` or `.Wait()` in a context where the synchronization context is captured can lead to a deadlock:
<br>
{% highlight csharp %}
{% raw %}
public class CoffeeDeadlock
{
    public static void Main(string[] args)
    {
        var result = GetContentAsync("http://coffeedeadlock.com").Result; // This can cause a deadlock
        Console.WriteLine(result);
    }

    public static async Task<string> GetContentAsync(string url)
    {
        using (var httpClient = new HttpClient())
        {
            var response = await httpClient.GetAsync(url); // Awaiting here captures the synchronization context
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
    }
}
{% endraw %}
{% endhighlight %}

1. **Placing the Order (Calling the Async Method)**: You go to the coffee shop (start your program) and order a coffee (call an asynchronous method, GetContentAsync). The coffee shop is busy (the system is doing work), so you're told to wait for your name to be called (the async operation will complete in the future).

2. **Waiting for the Coffee (Awaiting the Async Task)**: Instead of waiting normally, you decide to stand at the counter, not move and stare at the barista uncomfortably until you get your coffee (using .Result or .Wait()), effectively blocking anyone else from ordering (blocking the main thread).

3. **The Cofee Shop Rule (Synchronization Context)**: The coffee shop has a rule: Coffee must be handed to you personally at the counter (the continuation after an await must happen on the original synchronization context, or thread). But, because you're blocking the counter (the main thread), the barista can't serve anyone else, nor can they complete your order, because they need you to step aside to finish it (the async operation needs the blocked thread to continue).

4. **The Deadlock**: You're waiting for your coffee to be ready before you move (waiting for the async operation to complete), but the coffee shop can't finish making your coffee until you stop blocking the counter and stop making the barista uncomfortable with your stare (the system can't complete the async operation because it's waiting for the blocked thread to become available). As a result, everything stops. You're not moving. The barista can't serve your coffee. No one else can order. This standstill is the deadlock.

## Avoiding Deadlocks
To avoid the deadlock, you can ensure that the asynchronous method is allowed to complete without blocking the main thread. Here’s how you can do it:
<br>
{% highlight csharp %}
{% raw %}
public class CoffeeDeadlock
{
    public static async Task Main(string[] args)
    {
        var result = await GetContentAsync("http://coffeedeadlock.com").ConfigureAwait(false); // Avoids deadlock
        Console.WriteLine(result);
    }

    public static async Task<string> GetContentAsync(string url)
    {
        using (var httpClient = new HttpClient())
        {
            var response = await httpClient.GetAsync(url).ConfigureAwait(false); // Does not capture the synchronization context
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}

{% endraw %}
{% endhighlight %}

Just like in the coffee shop, where you could wait for your coffee without blocking the counter (maybe sit down or step aside), programming has a way to avoid deadlocks.

1. **Use `await` properly**: This is like waiting for your coffee without blocking the counter. You allow other customers to order, and the barista to serve other orders while yours is being prepared.
2. **`ConfigureAwait(false)`**: This tells the system, "I don't need to receive my coffee exactly at the counter where I ordered. You can call my name, and I'll pick it up wherever I am." This means the continuation of your task doesn't need to be on the original thread, avoiding the need for you to block any "place" or thread.

# Reusing HTTP Connections: The Art of Efficient Service
Imagine as soon as you walk into your favorite coffee shop, the barista knows not only your order but also prepares it by reusing the coffee grounds for multiple customers. While this might raise eyebrows in the coffee world, in the realm of HTTP connections, reusing connections (similar to coffee grounds) is efficient.

Creating a new connection for each HTTP request (akin to grinding new coffee beans for every cup) can be resource-intensive and slow. This can quickly overwhelm a node in a cloud architecture, causing autoscaling to kick in. Modern HTTP client libraries use connection pooling, similar to a coffee shop having a large pot of coffee ready to serve multiple customers quickly. This approach minimizes the overhead of establishing connections, similar to avoiding the time-consuming process of grinding beans and brewing coffee for each order.  

To illustrate the concept of reusing HTTP connections effectively, akin to the efficiency in a well-run coffee shop, let's consider a .NET example using `HttpClient`. The key here is to use a single `HttpClient` instance across multiple requests, rather than creating a new instance for each request. This approach leverages connection pooling under the hood, reducing the overhead of establishing new connections for each request and thus optimizing performance.

## Singleton Pattern: One Coffee Machine to Serve Them All
The singleton pattern in software development is like having a single, highly efficient coffee machine in a shop that all baristas share. Whether through dependency injection or static methods, this pattern ensures that only one instance of a resource (e.g., an `HttpClient` in .NET) is created and reused across the application, optimizing resource use.

### Dependency Injection (DI)
Using dependency injection to implement the singleton pattern is like a coffee shop where the manager ensures that all baristas use the same coffee machine, maintaining efficiency and consistency. It allows for flexible configuration and easy sharing of the coffee machine (or `HttpClient`) across different parts of the application.

In a .NET Core or .NET 5/6/7/8 applications, you can use the built-in DI container to manage `HttpClient` instances efficiently. This approach ensures that `HttpClient` instances are reused properly, which is crucial for managing connections and resources effectively.

1. **Configure `HttpClient` in `Startup.cs` or `Program.cs`**:
<br>
{% highlight csharp %}
{% raw %}
public void ConfigureServices(IServiceCollection services)
{
    // Other configurations...

    // Register HttpClient as a singleton indirectly through IHttpClientFactory
    services.AddHttpClient();
}
{% endraw %}
{% endhighlight %}

2. **Inject and Use `HttpClient` via `IHttpClientFactory`:**
{% highlight csharp %}
{% raw %}
public class MyService
{
    private readonly HttpClient _httpClient;

    public MyService(IHttpClientFactory httpClientFactory)
    {
        _httpClient = httpClientFactory.CreateClient();
    }

    public async Task<string> GetAsync(string url)
    {
        var response = await _httpClient.GetAsync(url);
        response.EnsureSuccessStatusCode();
        return await response.Content.ReadAsStringAsync();
    }
}
{% endraw %}
{% endhighlight %}






{% highlight csharp %}
{% raw %}

{% endraw %}
{% endhighlight %}
