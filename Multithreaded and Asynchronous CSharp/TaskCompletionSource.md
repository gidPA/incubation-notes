A `TaskCompletionSource<TResult>` is a **manual promise controller** for .NET's `Task` system. TCS makes it possible to create a `Task` and control **when** and **how** it completes (success, failure, or cancellation), instead of starting with a function that _returns_ a `Task` and then letting that function dictate when it completes

TCS can be likened to this:

> “Here’s a `Task` I’ll hand to someone else, and I’ll let you know later if it’s done, failed, or cancelled.”

---

### **Basic Anatomy**

```csharp
// Create the TaskCompletionSource object
var tcs = new TaskCompletionSource<string>();

// The Task that others can await:
// Typically, this is a process that takes a while
Task<string> task = tcs.Task;

// Complete it successfully:
tcs.SetResult("Hello!");

// Or fail it:
tcs.SetException(new InvalidOperationException("Something went wrong"));

// Or cancel it:
tcs.SetCanceled();
```

Key parts:

- **`Task`** → the task given to consumers.
- **`SetResult(TResult)`** → completes successfully.
- **`SetException(Exception)`** → completes with failure.
- **`SetCanceled()`** → completes as canceled.

---

### **Why It's Useful**

TCS is very useful for these cases:

- Exposing **callback-based** or **event-driven** APIs as async/await-friendly methods.
- **Bridging** non-`Task` code into the async ecosystem.
- **Manually triggering completions** of tasks after some condition.

Example: Converting an event into a `Task`

```csharp
public Task<string> WaitForMessageAsync()
{
    var tcs = new TaskCompletionSource<string>();

    void Handler(object? sender, MessageEventArgs e)
    {
        tcs.SetResult(e.Message);
        MessageReceived -= Handler; // Unsubscribe
    }

    MessageReceived += Handler;

    return tcs.Task; // This will be automatically set when
					 // the Handler subscriber is invoked
}
```

---

## 4. **Variants & Constructors**

```csharp
var tcs = new TaskCompletionSource<TResult>();
var tcs = new TaskCompletionSource<TResult>(TaskCreationOptions.RunContinuationsAsynchronously);
```

**TaskCreationOptions**:

- **`None`** — default.
- **`RunContinuationsAsynchronously`** — ensures continuations run asynchronously, not inline on `SetResult`.
    - Prevents potential deadlocks or unexpected synchronous execution.
- **`PreferFairness`** and others exist but are less common here.

---

## 5. **"Try" vs "Set" Methods**

- `SetResult`, `SetException`, `SetCanceled` — **throw** if the task is already completed.
- `TrySetResult`, `TrySetException`, `TrySetCanceled` — **return false** if the task is already completed (safer in race conditions).

Example:

```csharp
if (!tcs.TrySetResult("done"))
{
    Console.WriteLine("Task already completed!");
}
```

---

## 6. **Thread-Safety**

- **Setting** a result is thread-safe, but only one "winner" can succeed.
- **Multiple callers** may attempt to complete the TCS — the first wins; others fail/return false depending on `Set` vs `TrySet`.

---

## 7. **Common Pitfalls**

### 🔴 Completing Inline Without `RunContinuationsAsynchronously`

If you call `SetResult` inside the same stack that someone is awaiting, their continuation might run **before your method finishes**:

```csharp
tcs.SetResult("done");
// Awaiter code might run here inline!
```

→ This can cause **deadlocks** in some `SynchronizationContext`s (like UI threads).

Fix:

```csharp
var tcs = new TaskCompletionSource<string>(
    TaskCreationOptions.RunContinuationsAsynchronously);
```

---

### 🔴 Forgetting to Handle Cancellation

If the waiting side uses a `CancellationToken`, you must listen for it and call `TrySetCanceled`.

---

### 🔴 Not Catching Exceptions from Callbacks

If the awaited task fails, `await` throws the exception — handle it or propagate it.

---

## 8. **Real-World Example — Wrapping a Timer**

```csharp
public static Task DelayUntilAsync(DateTime target)
{
    var tcs = new TaskCompletionSource<bool>(
        TaskCreationOptions.RunContinuationsAsynchronously);

    var delay = target - DateTime.Now;
    if (delay <= TimeSpan.Zero)
    {
        tcs.SetResult(true);
        return tcs.Task;
    }

    var timer = new System.Timers.Timer(delay.TotalMilliseconds);
    timer.Elapsed += (s, e) =>
    {
        timer.Dispose();
        tcs.TrySetResult(true);
    };
    timer.AutoReset = false;
    timer.Start();

    return tcs.Task;
}
```

---

## 9. **When Not to Use TCS**

- If you can use `Task.Run`, `Task.FromResult`, or existing async APIs — do that instead.
- TCS is for _manual control_, not just to create a completed task (there’s `Task.FromResult` for that).

---

## 10. **Quick Comparison Table**

|Feature|`TaskCompletionSource`|`Task.Run` / async method|
|---|---|---|
|Who completes?|**You** control completion.|CLR / compiler-generated code.|
|Execution model|Event-driven, external triggers.|Code flow determines completion.|
|Use case|Wrapping callbacks/events, bridging sync to async.|Standard async workflows.|
