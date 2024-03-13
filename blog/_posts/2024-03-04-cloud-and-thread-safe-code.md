---
title:  "Brewing Healthy Cloud Applications"
header:
  teaser: "/assets/images/cloud-code-coffee.jpg"
excerpt: "Let's dive into a coffee shop metaphor to illustrate building healthy cloud application."
classes: wide
toc: true
tags:
  - Cloud
  - Programming
  - Architecture
---

<figure class="align-left">
	<a href="/assets/images/cloud-code-coffee.jpg"><img src="/assets/images/cloud-code-coffee.jpg"></a>
</figure>

# Introduction
One common misconception is that highly-available, resilient and elastic cloud architectures represent a silver bullet.  This is not necessarily the case.  Cloud architecture is tightly coupled to healthy programming techniques - a healthy cloud architecture and healthy code hosted on those architectues are two sides of the same coin.  This fundamental concept often gets lost.  Consider having the most resilient, multi-AZ (Availability Zone), multi-Region cloud architecture that unscrupulously autoscales additional nodes horizontally to handle additional load caused by sudden spikes of REST API calls.  Now also consider that these additional calls are caused by unhealthy code. This is not only inefficient and can crash a REST API, but it can also crash an entire cloud infrastructure and cause runaway costs incurred by additional horizontal scaling.  In this post, let's examine healthy programming techniques such as reusing HTTP connections and building thread-safe code with a coffee shop metaphor.

In the world of software development, optimizing cloud application performance, especially REST APIs, is similar to managing a highly efficient coffee shop. Just as a coffee shop tries to serve as many customers as efficiently as possible, cloud applications try to handle requests and tasks effectively. What can coffee shops teach us about reusing HTTP connections, thread-safe programming, avoiding deadlocks, and employing the singleton pattern for better performance and resource management? Let's dive in!

# Reusing HTTP Connections: The Art of Efficient Service
Imagine as soon as you walk into your favorite coffee shop, the barista immediately serves your favorite coffee from a large coffee pot.  I'd call that efficient service.

Creating a new connection for each HTTP request (similar to grinding new coffee beans for every cup) can put additional load on your web servers, aka cloud compute nodes. This can quickly overwhelm a node in a cloud architecture, causing autoscaling to kick in. Modern HTTP client libraries use connection pooling, similar to a coffee shop having a large pot of coffee ready to serve multiple customers quickly. This approach minimizes the overhead of establishing new connections for every request, similar to avoiding the time-consuming process of grinding beans and brewing coffee for every single cup of coffee.  

## Singleton Pattern: One Coffee Machine to Serve Them All
The singleton pattern in software development is like having a single, highly efficient coffee machine in a shop that all baristas share. This pattern ensures that only one instance of a resource (e.g., an `HttpClient` in .NET) is created and reused across the application, optimizing resource cloud hosting cost and avoiding runaway autoscaling of cloud compute nodes.

## Dependency Injection (DI)
Use dependency injection to implement the singleton pattern - it is like a coffee shop where the manager ensures that all baristas use the same coffee machine, maintaining efficiency and consistency. It allows for flexible configuration and easy sharing of the coffee machine (or `HttpClient`) across different parts of the application.

In a .NET Core or .NET 5/6/7/8 application, you can use the built-in DI container to manage `HttpClient` instances efficiently. This approach ensures that `HttpClient` instances are reused properly, which is crucial for managing connections and resources effectively.

**Step 1: Define Typed Client**

First, create a class that will serve as your typed client. This class will encapsulate all logic for making HTTP requests to a specific external service.

{% highlight csharp %}
{% raw %}
using System.Net.Http;
using System.Threading.Tasks;

public class CoffeeServiceClient
{
    private readonly HttpClient _httpClient;

    public CoffeeServiceClient(HttpClient httpClient)
    {
        _httpClient = httpClient;
        // Assuming the external service requires an API key in the header
        _httpClient.DefaultRequestHeaders.Add("ApiKey", "YourApiKeyHere");
	// Other configuration goes here
    }

    public async Task<string> GetCoffeeAsync(string typeOfRoast)
    {
        var response = await _httpClient.GetAsync($"coffee/{typeOfRoast}");
        response.EnsureSuccessStatusCode();
        return await response.Content.ReadAsStringAsync();
    }
}
{% endraw %}
{% endhighlight %}

**Step 2: Configure the Typed Client in `Program.cs`**:

In your `Program.cs`, register the typed client with the dependency injection (DI) container using AddHttpClient. This method allows you to configure the HttpClient that will be injected into your typed client.

{% highlight csharp %}
{% raw %}
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

// Register the typed client with HttpClient configured
builder.Services.AddHttpClient<CoffeeServiceClient>(client =>
{
    client.BaseAddress = new Uri("https://api.coffeeapi.com/v1/");
    // Set other default configurations here
});

var app = builder.Build();

// Map controllers and run app (assuming you're using controllers)
app.MapControllers();

app.Run();
{% endraw %}
{% endhighlight %}

**Step 3: Use the Typed Client in a Controller**

Inject the typed client into your controllers or services where you need to make HTTP requests. 

{% highlight csharp %}
{% raw %}
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;

[ApiController]
[Route("[controller]")]
public class CoffeeController : ControllerBase
{
    private readonly CoffeeServiceClient _coffeeServiceClient;

