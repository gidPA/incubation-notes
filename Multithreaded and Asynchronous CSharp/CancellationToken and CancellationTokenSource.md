# 🔹 Cancellation in .NET

Instead of forcing tasks/threads to stop abruptly (which risks corruption, deadlocks, etc.), .NET uses **cooperative cancellation**:

- A caller signals that some work _should_ stop.
- The callee periodically checks if cancellation was requested.
- If yes, the callee exits gracefully.

This pattern is implemented with **`CancellationToken`** and **`CancellationTokenSource`**.

---

# 🔹 `CancellationTokenSource`

A **producer** of cancellation requests.

- Think of it as the _controller_.
- It creates and manages a `CancellationToken`.
- When you call `Cancel()`, all code that uses its token will be notified.

### Important Members:

- **`Cancel()`**  
    Signals cancellation immediately.
- **`CancelAfter(TimeSpan)`** / **`CancelAfter(int milliseconds)`**  
    Schedules automatic cancellation after a delay.
- **`Token`**  
    Gets the `CancellationToken` that others can observe.
- **`Dispose()`**  
    Releases resources (important for timers used by `CancelAfter`)    

Example:

```csharp
using var cts = new CancellationTokenSource();
CancellationToken token = cts.Token;

// Request cancellation
cts.Cancel();
```

---

# 🔹 `CancellationToken`

A **consumer** of cancellation requests:
- A lightweight, immutable struct.
- Passed to methods that support cancellation.
- Represents "a handle" to know whether cancellation was requested.

### Important Members:

- **`IsCancellationRequested`**  
    `true` if cancellation has been requested.
- **`ThrowIfCancellationRequested()`**  
    Throws `OperationCanceledException` if cancellation is requested.
- **`Register(Action)`**  
    Registers a callback to run on cancellation (useful for cleanup).
- **`CanBeCanceled`**  
    Indicates whether this token is associated with a source.

Example usage:

```csharp
async Task DoWorkAsync(CancellationToken token)
{
    for (int i = 0; i < 10; i++)
    {
        token.ThrowIfCancellationRequested();
        await Task.Delay(500, token); // built-in cancellation support
        Console.WriteLine($"Step {i}");
    }
}
```

---

# 🔹 How They Work Together

```csharp
var cts = new CancellationTokenSource();
var token = cts.Token;

var task = Task.Run(async () =>
{
    await DoWorkAsync(token);
});

// Cancel after 2 seconds
cts.CancelAfter(2000);

try
{
    await task;
}
catch (OperationCanceledException)
{
    Console.WriteLine("Task was canceled.");
}
```

Flow:

1. `CancellationTokenSource` created.
2. Token passed into task.
3. After 2s, `CancelAfter` triggers `cts.Cancel()`.
4. Token is marked canceled → `Task.Delay` throws `OperationCanceledException`.
5. Caller handles it.

---

# 🔹 Key Patterns

### 1. **Cooperative polling**

Check periodically:

```csharp
while (!token.IsCancellationRequested)
{
    // Work
}
```

### 2. **Exception propagation**

Preferred in async:

```csharp
token.ThrowIfCancellationRequested();
```

### 3. **Pass-through cancellation**

Always pass `CancellationToken` down the call chain:

```csharp
await SomeOperationAsync(token);
```

### 4. **Linked tokens**

Combine multiple cancellation signals:

```csharp
using var linkedCts = CancellationTokenSource.CreateLinkedTokenSource(token1, token2);
await DoWorkAsync(linkedCts.Token);
```

---

# 🔹 Common Gotchas

1. **Forgetting to observe cancellation**  
    Cancelling without checking token → task won’t stop.
2. **Forgetting to dispose `CancellationTokenSource`**  
    If you use `CancelAfter`, a timer is created → must be disposed.
3. **Throwing wrong exception**  
    Use `OperationCanceledException` (or `TaskCanceledException`) for cancellation, not `Exception`.
4. **One-time use**  
    A `CancellationTokenSource` cannot be reset; once canceled, it's done.

---

# 🔹 Real-World Uses

- Canceling HTTP requests:
    
    ```csharp
    await httpClient.GetAsync(url, token);
    ```
    
- Canceling long-running loops when user clicks "Stop".
    
- Implementing timeouts with `CancelAfter`.
    
- Graceful shutdown of background services.
    

---

✅ **In short:**

- **`CancellationTokenSource`** = button to request cancellation.
- **`CancellationToken`** = a handle others can check to respond.
- Together, they make cooperative cancellation safe and composable in C# async and multithreaded code.


## 🔹 The Problem

You want `Scan()` to be cancellable in **two different ways**:

1. **External cancellation** → The caller (some other piece of code) passes in a `CancellationToken` so they can stop the scan early (e.g., user clicked _Cancel_).
    
2. **Internal timeout** → Even if the caller doesn’t cancel, the scan shouldn’t hang forever. You enforce a maximum time (`ManualScanTimeoutMs`).
    

---

## 🔹 The Solution: `CreateLinkedTokenSource`

```csharp
using var timeoutCts = new CancellationTokenSource(ManualScanTimeoutMs);
using var linkedCts = CancellationTokenSource.CreateLinkedTokenSource(timeoutCts.Token, externalToken);
```

- `timeoutCts` will fire when the timeout expires.
    
- `externalToken` may fire if the caller cancels.
    
- `linkedCts.Token` will fire if **either one** cancels.
    

This gives you a single token (`linkedCts.Token`) that represents _"cancel if either condition happens"_.

---

## 🔹 The `Register` Callback

```csharp
linkedCts.Token.Register(() =>
{
    if (timeoutCts.IsCancellationRequested)
    {
        // Timeout
        _manualScanTcs.TrySetException(
            new N4313TimeoutException($"Manual scanning operation timed out after {ManualScanTimeoutMs} ms")
        );
    }
    else
    {
        // External cancel
        _manualScanTcs.TrySetCanceled(externalToken);
    }
});
```

Here’s the important bit:

- If `timeoutCts` fired → Treat it as a **timeout failure** (`N4313TimeoutException`).
    
- If `externalToken` fired → Treat it as **external cancellation** (`TrySetCanceled(externalToken)`).
    

This distinction is crucial:

- A timeout is considered a protocol-level error.
    
- An external cancellation is a user/system-driven cancellation.
    

---

## 🔹 Why Not Just Use One Token?

If you only had `externalToken`, you’d have **no timeout** → a hanging scan could block indefinitely.  
If you only had `timeoutCts`, the caller could never cancel manually.

The linked approach is **composable**: any number of tokens (external, timeout, shutdown signal, etc.) can be combined.

---

## 🔹 Flow in Your Code

1. Caller calls:
    
    - `Scan()` → uses `CancellationToken.None` (timeout only applies).
        
    - `Scan(cancellationToken)` → caller + timeout both apply.
        
2. `timeoutCts` ensures that if no result comes within `ManualScanTimeoutMs`, cancellation happens.
    
3. If caller cancels earlier, `externalToken` fires first.
    
4. Either way, `linkedCts.Token` triggers, your `Register` callback fires, and `_manualScanTcs` is resolved (exception or cancellation).
    

---

✅ **Summary in plain English**:

- **External token** → lets the _caller_ say “stop now.”
- **Inner timeout token** → ensures you don’t wait forever.
- **Linked token source** → merges them so you only need to listen to one cancellation signal in your scan logic.