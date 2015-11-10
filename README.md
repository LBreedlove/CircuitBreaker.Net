# CircuitBreaker.Net
Circuit Breaker pattern implementation for .NET. More info about the pattern [on MSDN.](https://msdn.microsoft.com/en-us/library/dn589784.aspx)

## Install

It's available via [nuget package](https://www.nuget.org/packages/CircuitBreaker.Net)  
PM> `Install-Package CircuitBreaker.Net`

Or nuget package [with sources only](https://www.nuget.org/packages/CircuitBreaker.Net.Source)  
PM> `Install-Package CircuitBreaker.Net.Source`

## Example Usage

```csharp
// Initialize the circuit breaker
var circuitBreaker = new CircuitBreaker(
    TaskScheduler.Default,
    maxFailures: 3,
    invocationTimeout: TimeSpan.FromMilliseconds(100),
    circuitResetTimeout: TimeSpan.FromMilliseconds(10000));
    
try
{
    // perform a potentially fragile call through the circuit breaker
    circuitBreaker.Execute(externalService.Call);
}
catch (CircuitBreakerOpenException)
{
    // the service is unavailable, failover here
}
catch (CircuitBreakerTimeoutException)
{
    // handle timeouts
}
catch (Exception)
{
    // handle other unexpected exceptions
}

```

## WHY?

There are not [so many of them][nuget-curcuitbreaker]. I didn't find any that would suit me. [Polly][polly] seems the most mature from all of them but it doesn't provide a way to specify a separate `TaskScheduler` to execute actions. But that's a crucial aspect when you call a third-party service, because those calls could stuff your "main" `TaskScheduler`. Actually none of those libraries support injection of a `TaskScheduler`. `Helpful.CircuitBreaker` by RokitSalad isn't thread safe. `CircuitBreaker` by kylos101 executes actions on the same thread. `ManagedCircuitBreaker` by AsherW is a fork of `Polly` with emphasize on IoC containers. The code provided on MSDN isn't production ready and just a piece of code. And so on so forth. So that the yet another library was born. I hope you will find it helpful. :wink:


  [nuget-curcuitbreaker]: https://www.nuget.org/packages?q=circuit+breaker
  [polly]: https://github.com/michael-wolfenden/Polly