    public CoffeeController(CoffeeServiceClient coffeeServiceClient)
    {
        _coffeeServiceClient = coffeeServiceClient;
    }

    [HttpGet("{typeOfRoast}")]
    public async Task<IActionResult> Get(string typeOfRoast)
    {
        try
        {
            var data = await _coffeeServiceClient.GetCoffeeAsync(typeOfRoast);
            return Ok(data);
        }
        catch (HttpRequestException e)
        {
            return StatusCode(500, e.Message);
        }
    }
}

{% endraw %}
{% endhighlight %}
<br>

# Thread-Safe Programming: The Coordination of Multiple Baristas
In a busy coffee shop, multiple baristas (threads) work in parallel to serve customers efficiently. However, without proper coordination, they might bump into each other or duplicate efforts, leading to wasted resources and time. Similarly, in software applications, thread-safe programming ensures that multiple threads access shared resources (like a shared coffee machine) in a manner that prevents conflicts and ensures consistency.

## Key Concepts
First, some key concepts, simplified:

- **Synchronization Context**: Imagine the coffee shop has a rule: When your coffee is ready, it must be handed to you personally, and you must receive it where you placed the order. This "personal handover" rule is like the synchronization context in programming, ensuring some tasks are completed in a specific "place" or thread.
- **Asynchronous Task (`async/await`)**: An asynchronous task is like ordering a coffee at a busy coffee shop. You place your order (`async`) and then wait (`await`) for your name to be called when the coffee is ready. While waiting, you can do other things instead of standing still and blocking the line of other people wanting to order.
- **Blocking Calls (`.Result` or `.Wait()`)**: This is like insisting on standing at the counter, staring at the barista uncomfortably until your coffee is ready, not doing anything else, and not letting anyone else order.

## Deadlock Scenario: The Uncomfortable Stare
This example demonstrates how using `.Result` or `.Wait()` in a context where the synchronization context is captured can lead to a deadlock:
<br>
{% highlight csharp %}
{% raw %}
[HttpGet("{typeOfRoast}")]
public IActionResult Get(string typeOfRoast)
{
    try
    {
        // Incorrectly using .Result can lead to a deadlock
        var data = _coffeeServiceClient.GetCoffeeAsync(typeOfRoast).Result;
        return Ok(data);
    }
    catch (HttpRequestException e)
    {
        return StatusCode(500, e.Message);
    }
}

{% endraw %}
{% endhighlight %}
<br>

1. **Placing the Order (Calling the Async Method)**: You go to the coffee shop and order a coffee (call an asynchronous method, GetCoffeeAsync). The coffee shop is busy (the system is doing work), so you're told to wait for your name to be called (the async operation will complete in the future).

2. **Waiting for the Coffee (Awaiting the Async Task)**: Instead of waiting normally, you decide to stand at the counter, not move and stare at the barista uncomfortably until you get your coffee (using .Result or .Wait()), effectively blocking anyone else from ordering (blocking the main thread).

3. **The Coffee Shop Rule (Synchronization Context)**: The coffee shop has a rule: Coffee must be handed to you personally at the counter (the continuation after an await must happen on the original synchronization context, or thread). But, because you're blocking the counter (the main thread), the barista can't serve anyone else, nor can they complete your order, because they need you to step aside to finish it (the async operation needs the blocked thread to continue).

4. **The Deadlock**: You're waiting for your coffee to be ready before you move (waiting for the async operation to complete), but the coffee shop can't finish making your coffee until you stop blocking the counter and stop making the barista uncomfortable with your stare (the system can't complete the async operation because it's waiting for the blocked thread to become available). As a result, everything stops. You're not moving. The barista can't serve your coffee. No one else can order. This standstill is the deadlock.

## Avoiding Deadlocks
To avoid the deadlock, you can ensure that the asynchronous method is allowed to complete without blocking the main thread. Here’s how you can do it:
<br>
{% highlight csharp %}
{% raw %}
[HttpGet("{typeOfRoast}")]
public async Task<IActionResult> Get(string typeOfRoast)
{
    try
    {
        // Properly awaiting the asynchronous operation
        var data = await _coffeeServiceClient.GetCoffeeAsync(typeOfRoast);
        return Ok(data);
    }
    catch (HttpRequestException e)
    {
        return StatusCode(500, e.Message);
    }
}
{% endraw %}
{% endhighlight %}

Just like in the coffee shop, where you could wait for your coffee without blocking the counter (maybe sit down or step aside), programming has a way to avoid deadlocks.

1. **Use `await` properly**: This is like waiting for your coffee without blocking the counter. You allow other customers to order, and the barista to serve other orders while yours is being prepared.
2. **`ConfigureAwait(false)`**: This tells the system, "I don't need to receive my coffee exactly at the counter where I ordered. You can call my name, and I'll pick it up wherever I am." This means the continuation of your task doesn't need to be on the original thread, avoiding the need for you to block any "place" or thread.

# Conclusion
Just as the goal of a coffee shop is to serve its customers efficiently and effectively, the goal of software development is to create applications that handle tasks and requests with optimal performance. By drawing lessons from the operation of a coffee shop—reusing resources like HTTP connections, ensuring thread-safe programming, avoiding deadlocks, and efficiently managing shared resources through the singleton pattern—we can brew applications that stand out for their performance and reliability, much like a cup of finely crafted coffee.
