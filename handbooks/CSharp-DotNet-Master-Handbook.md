# C# / .NET Interview-First Master Handbook
### Candidate + Interviewer Edition (Evergreen)

> **Design goal:** One book you can use to prepare, revise, and drive interviews — built exactly the way interview conversations progress.

---

# TABLE OF CONTENTS

## [PART 1 — "What Happens When...?" (Interview Entry Points)](#part-1--what-happens-when-interview-entry-points)
- [Q1. What happens when a .NET application starts?](#q1-what-happens-when-a-net-application-starts)
- [Q2. What is the difference between a Process, a Thread, and an AppDomain?](#q2-what-is-the-difference-between-a-process-a-thread-and-an-appdomain)
- [Q3. Describe the CLR bootstrapping sequence in detail.](#q3-describe-the-clr-bootstrapping-sequence-in-detail)
- [Q4. Explain the journey from source code to execution.](#q4-explain-the-journey-from-source-code-to-execution)
- [Q5. What is the difference between managed and unmanaged code?](#q5-what-is-the-difference-between-managed-and-unmanaged-code)

## [PART 2 — Web & Application Request Lifecycle](#part-2--web--application-request-lifecycle)
- [Q6. Describe the ASP.NET (System.Web) execution pipeline.](#q6-describe-the-aspnet-systemweb-execution-pipeline)
- [Q7. Walk through the ASP.NET MVC request lifecycle in detail.](#q7-walk-through-the-aspnet-mvc-request-lifecycle-in-detail)
- [Q8. Explain the MVC Filter execution pipeline.](#q8-explain-the-mvc-filter-execution-pipeline)
- [Q9. Razor vs legacy view engines — what changed?](#q9-razor-vs-legacy-view-engines--what-changed)
- [Q10. Describe the ASP.NET Core request lifecycle.](#q10-describe-the-aspnet-core-request-lifecycle)
- [Q11. MVC vs Middleware — how do they relate conceptually?](#q11-mvc-vs-middleware--how-do-they-relate-conceptually)

## [PART 3 — Why .NET Exists (CLR, IL, CTS, CLS)](#part-3--why-net-exists-clr-il-cts-cls)
- [Q12. What problems existed before .NET that it was designed to solve?](#q12-what-problems-existed-before-net-that-it-was-designed-to-solve)
- [Q13. What are the responsibilities of the CLR?](#q13-what-are-the-responsibilities-of-the-clr)
- [Q14. What is IL / MSIL / CIL? Why is code "half-compiled"?](#q14-what-is-il--msil--cil-why-is-code-half-compiled)
- [Q15. Explain JIT compilation strategies.](#q15-explain-jit-compilation-strategies)
- [Q16. What is the Common Type System (CTS)?](#q16-what-is-the-common-type-system-cts)
- [Q17. What is the CLS and why does it matter?](#q17-what-is-the-cls-and-why-does-it-matter)

## [PART 4 — Assemblies, Versioning & Deployment](#part-4--assemblies-versioning--deployment)
- [Q18. What is an assembly really?](#q18-what-is-an-assembly-really)
- [Q19. What is the difference between a strong-named and weak-named assembly?](#q19-what-is-the-difference-between-a-strong-named-and-weak-named-assembly)
- [Q20. Private vs Shared assemblies — what's the difference?](#q20-private-vs-shared-assemblies--whats-the-difference)
- [Q21. How does the assembly resolution process work?](#q21-how-does-the-assembly-resolution-process-work)
- [Q22. Namespace vs Assembly — what's the difference?](#q22-namespace-vs-assembly--whats-the-difference)

## [PART 5 — Memory Management & Garbage Collector](#part-5--memory-management--garbage-collector)
- [Q23. Describe the managed heap architecture.](#q23-describe-the-managed-heap-architecture)
- [Q24. How does object allocation work? What are GC roots?](#q24-how-does-object-allocation-work-what-are-gc-roots)
- [Q25. What triggers garbage collection?](#q25-what-triggers-garbage-collection)
- [Q26. Explain GC Generations: Gen0, Gen1, Gen2.](#q26-explain-gc-generations-gen0-gen1-gen2)
- [Q27. What is the Large Object Heap (LOH)?](#q27-what-is-the-large-object-heap-loh)
- [Q28. Why does GC exist but memory leaks still happen?](#q28-why-does-gc-exist-but-memory-leaks-still-happen)
- [Q29. Explain Finalize vs Dispose.](#q29-explain-finalize-vs-dispose)
- [Q30. Why is GC.Collect() dangerous?](#q30-why-is-gccollect-dangerous)

## [PART 6 — C# Type System & Object Lifetime](#part-6--c-type-system--object-lifetime)
- [Q31. What is the real difference between value types and reference types?](#q31-what-is-the-real-difference-between-value-types-and-reference-types)
- [Q32. What is boxing and unboxing? Why should you care?](#q32-what-is-boxing-and-unboxing-why-should-you-care)
- [Q33. What is the difference between `var`, `object`, and `dynamic`?](#q33-what-is-the-difference-between-var-object-and-dynamic)
- [Q34. What are nullable value types?](#q34-what-are-nullable-value-types)

## [PART 7 — Strings, Immutability & Performance](#part-7--strings-immutability--performance)
- [Q35. Why is `string` immutable in C#?](#q35-why-is-string-immutable-in-c)
- [Q36. What is the string literal pool (interning)?](#q36-what-is-the-string-literal-pool-interning)
- [Q37. `string` vs `String` — is there a difference?](#q37-string-vs-string--is-there-a-difference)
- [Q38. When should you use StringBuilder vs string concatenation?](#q38-when-should-you-use-stringbuilder-vs-string-concatenation)

## [PART 8 — Object-Oriented Design](#part-8--object-oriented-design)
- [Q39. What does encapsulation really mean?](#q39-what-does-encapsulation-really-mean)
- [Q40. When does inheritance break systems?](#q40-when-does-inheritance-break-systems)
- [Q41. Explain polymorphism — runtime vs compile-time.](#q41-explain-polymorphism--runtime-vs-compile-time)
- [Q42. What is the difference between `virtual`, `override`, and `new`?](#q42-what-is-the-difference-between-virtual-override-and-new)

## [PART 9 — Constructors, Lifetime & Control](#part-9--constructors-lifetime--control)
- [Q43. What are the rules around constructor behavior?](#q43-what-are-the-rules-around-constructor-behavior)
- [Q44. Why do private constructors exist?](#q44-why-do-private-constructors-exist)
- [Q45. Explain static constructors and their guarantees.](#q45-explain-static-constructors-and-their-guarantees)
- [Q46. Do destructors (finalizers) make sense in C#?](#q46-do-destructors-finalizers-make-sense-in-c)

## [PART 10 — Abstract Classes vs Interfaces](#part-10--abstract-classes-vs-interfaces)
- [Q47. When should you choose an abstract class over an interface?](#q47-when-should-you-choose-an-abstract-class-over-an-interface)
- [Q48. What is explicit interface implementation and when is it useful?](#q48-what-is-explicit-interface-implementation-and-when-is-it-useful)
- [Q49. How did C# 8 default interface methods change the equation?](#q49-how-did-c-8-default-interface-methods-change-the-equation)

## [PART 11 — Structs, Enums & Special Types](#part-11--structs-enums--special-types)
- [Q50. Structs vs classes — when should you use structs?](#q50-structs-vs-classes--when-should-you-use-structs)
- [Q51. What are the dangers of mutable structs?](#q51-what-are-the-dangers-of-mutable-structs)
- [Q52. Enums — design considerations and pitfalls.](#q52-enums--design-considerations-and-pitfalls)

## [PART 12 — Delegates, Events & Functional Constructs](#part-12--delegates-events--functional-constructs)
- [Q53. What is a delegate really?](#q53-what-is-a-delegate-really)
- [Q54. Single-cast vs multicast delegates.](#q54-single-cast-vs-multicast-delegates)
- [Q55. How do events differ from delegates?](#q55-how-do-events-differ-from-delegates)
- [Q56. Anonymous methods vs lambda expressions.](#q56-anonymous-methods-vs-lambda-expressions)

## [PART 13 — LINQ, Iteration & Deferred Execution](#part-13--linq-iteration--deferred-execution)
- [Q57. What is deferred execution and why does it matter?](#q57-what-is-deferred-execution-and-why-does-it-matter)
- [Q58. IEnumerable vs IEnumerator — what's the difference?](#q58-ienumerable-vs-ienumerator--whats-the-difference)
- [Q59. How does `yield return` work?](#q59-how-does-yield-return-work)
- [Q60. Common LINQ performance traps.](#q60-common-linq-performance-traps)

## [PART 14 — Threading, Async & Concurrency](#part-14--threading-async--concurrency)
- [Q61. Process vs Thread — what's the real difference?](#q61-process-vs-thread--whats-the-real-difference)
- [Q62. Explain thread safety fundamentals.](#q62-explain-thread-safety-fundamentals)
- [Q63. What is a deadlock and how do you prevent it?](#q63-what-is-a-deadlock-and-how-do-you-prevent-it)
- [Q64. Explain the Singleton pattern's threading pitfalls.](#q64-explain-the-singleton-patterns-threading-pitfalls)
- [Q65. Task vs Thread — what's the difference?](#q65-task-vs-thread--whats-the-difference)
- [Q66. What really happens with async/await?](#q66-what-really-happens-with-asyncawait)
- [Q67. What is the sync-over-async anti-pattern?](#q67-what-is-the-sync-over-async-anti-pattern)
- [Q68. What is ThreadPool starvation?](#q68-what-is-threadpool-starvation)

## [PART 15 — ASP.NET MVC State & Security](#part-15--aspnet-mvc-state--security)
- [Q69. ViewData vs ViewBag vs TempData — when to use each?](#q69-viewdata-vs-viewbag-vs-tempdata--when-to-use-each)
- [Q70. Explain model binding in ASP.NET MVC.](#q70-explain-model-binding-in-aspnet-mvc)
- [Q71. Forms authentication vs Windows authentication.](#q71-forms-authentication-vs-windows-authentication)

## [PART 16 — Data, ORM, SQL & Performance Thinking](#part-16--data-orm-sql--performance-thinking)
- [Q72. Temporary tables vs table variables in SQL Server.](#q72-temporary-tables-vs-table-variables-in-sql-server)
- [Q73. Explain normalization forms (1NF through 3NF).](#q73-explain-normalization-forms-1nf-through-3nf)
- [Q74. LINQ to SQL vs LINQ to Objects — what's the difference?](#q74-linq-to-sql-vs-linq-to-objects--whats-the-difference)
- [Q75. ORM trade-offs and performance considerations.](#q75-orm-trade-offs-and-performance-considerations)

## [PART 17 — Serialization, WCF & Remoting](#part-17--serialization-wcf--remoting)
- [Q76. What is serialization and why do we need it?](#q76-what-is-serialization-and-why-do-we-need-it)
- [Q77. XmlSerializer vs BinaryFormatter — when to use which?](#q77-xmlserializer-vs-binaryformatter--when-to-use-which)
- [Q78. WCF basics — concurrency and instancing modes.](#q78-wcf-basics--concurrency-and-instancing-modes)

## [PART 18 — Modern .NET Additions](#part-18--modern-net-additions)
- [Q79. .NET Framework vs .NET Core — what was the philosophical shift?](#q79-net-framework-vs-net-core--what-was-the-philosophical-shift)
- [Q80. Why was System.Web removed?](#q80-why-was-systemweb-removed)
- [Q81. Explain the built-in Dependency Injection in .NET Core.](#q81-explain-the-built-in-dependency-injection-in-net-core)
- [Q82. Configuration and logging providers in .NET Core.](#q82-configuration-and-logging-providers-in-net-core)
- [Q83. Middleware vs Filters — when to use each?](#q83-middleware-vs-filters--when-to-use-each)
- [Q84. What are Minimal APIs?](#q84-what-are-minimal-apis)
- [Q85. What is the observability mindset?](#q85-what-is-the-observability-mindset)

## [PART 19 — How Interviewers Think (Meta Layer)](#part-19--how-interviewers-think-meta-layer)
- [Q86. Why do interviewers ask "simple" questions?](#q86-why-do-interviewers-ask-simple-questions)
- [Q87. What are the common follow-up question trees?](#q87-what-are-the-common-follow-up-question-trees)
- [Q88. What signals indicate real experience vs memorized answers?](#q88-what-signals-indicate-real-experience-vs-memorized-answers)
- [Q89. Common traps candidates fall into.](#q89-common-traps-candidates-fall-into)
- [Q90. How to steer a technical discussion in your favor.](#q90-how-to-steer-a-technical-discussion-in-your-favor)

---

# PART 1 — "What Happens When…?" (Interview Entry Points)

> *Almost every interview starts here. This is the warm-up round — but strong answers here set the tone for the entire interview.*

---

### Q1. What happens when a .NET application starts?

**Expected Answer:**

1. **OS loads the executable** — The OS loader reads the PE (Portable Executable) header of the `.exe` file.
2. **CLR bootstrap** — The PE header contains a reference to `mscoree.dll` (the CLR shim). The OS calls `_CorExeMain()` which initializes the CLR.
3. **CLR initialization** — The CLR sets up:
   - Garbage Collector
   - JIT Compiler
   - Type Loader
   - Security system
   - Thread pool
   - Exception handling infrastructure
4. **Assembly loading** — The CLR loads the entry assembly and resolves its dependencies.
5. **JIT compilation** — The `Main()` method's IL is JIT-compiled to native code.
6. **Execution begins** — The native code for `Main()` executes.

**Key talking point:** The CLR acts as an execution engine — your C# code never runs directly. It's always IL that gets JIT-compiled to native machine code at runtime.

**Follow-up trap:** *"Is Main() compiled before or during execution?"* — It's compiled **during** execution (JIT = Just-In-Time). The method is compiled the first time it's called.

---

### Q2. What is the difference between a Process, a Thread, and an AppDomain?

**Expected Answer:**

| Concept | What it is | Isolation Level | Resource Ownership |
|---------|-----------|----------------|-------------------|
| **Process** | An OS-level execution unit with its own memory space | Full isolation (separate address space) | Owns memory, handles, threads |
| **Thread** | A unit of execution within a process | No memory isolation (shares process memory) | Has its own stack, shares heap |
| **AppDomain** | A logical container within a CLR process (.NET Framework only) | Partial isolation (type safety enforced, but same process memory) | Own assemblies, static variables |

```
Process
├── AppDomain 1 (Default)
│   ├── Thread 1 (Main thread)
│   ├── Thread 2
│   └── Loaded assemblies...
├── AppDomain 2
│   ├── Thread 3
│   └── Loaded assemblies...
└── Shared: GC heap, ThreadPool
```

**Critical nuance:** AppDomains were removed in .NET Core/.NET 5+. In modern .NET, process isolation (or `AssemblyLoadContext` for assembly isolation) replaces AppDomains.

**Follow-up:** *"Why were AppDomains removed?"* — They added complexity without true isolation. A rogue thread in one AppDomain could still corrupt shared memory. Process-level isolation is more robust and aligns with container-based deployment.

---

### Q3. Describe the CLR bootstrapping sequence in detail.

**Expected Answer:**

1. **PE Header parsing** — OS reads the `.exe` PE header, finds the CLR header pointing to `mscoree.dll`.
2. **Shim loading** — `mscoree.dll` (the CLR shim/host) is loaded. It determines which CLR version to use.
3. **Runtime initialization** — `CLRCreateInstance()` or `CorBindToRuntimeEx()` loads the actual runtime (`clrjit.dll`, `clr.dll`).
4. **Subsystem startup:**
   - **GC initialization** — Managed heap segments allocated (SOH + LOH).
   - **JIT compiler ready** — `clrjit.dll` loaded.
   - **Type system** — Core types from `mscorlib`/`System.Private.CoreLib` loaded.
   - **ThreadPool** — Worker threads and I/O completion port threads initialized.
   - **Security** — CAS (Code Access Security) in .NET Framework; removed in Core.
5. **Entry point resolution** — CLR locates `Main()` via assembly metadata.
6. **JIT + Execute** — `Main()` IL is JIT-compiled and execution begins.

**In .NET Core/5+:** The process is simpler — `dotnet.exe` (or the self-contained host) loads `hostfxr.dll` → `hostpolicy.dll` → `coreclr.dll`. No shim layer.

---

### Q4. Explain the journey from source code to execution.

**Expected Answer:**

```
C# Source Code (.cs)
        │
        ▼  [C# Compiler (Roslyn)]
IL Code + Metadata (.dll / .exe)   ← "Assembly"
        │
        ▼  [CLR loads assembly]
IL Code in memory
        │
        ▼  [JIT Compiler — per method, on first call]
Native Machine Code
        │
        ▼  [CPU executes]
Running Application
```

**Three-stage compilation:**

1. **Compilation (Build time):** Roslyn compiles C# → IL (Intermediate Language) + metadata. The output is a managed assembly (`.dll` or `.exe`).
2. **IL generation:** IL is CPU-independent bytecode. It contains opcodes for a stack-based virtual machine. Metadata describes types, methods, and references.
3. **JIT execution (Runtime):** When a method is first called, the JIT compiler converts its IL to native CPU instructions. Subsequent calls reuse the compiled native code (no re-JIT).

**Key insight:** This two-phase compilation (source → IL → native) is what enables:
- **Platform independence** — Same IL runs on different CPUs/OS.
- **Runtime optimization** — JIT can optimize for the actual CPU it runs on.
- **Language interoperability** — VB.NET, F#, C# all compile to IL.

---

### Q5. What is the difference between managed and unmanaged code?

**Expected Answer:**

| Aspect | Managed Code | Unmanaged Code |
|--------|-------------|----------------|
| **Runs under** | CLR supervision | Directly on the OS |
| **Memory** | GC handles allocation/deallocation | Manual memory management (malloc/free) |
| **Type safety** | Enforced by CLR | Programmer's responsibility |
| **Examples** | C#, VB.NET, F# | C, C++, COM components |
| **Security** | CAS, verifiable IL | OS-level security only |
| **Interop** | Can call unmanaged via P/Invoke or COM Interop | Can host CLR to call managed |

**Real-world scenario:** "In our application, we use P/Invoke to call a C++ tax computation engine. The managed C# code communicates with the unmanaged COM component through a binary protocol. We must be careful with marshaling — incorrect type mappings can corrupt data silently."

**Follow-up:** *"Can managed code have memory leaks?"* — Yes! Event handler subscriptions, static references, and undisposed unmanaged wrappers are common sources. The GC can only collect objects with no live references.

---

[⬆ Back to Index](#table-of-contents)

# PART 2 — Web & Application Request Lifecycle

> *This section tests architectural understanding. Interviewers want to see that you understand the full pipeline, not just controller code.*

---

### Q6. Describe the ASP.NET (System.Web) execution pipeline.

**Expected Answer:**

This is the classic .NET Framework pipeline (IIS-hosted):

```
HTTP Request → IIS → ISAPI Filter (aspnet_isapi.dll) → ASP.NET Worker Process (w3wp.exe)
    │
    ▼
HttpRuntime.ProcessRequest()
    │
    ▼
HttpApplication pipeline (events):
    1. BeginRequest
    2. AuthenticateRequest
    3. AuthorizeRequest
    4. ResolveRequestCache
    5. MapRequestHandler    ← IHttpHandler selected
    6. AcquireRequestState  ← Session loaded
    7. PreRequestHandlerExecute
    8. [Handler.ProcessRequest()]  ← Your page/controller runs
    9. PostRequestHandlerExecute
   10. ReleaseRequestState  ← Session saved
   11. UpdateRequestCache
   12. EndRequest
    │
    ▼
HTTP Response → Client
```

**Key concepts:**
- **HttpModules** hook into pipeline events (like middleware).
- **HttpHandlers** process specific request types (`.aspx` → `PageHandlerFactory`, MVC → `MvcHandler`).
- **Global.asax** events map to these pipeline stages.

---

### Q7. Walk through the ASP.NET MVC request lifecycle in detail.

**Expected Answer:**

```
Browser Request: GET /Products/Details/5
        │
        ▼
1. ROUTING
   RouteTable.Routes matched → RouteData { controller="Products", action="Details", id=5 }
        │
        ▼
2. MVC HANDLER
   MvcRouteHandler creates MvcHandler (implements IHttpHandler)
        │
        ▼
3. CONTROLLER FACTORY
   DefaultControllerFactory creates ProductsController
   (Dependency Injection resolves constructor parameters)
        │
        ▼
4. ACTION INVOKER
   ControllerActionInvoker selects Details(int id) method
        │
        ▼
5. FILTERS EXECUTE (in order):
   a. Authorization filters  → [Authorize]
   b. Action filters (OnActionExecuting)
   c. ACTION METHOD EXECUTES → Details(5) returns ViewResult
   d. Action filters (OnActionExecuted)
   e. Result filters (OnResultExecuting)
   f. VIEW ENGINE finds Details.cshtml
   g. VIEW RENDERS to HTML
   h. Result filters (OnResultExecuted)
        │
        ▼
6. RESPONSE
   HTML sent to browser
        │
        ▼
7. CLEANUP
   Controller.Dispose() called → releases resources
```

---

### Q8. Explain the MVC Filter execution pipeline.

**Expected Answer:**

Filters execute in a specific order and wrap around the action method:

```
Request
  └→ Authorization Filters     [IAuthorizationFilter]
       └→ Action Filters        [IActionFilter]
            ├→ OnActionExecuting
            ├→ ACTION METHOD
            └→ OnActionExecuted
                 └→ Result Filters  [IResultFilter]
                      ├→ OnResultExecuting
                      ├→ RESULT EXECUTION (view rendering)
                      └→ OnResultExecuted
  └→ Exception Filters          [IExceptionFilter]
       (catch any unhandled exceptions from above)
```

**Execution order within each filter type:**
1. Global filters (registered in `FilterConfig`)
2. Controller-level attributes
3. Action-level attributes

**Key point:** If an Authorization filter short-circuits (sets `Result`), no subsequent filters or the action method execute.

```csharp
public class CustomAuthFilter : IAuthorizationFilter
{
    public void OnAuthorization(AuthorizationFilterContext context)
    {
        if (!IsAuthorized(context))
        {
            // Short-circuit — nothing else runs
            context.Result = new UnauthorizedResult();
        }
    }
}
```

---

### Q9. Razor vs legacy view engines — what changed?

**Expected Answer:**

| Feature | WebForms View Engine | Razor View Engine |
|---------|---------------------|-------------------|
| Syntax | `<%= expression %>`, `<% code %>` | `@expression`, `@{ code }` |
| File extension | `.aspx` | `.cshtml` (C#), `.vbhtml` (VB) |
| Compiled | Yes (code-behind) | Yes (compiled to C# classes) |
| Testability | Poor (tight coupling to page lifecycle) | Better (views are just templates) |
| Layout | Master pages | `_Layout.cshtml` with `@RenderBody()` |

**Why Razor won:** Cleaner syntax, better IntelliSense, no server control abstraction layer, easier unit testing. Razor compiles views to classes that inherit `RazorPage<TModel>`, making them type-safe.

---

### Q10. Describe the ASP.NET Core request lifecycle.

**Expected Answer:**

```
HTTP Request
    │
    ▼
KESTREL (cross-platform HTTP server)
    │  (or reverse-proxied from IIS/Nginx/Apache)
    ▼
HOST BUILDER
    │  WebApplication.CreateBuilder() configures:
    │    - Services (DI container)
    │    - Configuration (appsettings, env vars)
    │    - Logging
    ▼
MIDDLEWARE PIPELINE (configured in Program.cs)
    │
    ├→ app.UseExceptionHandler()     ← Catches all exceptions
    ├→ app.UseHttpsRedirection()
    ├→ app.UseCors()
    ├→ app.UseAuthentication()       ← Identity established
    ├→ app.UseAuthorization()        ← Access checked
    ├→ app.UseRouting()              ← Endpoint selected
    ├→ [Custom middleware...]
    ├→ app.UseEndpoints()            ← Endpoint executed
    │       │
    │       ▼
    │   ENDPOINT ROUTING
    │       ├→ Controller action (MVC)
    │       ├→ Razor Page handler
    │       ├→ Minimal API delegate
    │       └→ gRPC service method
    │
    ▼
HTTP Response flows back UP through middleware (reverse order)
```

**Critical difference from classic ASP.NET:**
- No `HttpModules` / `HttpHandlers` — everything is middleware.
- No `System.Web` dependency — ASP.NET Core is fully decoupled from IIS.
- **Middleware order matters** — each middleware can short-circuit or pass to the next.
- **Kestrel** is the default, cross-platform HTTP server (libuv/Socket-based).

---

### Q11. MVC vs Middleware — how do they relate conceptually?

**Expected Answer:**

| Concept | Classic ASP.NET | ASP.NET Core |
|---------|----------------|--------------|
| Cross-cutting concerns | HttpModules | Middleware |
| Request handling | HttpHandlers | Endpoint (controller, Razor Page, etc.) |
| Pipeline configuration | web.config | `Program.cs` / `Startup.cs` |
| Execution model | Event-based (pipeline events) | Delegate chain (`RequestDelegate`) |

```csharp
// Middleware is just a function:
app.Use(async (context, next) =>
{
    // Before the next middleware
    var stopwatch = Stopwatch.StartNew();

    await next();  // Call next middleware

    // After the next middleware (response flowing back)
    stopwatch.Stop();
    context.Response.Headers.Add("X-Elapsed-Ms", stopwatch.ElapsedMilliseconds.ToString());
});
```

**Key insight:** MVC is itself a middleware (`app.UseEndpoints()`). It sits at the end of the pipeline. All other middleware runs before/after it.

---

[⬆ Back to Index](#table-of-contents)

# PART 3 — Why .NET Exists (CLR, IL, CTS, CLS)

> *"Explain this like an architect" round — tests depth of understanding beyond syntax.*

---

### Q12. What problems existed before .NET that it was designed to solve?

**Expected Answer:**

| Problem | Pre-.NET | .NET Solution |
|---------|---------|--------------|
| **Language isolation** | C++ code couldn't use VB components easily | Common IL — all languages compile to same bytecode |
| **DLL Hell** | Shared DLLs overwritten, version conflicts | Assemblies with version metadata, side-by-side deployment |
| **Memory leaks** | Manual `malloc`/`free` in C/C++ | Garbage Collector |
| **Type mismatches** | VB `Integer` ≠ C++ `int` | Common Type System (CTS) |
| **Security** | OS-level security only | Code Access Security (CAS), verifiable IL |
| **Platform coupling** | COM/DCOM tied to Windows | Managed execution model (later realized with .NET Core) |

---

### Q13. What are the responsibilities of the CLR?

**Expected Answer:**

The CLR (Common Language Runtime) is the execution engine. It provides:

1. **Memory Management** — Garbage collection, heap management
2. **JIT Compilation** — IL → native code translation
3. **Type Safety** — Verifies IL before execution, prevents invalid casts
4. **Exception Handling** — Structured exception handling across languages
5. **Thread Management** — ThreadPool, synchronization primitives
6. **Security** — Code Access Security (Framework), assembly verification
7. **Assembly Loading** — Locating, loading, and resolving dependencies
8. **Interop** — P/Invoke for native calls, COM Interop

**Analogy:** The CLR is to .NET what the JVM is to Java — a managed runtime that sits between your code and the OS.

---

### Q14. What is IL / MSIL / CIL? Why is code "half-compiled"?

**Expected Answer:**

IL (Intermediate Language), MSIL (Microsoft IL), and CIL (Common IL) are different names for the same thing — the **CPU-independent instruction set** that all .NET languages compile to.

```csharp
// C# source
public int Add(int a, int b) => a + b;
```

```
// IL output (simplified)
.method public hidebysig instance int32 Add(int32 a, int32 b) cil managed
{
    .maxstack 2
    ldarg.1          // Push 'a' onto stack
    ldarg.2          // Push 'b' onto stack
    add              // Pop both, push sum
    ret              // Return top of stack
}
```

**Why "half-compiled"?**
- **First half (build time):** Source → IL. This catches syntax errors, type errors, resolves references.
- **Second half (runtime):** IL → native code via JIT. This optimizes for the actual CPU/OS.

**Benefits of two-phase compilation:**
1. **Language interoperability** — C# and F# both produce IL, so they can reference each other.
2. **Platform portability** — Same IL runs on x64 Windows, ARM Linux, macOS.
3. **Runtime optimization** — JIT knows the actual CPU features (AVX, SSE, etc.).

---

### Q15. Explain JIT compilation strategies.

**Expected Answer:**

| Strategy | When | Trade-off |
|----------|------|-----------|
| **Normal JIT** | Method compiled on first call, cached in memory | Slow startup, fast subsequent calls |
| **Econo JIT** | Method compiled on each call, not cached | Minimal memory, slower execution (mobile/embedded) |
| **Pre-JIT (NGen / ReadyToRun)** | Compiled ahead-of-time at install/publish | Fast startup, but less optimized (can't use runtime CPU info) |
| **Tiered Compilation** (.NET Core 3+) | Quick compile first (Tier 0), re-optimize hot methods (Tier 1) | Best of both: fast startup + optimized hot paths |

**Tiered Compilation (modern default):**
```
First call  → Tier 0 (quick, minimal optimization)
                │
After ~30 calls → Tier 1 (full optimization, inlining, etc.)
```

**Follow-up:** *"What is ReadyToRun (R2R)?"* — A modern replacement for NGen. Assemblies contain pre-compiled native code for a specific platform, but the JIT can still re-optimize at runtime. Enabled with `<PublishReadyToRun>true</PublishReadyToRun>`.

---

### Q16. What is the Common Type System (CTS)?

**Expected Answer:**

CTS defines how types are declared, used, and managed in the CLR. It's the **unified type system** that all .NET languages share.

```
                    System.Object
                         │
            ┌────────────┴────────────┐
       Value Types              Reference Types
            │                         │
    ┌───────┼───────┐         ┌───────┼───────┐
  Built-in  Struct  Enum    Class  Interface  Array
  (int,                     (string,  Delegate
   bool,                     object)
   float)
```

**Key rules:**
- **Every type inherits from `System.Object`** (even value types, conceptually).
- **Value types** live on the stack (usually), copied on assignment.
- **Reference types** live on the heap, assignment copies the reference.
- CTS enables **cross-language type compatibility** — a `System.Int32` is the same whether written in C#, VB.NET, or F#.

---

### Q17. What is the CLS and why does it matter?

**Expected Answer:**

The **Common Language Specification (CLS)** is a subset of the CTS — the minimum set of rules that all .NET languages must support for interoperability.

**Example:**
```csharp
// CLS-compliant — all languages can use this
public int GetCount() { ... }

// NOT CLS-compliant — C# supports unsigned int, but VB.NET didn't originally
public uint GetCount() { ... }

// Mark assembly as CLS-compliant to get compiler warnings:
[assembly: CLSCompliant(true)]
```

**CLS rules include:**
- No unsigned types in public APIs
- No pointer types in public APIs
- Method names must not differ only by case
- No operator overloading required for consumers

**Why it matters:** If your library targets multiple .NET languages, following CLS ensures any language can consume it. If it's C#-only, CLS compliance is optional.

---

[⬆ Back to Index](#table-of-contents)

# PART 4 — Assemblies, Versioning & Deployment

> *Frequently used to test real experience vs textbook knowledge.*

---

### Q18. What is an assembly really?

**Expected Answer:**

An assembly is the **fundamental unit of deployment, versioning, and security** in .NET. It's a `.dll` or `.exe` file containing:

1. **Manifest** — Identity (name, version, culture, public key token), list of files, referenced assemblies, exported types.
2. **Metadata** — Type definitions, method signatures, attributes.
3. **IL Code** — The actual intermediate language instructions.
4. **Resources** — Embedded files, strings, images.

```
MyAssembly.dll
├── Manifest
│   ├── Assembly name: MyAssembly
│   ├── Version: 2.1.0.0
│   ├── Culture: neutral
│   └── Public key token: b77a5c561934e089
├── Type Metadata
│   ├── MyNamespace.MyClass
│   └── MyNamespace.IMyInterface
├── IL Code
│   └── Method bodies...
└── Resources
    └── strings.resources
```

**Key point:** An assembly is NOT just a DLL — it's a DLL with .NET metadata. A plain C++ DLL is not an assembly.

---

### Q19. What is the difference between a strong-named and weak-named assembly?

**Expected Answer:**

| Aspect | Weak-Named | Strong-Named |
|--------|-----------|-------------|
| **Signing** | Not signed | Signed with public/private key pair |
| **GAC deployment** | Cannot be in GAC | Can be installed in GAC |
| **Version binding** | Loose (any version accepted) | Strict (exact version match by default) |
| **Tamper detection** | None | Hash verification on load |
| **Identity** | Name only | Name + Version + Culture + Public Key Token |

```bash
# Create a strong name key
sn -k MyKey.snk

# Sign an assembly
csc /keyfile:MyKey.snk MyAssembly.cs

# Check if assembly is strong-named
sn -v MyAssembly.dll
```

**Modern relevance:** Strong naming is less important in .NET Core/5+ because there's no GAC. It's mainly used for NuGet package identity and backward compatibility.

---

### Q20. Private vs Shared assemblies — what's the difference?

**Expected Answer:**

| Aspect | Private Assembly | Shared Assembly |
|--------|-----------------|----------------|
| **Location** | Application's own directory (bin/) | GAC (Global Assembly Cache) |
| **Naming** | Weak or strong name | Must be strong-named |
| **Versioning** | Application controls version | Side-by-side versioning in GAC |
| **Deployment** | XCopy with the app | `gacutil -i` or installer |
| **Sharing** | Used by one app only | Shared across multiple apps |

**Modern practice:** With .NET Core/5+, there's no GAC. All assemblies are effectively private (deployed with the app or via NuGet). This is by design — it eliminates "DLL Hell" entirely.

---

### Q21. How does the assembly resolution process work?

**Expected Answer:**

When code references a type from another assembly, the CLR must **locate and load** that assembly:

**.NET Framework resolution order:**
1. Check if already loaded in the AppDomain
2. Check the GAC (for strong-named assemblies)
3. Apply publisher policy / binding redirects (`app.config`)
4. Probe the application base directory
5. Probe private bin paths
6. Fire `AppDomain.AssemblyResolve` event (custom resolution)

**.NET Core/5+ resolution:**
1. Check already loaded assemblies
2. Look in the app's directory (`deps.json` graph)
3. Probe shared framework directories (`dotnet/shared/`)
4. Fire `AssemblyLoadContext.Resolving` event
5. Fire `AppDomain.AssemblyResolve` event

**Common interview scenario:** *"The app works on my machine but not in production."* — Usually a missing assembly or version mismatch. Check `deps.json` and use `dotnet publish` to get all dependencies.

---

### Q22. Namespace vs Assembly — what's the difference?

**Expected Answer:**

| Concept | Namespace | Assembly |
|---------|-----------|----------|
| **Nature** | Logical grouping | Physical file |
| **Purpose** | Organize types, avoid naming conflicts | Unit of deployment and versioning |
| **Crossing** | One namespace can span multiple assemblies | One assembly can contain multiple namespaces |
| **Example** | `System.Collections.Generic` | `System.Runtime.dll` |

```csharp
// Namespace = logical organization
namespace MyCompany.MyProduct.Services
{
    public class TaxService { }  // Lives in MyProduct.Domain.dll
}

// The same namespace can exist in multiple assemblies
// And one assembly can have many namespaces
```

**Common mistake:** Assuming `using System.Linq;` means you need a `System.Linq.dll`. The types in that namespace live in `System.Core.dll` (Framework) or `System.Linq.dll` (Core).

---

[⬆ Back to Index](#table-of-contents)

# PART 5 — Memory Management & Garbage Collector

> *Core senior topic. Expect deep follow-ups.*

---

### Q23. Describe the managed heap architecture.

**Expected Answer:**

```
Managed Heap
├── Small Object Heap (SOH)
│   ├── Generation 0  — New objects (short-lived, ~256 KB)
│   ├── Generation 1  — Survived one GC (buffer zone, ~2 MB)
│   └── Generation 2  — Long-lived objects (unbounded)
├── Large Object Heap (LOH)
│   └── Objects ≥ 85,000 bytes (collected with Gen2, not compacted by default)
└── Pinned Object Heap (POH) — .NET 5+
    └── Pinned objects (prevents heap fragmentation)
```

**Allocation:** Objects are allocated at the **next pointer** in Gen0. This is a simple pointer bump — extremely fast (comparable to stack allocation).

---

### Q24. How does object allocation work? What are GC roots?

**Expected Answer:**

**Allocation:**
1. CLR checks if Gen0 has enough space.
2. If yes → place object at next pointer, bump pointer. O(1) operation.
3. If no → trigger Gen0 GC to free space.

**GC Roots** — The starting points for reachability analysis:
- **Stack references** — Local variables and parameters on all thread stacks
- **Static references** — Static fields on classes
- **GC handles** — Pinned objects, strong/weak GC handles
- **Finalization queue** — Objects with finalizers pending execution
- **CPU registers** — Currently referenced objects in registers

```
GC Root (stack variable 'x')
    │
    ▼
Object A ──references──▶ Object B ──references──▶ Object C
                                                       │
Object D (no root path)  ← GARBAGE, will be collected  │
                                                       ▼
                                                  Object E
```

**Key insight:** The GC doesn't track dead objects — it traces **live** objects from roots. Everything not reachable is garbage.

---

### Q25. What triggers garbage collection?

**Expected Answer:**

1. **Gen0 threshold exceeded** — Most common trigger. Gen0 fills up (~256 KB).
2. **System memory pressure** — OS signals low physical memory.
3. **Explicit call** — `GC.Collect()` (strongly discouraged).
4. **LOH allocation** — Large objects may trigger Gen2 collection.
5. **AppDomain unload** — Collects all objects in the domain.
6. **Application shutdown** — Final collection.

**GC Modes:**

| Mode | Behavior | Best For |
|------|----------|----------|
| **Workstation GC** | Concurrent, minimizes pause time | Desktop apps, low-latency |
| **Server GC** | One heap per CPU core, parallel collection | Web servers, throughput |

```xml
<!-- Configure in .csproj -->
<PropertyGroup>
    <ServerGarbageCollection>true</ServerGarbageCollection>
    <ConcurrentGarbageCollection>true</ConcurrentGarbageCollection>
</PropertyGroup>
```

---

### Q26. Explain GC Generations: Gen0, Gen1, Gen2.

**Expected Answer:**

The generational hypothesis: **most objects die young**.

| Generation | Contains | Collection Frequency | Typical Size |
|-----------|----------|---------------------|-------------|
| **Gen0** | Newly allocated objects | Very frequent (milliseconds) | ~256 KB |
| **Gen1** | Survived 1 Gen0 collection | Moderate | ~2 MB |
| **Gen2** | Survived Gen1 collection | Rare (expensive, full GC) | Unbounded |

**How promotion works:**
```
1. Object created → Gen0
2. Gen0 GC runs:
   - Live objects → promoted to Gen1
   - Dead objects → memory reclaimed
3. Gen1 GC runs (includes Gen0):
   - Gen1 survivors → promoted to Gen2
4. Gen2 GC runs (FULL GC — includes Gen0 + Gen1 + Gen2 + LOH)
```

**Why this works:** ~90% of objects die in Gen0. By only collecting Gen0 frequently, we minimize pause times. Gen2 collections are rare but expensive.

---

### Q27. What is the Large Object Heap (LOH)?

**Expected Answer:**

Objects **≥ 85,000 bytes** (approximately 85 KB) are allocated directly on the LOH.

**Key characteristics:**
- **Collected with Gen2** — LOH collection only happens during full (Gen2) GC.
- **Not compacted by default** — Unlike SOH, objects aren't moved after collection. This leads to **fragmentation**.
- **Compaction option:** `.NET 4.5.1+` allows explicit LOH compaction:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect(); // LOH compacted on this collection only
```

**Common LOH pitfalls:**
- Large arrays (e.g., `byte[100000]`) go to LOH.
- `List<T>` internal array doubling can create LOH objects.
- Frequent LOH allocation/deallocation causes fragmentation → `OutOfMemoryException` even with available memory.

**Mitigation:** Use `ArrayPool<T>.Shared` to rent and return large arrays instead of allocating/discarding them.

---

### Q28. Why does GC exist but memory leaks still happen?

**Expected Answer:**

The GC collects objects with **no reachable references**. A "memory leak" in .NET means objects remain **reachable but unwanted**:

| Leak Type | Example | Solution |
|-----------|---------|----------|
| **Event handler leak** | `publisher.Event += handler;` never unsubscribed | Unsubscribe in Dispose or use weak events |
| **Static references** | `static List<Data> cache = new()` grows forever | Use `ConcurrentDictionary` with eviction, or `WeakReference<T>` |
| **Closure captures** | Lambda captures `this`, keeping whole object alive | Capture only needed values, not `this` |
| **Timer leak** | `System.Timers.Timer` not disposed | Always dispose timers |
| **Unmanaged resource leak** | `SqlConnection` not disposed | `using` statement or `IDisposable` pattern |
| **Long-lived collections** | Cache without expiry or size limit | Use `MemoryCache` with policies |

```csharp
// LEAK: Event handler keeps 'subscriber' alive as long as 'publisher' lives
publisher.DataChanged += subscriber.OnDataChanged;

// FIX: Unsubscribe when done
publisher.DataChanged -= subscriber.OnDataChanged;
```

---

### Q29. Explain Finalize vs Dispose.

**Expected Answer:**

| Aspect | Finalize (~Destructor) | Dispose (IDisposable) |
|--------|----------------------|----------------------|
| **Called by** | GC (non-deterministic) | Developer (deterministic) |
| **When** | Before GC reclaims object | Whenever developer calls it |
| **Purpose** | Safety net for unmanaged resources | Prompt cleanup of all resources |
| **Performance** | Expensive (objects survive extra GC) | Cheap (immediate cleanup) |
| **Guarantee** | Not guaranteed to run (app crash, `Environment.FailFast`) | Only if developer calls it |

**The correct IDisposable pattern:**

```csharp
public class ResourceHolder : IDisposable
{
    private IntPtr _unmanagedHandle;
    private ManagedResource _managed;
    private bool _disposed;

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this); // Tell GC: no need to finalize
    }

    protected virtual void Dispose(bool disposing)
    {
        if (_disposed) return;

        if (disposing)
        {
            // Free managed resources
            _managed?.Dispose();
        }

        // Free unmanaged resources
        CloseHandle(_unmanagedHandle);
        _disposed = true;
    }

    ~ResourceHolder() // Destructor = Finalizer
    {
        Dispose(false); // Only clean unmanaged resources
    }
}
```

**Why `GC.SuppressFinalize(this)`?** Objects with finalizers survive an extra GC cycle (promoted to Gen1 at minimum). Suppressing finalization after Dispose lets the GC collect the object normally.

---

### Q30. Why is GC.Collect() dangerous?

**Expected Answer:**

1. **Forces a full (Gen2) collection** — Expensive, pauses all threads (even with concurrent GC, there's a brief Stop-the-World phase).
2. **Promotes objects unnecessarily** — Surviving Gen0 objects get promoted to Gen1, surviving Gen1 to Gen2. These promoted objects are collected less frequently.
3. **Disrupts GC heuristics** — The GC self-tunes based on allocation patterns. Manual collection throws off its predictions.
4. **Almost never helps** — If you have a memory issue, `GC.Collect()` won't fix the root cause (reachable unwanted references).

**When it's acceptable (rare):**
- After loading a large dataset that created many temporary objects
- During a natural application pause (loading screen, level transition in games)
- In benchmarks to normalize starting conditions

```csharp
// Almost always wrong
GC.Collect();
GC.WaitForPendingFinalizers();

// The rare acceptable case
void OnLevelComplete()
{
    UnloadLevelResources(); // Remove references first
    GC.Collect(2, GCCollectionMode.Optimized); // Hint, not force
}
```

---

[⬆ Back to Index](#table-of-contents)

# PART 6 — C# Type System & Object Lifetime

---

### Q31. What is the real difference between value types and reference types?

**Expected Answer:**

| Aspect | Value Type | Reference Type |
|--------|-----------|----------------|
| **Storage** | Contains the actual data | Contains a reference (pointer) to data |
| **Assignment** | Copies the data | Copies the reference (same object) |
| **Default** | `default(T)` (zeros) | `null` |
| **Inheritance** | Cannot be inherited (sealed implicitly) | Can be inherited |
| **Allocation** | Usually stack (local), or inline in containing object | Always heap |
| **Examples** | `int`, `bool`, `DateTime`, `struct`, `enum` | `string`, `object`, `class`, `array`, `delegate` |

```csharp
// Value type — independent copies
int a = 5;
int b = a;     // b is a copy
b = 10;        // a is still 5

// Reference type — shared object
var list1 = new List<int> { 1, 2, 3 };
var list2 = list1;    // Same list!
list2.Add(4);         // list1 also has 4 now
```

**Common misconception:** "Value types are always on the stack." **Wrong.** Value types live where their container lives:
- Local variable → stack
- Field in a class → heap (part of the class instance)
- Element in an array → heap (arrays are reference types)
- Boxed → heap

---

### Q32. What is boxing and unboxing? Why should you care?

**Expected Answer:**

**Boxing:** Wrapping a value type in an `object` (heap allocation + copy).
**Unboxing:** Extracting the value type from the `object` (type check + copy).

```csharp
int num = 42;
object boxed = num;        // Boxing: allocates on heap, copies value
int unboxed = (int)boxed;  // Unboxing: type check + copy back to stack
```

**Performance impact:**
- Each boxing allocates a new heap object (~12 bytes overhead + value size).
- In tight loops, this causes GC pressure.

```csharp
// BAD — boxing on every iteration
ArrayList list = new ArrayList();
for (int i = 0; i < 1_000_000; i++)
    list.Add(i);  // Boxing int → object each time

// GOOD — no boxing
List<int> list = new List<int>();
for (int i = 0; i < 1_000_000; i++)
    list.Add(i);  // No boxing — generic collection
```

**Hidden boxing traps:**
- `string.Format("{0}", myInt)` — boxes the int.
- `myStruct.Equals(other)` — if not overridden, boxes for `object.Equals`.
- Casting a struct to an interface it implements → boxing.

---

### Q33. What is the difference between `var`, `object`, and `dynamic`?

**Expected Answer:**

| Feature | `var` | `object` | `dynamic` |
|---------|-------|----------|-----------|
| **Type resolution** | Compile-time (inferred) | Compile-time (explicit) | Runtime |
| **Type safety** | Full (compiler knows actual type) | Partial (must cast) | None (runtime errors) |
| **IntelliSense** | Yes (full) | No (only `object` members) | No |
| **Performance** | Same as explicit type | Boxing for value types | DLR overhead, reflection |

```csharp
var x = 42;         // Compiler knows x is int — full IntelliSense, type-safe
object y = 42;      // y is object at compile-time — must cast: (int)y
dynamic z = 42;     // No compile-time checking — errors happen at runtime

x.GetHashCode();    // Calls int.GetHashCode() directly
y.GetHashCode();    // Calls object.GetHashCode() (virtual dispatch)
z.Foo();            // Compiles fine! Crashes at runtime: RuntimeBinderException
```

**Key insight:** `var` is NOT dynamic typing. It's **implicit static typing** — the compiler infers the type and treats it exactly as if you wrote it explicitly.

---

### Q34. What are nullable value types?

**Expected Answer:**

`Nullable<T>` (shorthand: `T?`) allows value types to represent `null`:

```csharp
int? age = null;       // Nullable<int> — can be null
int normalAge = 25;    // Cannot be null

// Under the hood
Nullable<int> age = new Nullable<int>();  // HasValue = false

// Checking
if (age.HasValue)
    Console.WriteLine(age.Value);

// Null-coalescing
int result = age ?? 0;          // 0 if null
int result2 = age.GetValueOrDefault(); // 0 if null

// Null-conditional
int? length = name?.Length;  // null if name is null
```

**Nullable reference types (C# 8+):**
```csharp
#nullable enable
string name = null;    // Warning: assigning null to non-nullable
string? name2 = null;  // OK — explicitly nullable
```

This is a compile-time analysis feature — no runtime overhead. It helps catch `NullReferenceException` at compile time.

---

[⬆ Back to Index](#table-of-contents)

# PART 7 — Strings, Immutability & Performance

---

### Q35. Why is `string` immutable in C#?

**Expected Answer:**

Once a `string` is created, its content **cannot be changed**. Any "modification" creates a **new string**.

**Reasons for immutability:**
1. **Thread safety** — Strings can be shared across threads without synchronization.
2. **Hash code caching** — Used as dictionary keys and intern pool entries.
3. **Security** — Connection strings, file paths can't be modified after validation.
4. **String interning** — Compiler can reuse identical string literals.

```csharp
string s = "Hello";
s += " World";  // Creates a NEW string "Hello World", "Hello" is now garbage

// What actually happens:
// 1. Allocate new string of length 11
// 2. Copy "Hello" (5 chars)
// 3. Copy " World" (6 chars)
// 4. s now points to new string
// 5. Original "Hello" is eligible for GC
```

---

### Q36. What is the string literal pool (interning)?

**Expected Answer:**

The CLR maintains an **intern pool** — a hash table of unique string literals. Identical string literals share the same reference.

```csharp
string a = "Hello";
string b = "Hello";
Console.WriteLine(object.ReferenceEquals(a, b));  // True — same interned instance

string c = new string("Hello".ToCharArray());
Console.WriteLine(object.ReferenceEquals(a, c));  // False — not interned

string d = string.Intern(c);
Console.WriteLine(object.ReferenceEquals(a, d));  // True — manually interned
```

**Rules:**
- All string **literals** in source code are automatically interned.
- Runtime-created strings (concatenation, user input) are NOT interned by default.
- `string.Intern()` explicitly adds a string to the pool.
- **Warning:** Interned strings are never garbage collected (they live for the app's lifetime).

---

### Q37. `string` vs `String` — is there a difference?

**Expected Answer:**

**No difference at all.** `string` is a C# keyword alias for `System.String`.

```csharp
string name = "Alice";        // C# keyword alias
String name = "Alice";        // System.String (needs using System;)
System.String name = "Alice"; // Fully qualified

// All three compile to identical IL
```

**Convention:** Use `string` (lowercase) for variable declarations and `String` for static method calls:
```csharp
string name = String.IsNullOrEmpty(input) ? "default" : input;
```
However, many teams (and modern C# style) use `string` everywhere — both are acceptable.

---

### Q38. When should you use StringBuilder vs string concatenation?

**Expected Answer:**

| Scenario | Use | Why |
|----------|-----|-----|
| 2-3 concatenations | `string` or `$""` | Compiler optimizes, no overhead |
| Loop concatenation | `StringBuilder` | Avoids N string allocations |
| Known parts at compile time | String interpolation `$""` | Compiler optimizes |
| Building large text (HTML, SQL) | `StringBuilder` | Mutable buffer, amortized allocation |

```csharp
// BAD — O(n²) allocations in a loop
string result = "";
for (int i = 0; i < 10000; i++)
    result += i.ToString();  // Creates 10,000 intermediate strings!

// GOOD — O(n) with StringBuilder
var sb = new StringBuilder();
for (int i = 0; i < 10000; i++)
    sb.Append(i);
string result = sb.ToString();  // One final string allocation
```

**How StringBuilder works internally:**
- Maintains a `char[]` buffer (default 16 chars).
- Doubles capacity when full (amortized O(1) append).
- `ToString()` creates one final string from the buffer.

**Modern alternative (.NET 6+):**
```csharp
// String.Create for performance-critical scenarios
string result = string.Create(11, (object?)null, (span, _) =>
{
    "Hello".AsSpan().CopyTo(span);
    " World".AsSpan().CopyTo(span[5..]);
});
```

---

[⬆ Back to Index](#table-of-contents)

# PART 8 — Object-Oriented Design

> *Real decisions, not definitions. Interviewers want to see you think about trade-offs.*

---

### Q39. What does encapsulation really mean?

**Expected Answer:**

Encapsulation is **hiding internal state and requiring interaction through a public interface**. It's not just "making fields private" — it's about protecting **invariants**.

```csharp
// BAD — no encapsulation, anyone can break invariants
public class BankAccount
{
    public decimal Balance;  // Anyone can set this to -1000
}

// GOOD — encapsulated, invariants protected
public class BankAccount
{
    private decimal _balance;

    public decimal Balance => _balance;

    public void Deposit(decimal amount)
    {
        if (amount <= 0) throw new ArgumentException("Amount must be positive");
        _balance += amount;
    }

    public void Withdraw(decimal amount)
    {
        if (amount > _balance) throw new InvalidOperationException("Insufficient funds");
        _balance -= amount;
    }
}
```

**Key insight:** Encapsulation enables **change without breaking consumers**. You can add logging, validation, or change the internal representation without modifying any caller code.

---

### Q40. When does inheritance break systems?

**Expected Answer:**

Inheritance creates **tight coupling** between base and derived classes. Changes to the base class ripple through all descendants.

**Fragile base class problem:**
```csharp
public class Collection
{
    public int Count { get; private set; }

    public virtual void Add(object item) { Count++; /* ... */ }

    public virtual void AddRange(IEnumerable items)
    {
        foreach (var item in items)
            Add(item);  // Calls virtual Add
    }
}

public class CountingCollection : Collection
{
    public int AddCount { get; private set; }

    public override void Add(object item)
    {
        AddCount++;
        base.Add(item);
    }
}

// BUG: AddRange calls Add (virtual) → CountingCollection.Add called for each item
// AddCount is incremented TWICE for each item in AddRange!
var c = new CountingCollection();
c.AddRange(new[] { 1, 2, 3 });  // AddCount = 3, but user might expect 1 (one AddRange call)
```

**When to prefer composition over inheritance:**
- When you want to reuse behavior, not represent an "is-a" relationship
- When the base class isn't designed for inheritance (not `virtual`/`abstract`)
- When you need to change the reused behavior at runtime

```csharp
// Composition — more flexible
public class CountingCollection
{
    private readonly List<object> _inner = new();
    public int AddCount { get; private set; }

    public void Add(object item) { AddCount++; _inner.Add(item); }
    public void AddRange(IEnumerable items) { AddCount++; _inner.AddRange(items); }
}
```

---

### Q41. Explain polymorphism — runtime vs compile-time.

**Expected Answer:**

| Type | Mechanism | Resolution |
|------|-----------|-----------|
| **Compile-time (static)** | Method overloading, operator overloading | Compiler picks the method based on parameter types |
| **Runtime (dynamic)** | Method overriding (virtual/override) | CLR picks the method based on actual object type |

```csharp
// COMPILE-TIME polymorphism — overloading
public class Calculator
{
    public int Add(int a, int b) => a + b;       // Chosen at compile time
    public double Add(double a, double b) => a + b; // based on argument types
}

// RUNTIME polymorphism — overriding
public class Shape
{
    public virtual double Area() => 0;
}

public class Circle : Shape
{
    public double Radius { get; set; }
    public override double Area() => Math.PI * Radius * Radius;
}

Shape shape = new Circle { Radius = 5 };
shape.Area();  // Calls Circle.Area() at runtime via vtable lookup
```

---

### Q42. What is the difference between `virtual`, `override`, and `new`?

**Expected Answer:**

```csharp
public class Base
{
    public virtual void Greet() => Console.WriteLine("Base");
    public void Info() => Console.WriteLine("Base Info");
}

public class Derived : Base
{
    public override void Greet() => Console.WriteLine("Derived");  // Overrides
    public new void Info() => Console.WriteLine("Derived Info");   // Hides
}

Base obj = new Derived();
obj.Greet();  // "Derived" — virtual dispatch, actual type wins
obj.Info();   // "Base Info" — non-virtual, declared type wins (hiding!)

Derived obj2 = new Derived();
obj2.Info();  // "Derived Info" — called on Derived reference
```

| Keyword | What it does | Behavior with base reference |
|---------|-------------|----------------------------|
| `virtual` | Declares method can be overridden | Enables runtime dispatch |
| `override` | Replaces virtual method in derived class | Derived version called |
| `new` | **Hides** base method (no virtual dispatch) | Base version called! |

**Interview red flag:** If a candidate uses `new` intentionally, ask **why**. It's almost always a design smell — it breaks polymorphism.

---

[⬆ Back to Index](#table-of-contents)

# PART 9 — Constructors, Lifetime & Control

---

### Q43. What are the rules around constructor behavior?

**Expected Answer:**

**Default constructor:**
- If no constructors are defined, C# provides a parameterless constructor.
- If ANY constructor is defined, the default is NOT generated.

**Constructor chaining:**
```csharp
public class Person
{
    public string Name { get; }
    public int Age { get; }

    public Person() : this("Unknown", 0) { }  // Chains to parameterized

    public Person(string name) : this(name, 0) { }  // Chains to full

    public Person(string name, int age)  // Primary constructor
    {
        Name = name;
        Age = age;
    }
}
```

**Base constructor call:**
```csharp
public class Employee : Person
{
    public string Department { get; }

    public Employee(string name, string dept)
        : base(name)  // Must call base constructor
    {
        Department = dept;
    }
}
```

**Execution order:**
1. Base class field initializers
2. Base class constructor body
3. Derived class field initializers
4. Derived class constructor body

---

### Q44. Why do private constructors exist?

**Expected Answer:**

Private constructors prevent external instantiation. Common uses:

**1. Singleton pattern:**
```csharp
public class Singleton
{
    private static readonly Singleton _instance = new();
    private Singleton() { }  // Can't create from outside
    public static Singleton Instance => _instance;
}
```

**2. Static-only classes (pre-C# 2.0):**
```csharp
public class MathHelper
{
    private MathHelper() { }  // No instantiation
    public static double Square(double x) => x * x;
}
// Modern: use 'static class MathHelper' instead
```

**3. Factory pattern (control creation):**
```csharp
public class Connection
{
    private Connection(string connStr) { /* ... */ }

    public static Connection Create(string connStr)
    {
        // Validation, pooling, logging before creation
        if (string.IsNullOrEmpty(connStr)) throw new ArgumentException("...");
        return new Connection(connStr);
    }
}
```

---

### Q45. Explain static constructors and their guarantees.

**Expected Answer:**

```csharp
public class Config
{
    public static readonly string Setting;

    static Config()  // Static constructor
    {
        Setting = LoadFromFile();  // Runs once, before first use
    }
}
```

**Guarantees:**
1. Called **automatically**, at most **once** per AppDomain/type.
2. Called **before** any static member access or instance creation.
3. **Thread-safe** — CLR guarantees only one thread executes it.
4. Cannot be called directly — no access modifiers, no parameters.
5. If it throws, the type is **unusable** for the rest of the AppDomain (`TypeInitializationException`).

**Pitfall:** Static constructor exceptions are wrapped in `TypeInitializationException`. The original exception is in `.InnerException`. This makes debugging harder.

```csharp
try
{
    var x = Config.Setting;  // Static ctor runs here
}
catch (TypeInitializationException ex)
{
    // ex.InnerException has the actual error
    Console.WriteLine(ex.InnerException?.Message);
}
```

---

### Q46. Do destructors (finalizers) make sense in C#?

**Expected Answer:**

Destructors compile to `Finalize()` methods. They should be **rare** — only for types that directly hold unmanaged resources.

```csharp
public class NativeWrapper
{
    private IntPtr _handle;

    ~NativeWrapper()  // Destructor = Finalize
    {
        // Release unmanaged resource
        NativeMethods.CloseHandle(_handle);
    }
}
```

**Why they're problematic:**
1. **Non-deterministic** — You don't know when (or if) the finalizer runs.
2. **Performance cost** — Finalizable objects survive at least one extra GC generation.
3. **Ordering not guaranteed** — Can't rely on other managed objects being alive in finalizer.
4. **Separate thread** — Runs on the finalizer thread, can cause threading issues.

**Best practice:** Implement `IDisposable`, use `using`, and add a finalizer only as a safety net for unmanaged resources (see Q29 for the full pattern).

---

[⬆ Back to Index](#table-of-contents)

# PART 10 — Abstract Classes vs Interfaces

> *Interview classic. The key is going beyond "abstract can have implementation, interface can't" (especially since C# 8 default interface methods changed that).*

---

### Q47. When should you choose an abstract class over an interface?

**Expected Answer:**

**Decision matrix:**

| Need | Abstract Class | Interface |
|------|---------------|-----------|
| Shared implementation code | Yes | Limited (C# 8+ default methods) |
| State (fields) | Yes | No (only properties without backing fields) |
| Constructor logic | Yes | No |
| Multiple inheritance | No (single base class) | Yes (multiple interfaces) |
| Version new members | Add non-abstract methods | Breaking change (pre-C# 8) |
| Access modifiers on members | All (private, protected, etc.) | Public only (pre-C# 8) |

**Choose abstract class when:**
- You need shared state or constructor initialization
- Derived classes share significant implementation
- You want `protected` members for the inheritance hierarchy
- You're modeling an "is-a" relationship with shared behavior

**Choose interface when:**
- Multiple unrelated types share behavior ("can-do")
- You need multiple inheritance
- You want to define a contract without any implementation
- The implementors are in different class hierarchies

```csharp
// Abstract class — shared behavior + state
public abstract class TaxCalculator
{
    protected readonly decimal TaxRate;

    protected TaxCalculator(decimal taxRate) => TaxRate = taxRate;

    public decimal Calculate(decimal income)
    {
        var deductions = GetDeductions(income);  // Shared algorithm
        return (income - deductions) * TaxRate;
    }

    protected abstract decimal GetDeductions(decimal income);  // Varies
}

// Interface — contract only
public interface IAuditable
{
    DateTime CreatedAt { get; }
    DateTime? ModifiedAt { get; }
    string ModifiedBy { get; }
}
```

---

### Q48. What is explicit interface implementation and when is it useful?

**Expected Answer:**

```csharp
public interface ILogger { void Log(string message); }
public interface IAuditor { void Log(string message); }

public class Service : ILogger, IAuditor
{
    // Explicit implementation — only accessible through the interface
    void ILogger.Log(string message) => Console.WriteLine($"LOG: {message}");
    void IAuditor.Log(string message) => Console.WriteLine($"AUDIT: {message}");
}

var service = new Service();
// service.Log("test");  // Won't compile — no public Log method

((ILogger)service).Log("test");   // "LOG: test"
((IAuditor)service).Log("test");  // "AUDIT: test"
```

**Use cases:**
1. **Resolving conflicts** — When two interfaces have the same method signature.
2. **Hiding implementation details** — Interface members aren't part of the class's public API.
3. **Implementing IDisposable privately** — `void IDisposable.Dispose()` keeps Dispose off the public API while still supporting `using`.

---

### Q49. How did C# 8 default interface methods change the equation?

**Expected Answer:**

C# 8 allows interfaces to have default method implementations:

```csharp
public interface ILogger
{
    void Log(string message);

    // Default implementation — new in C# 8
    void LogError(string message) => Log($"ERROR: {message}");
    void LogWarning(string message) => Log($"WARN: {message}");
}

// Implementors only need to implement Log():
public class ConsoleLogger : ILogger
{
    public void Log(string message) => Console.WriteLine(message);
    // LogError and LogWarning use the default implementation
}
```

**Important caveats:**
- Default interface methods are **only accessible through the interface reference**, not the implementing class reference (similar to explicit implementation).
- They don't have access to class state — no `this` for instance fields.
- They're a **versioning tool** — add new methods to interfaces without breaking existing implementors.

**This does NOT make interfaces equal to abstract classes.** Interfaces still can't have: fields, constructors, destructors, or instance state.

---

[⬆ Back to Index](#table-of-contents)

# PART 11 — Structs, Enums & Special Types

---

### Q50. Structs vs classes — when should you use structs?

**Expected Answer:**

**Use structs when ALL of these are true:**
1. Represents a single value (like `int`, `DateTime`, `Point`)
2. Instance size is small (< 16 bytes ideally)
3. Immutable (or at least designed as such)
4. Won't be boxed frequently
5. Short-lived (allocated and discarded quickly)

```csharp
// GOOD struct — small, immutable, value-like
public readonly struct Point
{
    public double X { get; }
    public double Y { get; }

    public Point(double x, double y) => (X, Y) = (x, y);

    public double DistanceTo(Point other) =>
        Math.Sqrt(Math.Pow(X - other.X, 2) + Math.Pow(Y - other.Y, 2));
}

// BAD struct — too large, mutable, will cause copying issues
public struct BigMutableData
{
    public byte[] Buffer;      // Reference type field
    public int Count;
    public void Increment() => Count++;  // Modifying struct — danger!
}
```

**Performance characteristics:**
- **Allocation:** Stack (local) or inline (field in class) — no GC pressure.
- **Copying:** Entire value copied on assignment/parameter passing.
- **Cache-friendly:** Contiguous in arrays (`Point[]` is a single memory block, not pointers to heap).

---

### Q51. What are the dangers of mutable structs?

**Expected Answer:**

```csharp
public struct MutablePoint
{
    public int X;
    public int Y;
    public void MoveRight() => X++;
}

// Trap 1: Modifying a copy, not the original
List<MutablePoint> points = new() { new MutablePoint { X = 1, Y = 1 } };
// points[0].MoveRight();  // Won't compile — indexer returns a copy

// Trap 2: foreach copies
MutablePoint[] arr = { new MutablePoint { X = 1 } };
foreach (var p in arr)
{
    p.MoveRight();  // Modifies the LOCAL COPY, not the array element!
    // p.X is 2 here, but arr[0].X is still 1
}

// Trap 3: Property returns a copy
public class Container
{
    public MutablePoint Position { get; set; }
}
var c = new Container { Position = new MutablePoint { X = 1 } };
c.Position.MoveRight();  // Modifies a COPY — Position.X is still 1!
```

**Rule of thumb:** Make structs `readonly` to prevent these bugs:
```csharp
public readonly struct ImmutablePoint
{
    public int X { get; }
    public int Y { get; }
    public ImmutablePoint(int x, int y) => (X, Y) = (x, y);
    public ImmutablePoint MoveRight() => new(X + 1, Y);  // Return new instance
}
```

---

### Q52. Enums — design considerations and pitfalls.

**Expected Answer:**

```csharp
// Basic enum (underlying type: int by default)
public enum OrderStatus
{
    Pending = 0,
    Processing = 1,
    Shipped = 2,
    Delivered = 3,
    Cancelled = 4
}

// Flags enum — combinable values
[Flags]
public enum Permissions
{
    None = 0,
    Read = 1,       // 0001
    Write = 2,      // 0010
    Execute = 4,    // 0100
    All = Read | Write | Execute  // 0111
}

Permissions p = Permissions.Read | Permissions.Write;
bool canWrite = p.HasFlag(Permissions.Write);  // true
```

**Pitfalls:**

1. **Invalid values:** Enums are just integers — `(OrderStatus)999` compiles fine!
```csharp
OrderStatus status = (OrderStatus)999;  // No error! IsValid check needed.
if (!Enum.IsDefined(typeof(OrderStatus), status))
    throw new ArgumentOutOfRangeException();
```

2. **Adding values breaks switch:** New enum values won't hit existing switch cases — always add a `default` case.

3. **Serialization fragility:** Storing enum as int in a database — adding a value in the middle shifts all subsequent values. **Always assign explicit values.**

4. **Underlying type matters:**
```csharp
public enum TinyStatus : byte { Active = 1, Inactive = 2 }  // 1 byte instead of 4
```

---

[⬆ Back to Index](#table-of-contents)

# PART 12 — Delegates, Events & Functional Constructs

---

### Q53. What is a delegate really?

**Expected Answer:**

A delegate is a **type-safe function pointer** — an object that holds a reference to a method (or multiple methods) with a specific signature.

```csharp
// Delegate declaration — defines the signature
public delegate int MathOperation(int a, int b);

// Usage
MathOperation add = (a, b) => a + b;
MathOperation multiply = (a, b) => a * b;

int result = add(3, 4);       // 7
int result2 = multiply(3, 4); // 12

// Built-in generic delegates (prefer these):
Func<int, int, int> add2 = (a, b) => a + b;      // Returns value
Action<string> print = msg => Console.WriteLine(msg); // No return
Predicate<int> isEven = n => n % 2 == 0;           // Returns bool
```

**Under the hood:** A delegate is a class inheriting from `System.MulticastDelegate`:
- `Target` — the object the method belongs to (null for static methods)
- `Method` — `MethodInfo` reference
- `Invoke()` — calls the method
- `BeginInvoke()` / `EndInvoke()` — async pattern (legacy)

---

### Q54. Single-cast vs multicast delegates.

**Expected Answer:**

All delegates in C# are technically **multicast** — they can hold a chain (invocation list) of methods.

```csharp
Action<string> logger = Console.WriteLine;
logger += msg => File.AppendAllText("log.txt", msg + "\n");
logger += msg => Debug.WriteLine(msg);

logger("Hello");  // Calls ALL three methods in order

// Remove a handler
logger -= Console.WriteLine;
```

**Important behaviors:**
- **Return value:** For non-void delegates, only the **last** delegate's return value is returned.
- **Exceptions:** If any delegate in the chain throws, subsequent delegates **don't execute**.
- **Thread safety:** `+=` and `-=` are not atomic — use `Interlocked` or lock for thread safety.

```csharp
// Getting all return values from a multicast delegate
Func<int> getNumbers = () => 1;
getNumbers += () => 2;
getNumbers += () => 3;

foreach (Func<int> d in getNumbers.GetInvocationList())
    Console.WriteLine(d());  // 1, 2, 3 — call each individually
```

---

### Q55. How do events differ from delegates?

**Expected Answer:**

An event is a **restricted delegate** — it adds encapsulation by limiting who can invoke it.

```csharp
public class Button
{
    // Event — only Button class can invoke (raise) it
    public event EventHandler<ClickEventArgs> Clicked;

    // vs raw delegate — anyone can invoke it
    public EventHandler<ClickEventArgs> ClickedDelegate;

    protected virtual void OnClicked(ClickEventArgs e)
    {
        Clicked?.Invoke(this, e);  // Only the owning class can invoke
    }
}

// Consumers can only += and -=
button.Clicked += (sender, e) => HandleClick(e);
// button.Clicked(this, args);  // Won't compile — can't invoke from outside!
// button.Clicked = null;       // Won't compile — can't reassign from outside!

// But raw delegates allow this:
button.ClickedDelegate(this, args);  // Anyone can invoke!
button.ClickedDelegate = null;       // Anyone can wipe all subscribers!
```

**Event memory leak pattern:**
```csharp
// LEAK: subscriber stays alive as long as publisher lives
publisher.SomeEvent += subscriber.HandleEvent;

// The delegate holds a reference to 'subscriber'
// Even if nothing else references subscriber, it can't be GC'd
// until publisher is GC'd or the event is unsubscribed

// FIX: Always unsubscribe when done
public void Dispose()
{
    publisher.SomeEvent -= HandleEvent;
}
```

---

### Q56. Anonymous methods vs lambda expressions.

**Expected Answer:**

```csharp
// Anonymous method (C# 2.0)
Func<int, int> doubleIt = delegate(int x) { return x * 2; };

// Lambda expression (C# 3.0) — shorthand
Func<int, int> doubleIt2 = (int x) => { return x * 2; };

// Expression lambda — even shorter (single expression)
Func<int, int> doubleIt3 = x => x * 2;

// All three compile to the same thing
```

**Key differences:**
- Lambdas can be converted to **expression trees** (`Expression<Func<T>>`), anonymous methods cannot.
- Lambdas are the standard in modern C#.

**Closure capture:**
```csharp
int factor = 10;
Func<int, int> multiply = x => x * factor;  // Captures 'factor'
factor = 20;
Console.WriteLine(multiply(5));  // 100, not 50! Captures the VARIABLE, not the value.
```

---

[⬆ Back to Index](#table-of-contents)

# PART 13 — LINQ, Iteration & Deferred Execution

---

### Q57. What is deferred execution and why does it matter?

**Expected Answer:**

LINQ queries are **not executed when defined** — they execute when you **iterate** the results.

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5 };

// Define query — NO execution happens here
var query = numbers.Where(n => n > 2).Select(n => n * 10);

// Modify the source AFTER defining the query
numbers.Add(6);

// Execute NOW — when we iterate
foreach (var n in query)
    Console.Write($"{n} ");  // 30 40 50 60 — includes 6!
```

**Deferred vs Immediate execution:**

| Deferred (lazy) | Immediate (eager) |
|-----------------|-------------------|
| `Where`, `Select`, `OrderBy`, `Take`, `Skip`, `SelectMany` | `ToList()`, `ToArray()`, `ToDictionary()`, `Count()`, `First()`, `Sum()`, `Any()` |

**Why it matters:**
1. **Performance** — Only processes elements as needed. `numbers.Where(n > 2).First()` stops after finding one match.
2. **Composition** — Build queries incrementally, execute once.
3. **Gotcha** — Query captures the variable, not the data. Source changes affect results.

```csharp
// Multiple enumeration trap
var query = GetExpensiveData().Where(x => x.IsActive);
var count = query.Count();    // Enumerates once
var list = query.ToList();     // Enumerates AGAIN!

// Fix: materialize once
var data = GetExpensiveData().Where(x => x.IsActive).ToList();
var count = data.Count;        // No re-enumeration
```

---

### Q58. IEnumerable vs IEnumerator — what's the difference?

**Expected Answer:**

```csharp
// IEnumerable — "I can be iterated"
public interface IEnumerable<T>
{
    IEnumerator<T> GetEnumerator();
}

// IEnumerator — "I am iterating"
public interface IEnumerator<T>
{
    T Current { get; }
    bool MoveNext();
    void Reset();
}
```

**Analogy:** `IEnumerable` is a book. `IEnumerator` is a bookmark — it tracks your position.

```csharp
// foreach is syntactic sugar for:
IEnumerator<int> enumerator = list.GetEnumerator();
try
{
    while (enumerator.MoveNext())
    {
        int item = enumerator.Current;
        // ... use item
    }
}
finally
{
    enumerator.Dispose();
}
```

**Multiple enumerators:** Each `foreach` (or `GetEnumerator()` call) gets an **independent** enumerator. Two `foreach` loops iterate independently.

---

### Q59. How does `yield return` work?

**Expected Answer:**

`yield return` creates a **state machine** — the compiler generates an `IEnumerator<T>` implementation that pauses and resumes execution.

```csharp
public IEnumerable<int> Fibonacci()
{
    int a = 0, b = 1;
    while (true)  // Infinite sequence — safe because of deferred execution!
    {
        yield return a;     // Pause here, return 'a'
        (a, b) = (b, a + b); // Resume here on next MoveNext()
    }
}

// Usage — only generates what we consume
var first10 = Fibonacci().Take(10).ToList();
// [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

**What the compiler generates (conceptually):**
```csharp
// The compiler transforms this into a state machine class
// with fields for local variables and a switch-based MoveNext():
private class FibonacciIterator : IEnumerator<int>
{
    private int _state;
    private int _a, _b;

    public int Current { get; private set; }

    public bool MoveNext()
    {
        switch (_state)
        {
            case 0: _a = 0; _b = 1; goto case 1;
            case 1: Current = _a; _state = 2; return true;
            case 2: (_a, _b) = (_b, _a + _b); goto case 1;
        }
        return false;
    }
}
```

**`yield break`** — exits the iterator:
```csharp
IEnumerable<int> TakeWhilePositive(IEnumerable<int> source)
{
    foreach (var item in source)
    {
        if (item < 0) yield break;  // Stop iteration
        yield return item;
    }
}
```

---

### Q60. Common LINQ performance traps.

**Expected Answer:**

**1. Multiple enumeration of database queries:**
```csharp
// BAD — executes the query TWICE
var users = dbContext.Users.Where(u => u.IsActive);
var count = users.Count();     // SQL query #1
var list = users.ToList();     // SQL query #2

// GOOD — execute once
var list = dbContext.Users.Where(u => u.IsActive).ToList();
var count = list.Count;
```

**2. N+1 query in Select:**
```csharp
// BAD — N+1 queries
var orders = db.Fetch<Order>("SELECT * FROM Orders");
var viewModels = orders.Select(o => new {
    Order = o,
    Customer = db.Single<Customer>("WHERE Id = @0", o.CustomerId)  // Query per order!
});

// GOOD — batch with JOIN or WHERE IN
```

**3. Unnecessary materialization:**
```csharp
// BAD — materializes full list just to check existence
if (items.ToList().Count > 0) { ... }

// GOOD — short-circuits at first match
if (items.Any()) { ... }
```

**4. Closure allocation in hot paths:**
```csharp
// Each lambda creates a closure object
var filtered = items.Where(x => x.Value > threshold);  // Allocates closure

// In hot paths, consider a for loop:
for (int i = 0; i < items.Count; i++)
    if (items[i].Value > threshold) results.Add(items[i]);
```

**5. OrderBy then Where (wrong order):**
```csharp
// BAD — sorts everything, then filters
items.OrderBy(x => x.Name).Where(x => x.IsActive);

// GOOD — filter first, sort the smaller set
items.Where(x => x.IsActive).OrderBy(x => x.Name);
```

---

[⬆ Back to Index](#table-of-contents)

# PART 14 — Threading, Async & Concurrency

> *Old + New Unified. This is the most important section for senior-level interviews.*

---

### Q61. Process vs Thread — what's the real difference?

**Expected Answer:**

| Aspect | Process | Thread |
|--------|---------|--------|
| **Memory** | Own address space | Shares process memory |
| **Communication** | IPC (pipes, sockets, shared memory) | Direct (same heap) |
| **Creation cost** | Heavy (OS allocates memory, handles) | Light (just a stack + context) |
| **Crash isolation** | One process crash doesn't affect others | One thread crash can crash the process |
| **Context switch** | Expensive (memory mapping changes) | Cheaper (same address space) |

**Modern .NET context:** ASP.NET Core uses a single process with the ThreadPool handling requests on multiple threads. You rarely create `new Thread()` — use `Task` and `async/await` instead.

---

### Q62. Explain thread safety fundamentals.

**Expected Answer:**

A method/class is **thread-safe** if it behaves correctly when accessed from multiple threads simultaneously.

**Race condition example:**
```csharp
// NOT thread-safe
private int _count;
public void Increment()
{
    _count++;  // Read → Modify → Write (three operations, not atomic)
}

// Thread 1: reads _count = 5
// Thread 2: reads _count = 5
// Thread 1: writes _count = 6
// Thread 2: writes _count = 6  ← Lost update! Should be 7
```

**Solutions:**

```csharp
// 1. Lock (mutual exclusion)
private readonly object _lock = new();
public void Increment()
{
    lock (_lock) { _count++; }
}

// 2. Interlocked (atomic operations — best for simple cases)
public void Increment()
{
    Interlocked.Increment(ref _count);
}

// 3. Concurrent collections
private readonly ConcurrentDictionary<string, int> _data = new();

// 4. Immutable data (no shared mutable state = no race conditions)
private readonly ImmutableList<int> _items = ImmutableList<int>.Empty;
```

---

### Q63. What is a deadlock and how do you prevent it?

**Expected Answer:**

A deadlock occurs when two or more threads are each waiting for a resource held by the other.

```csharp
// DEADLOCK scenario
object lockA = new(), lockB = new();

// Thread 1
lock (lockA)
{
    Thread.Sleep(100);  // Simulate work
    lock (lockB) { /* ... */ }  // Waiting for lockB (held by Thread 2)
}

// Thread 2
lock (lockB)
{
    Thread.Sleep(100);
    lock (lockA) { /* ... */ }  // Waiting for lockA (held by Thread 1)
}
// Both threads wait forever!
```

**Prevention strategies:**
1. **Consistent lock ordering** — Always acquire locks in the same order (A then B, never B then A).
2. **Lock timeout** — Use `Monitor.TryEnter(obj, timeout)` instead of `lock`.
3. **Reduce lock scope** — Hold locks for the shortest possible time.
4. **Avoid nested locks** — Redesign to use a single lock or lock-free structures.
5. **Use `async/await`** — Avoids thread blocking entirely.

---

### Q64. Explain the Singleton pattern's threading pitfalls.

**Expected Answer:**

```csharp
// BROKEN — race condition
public class Singleton
{
    private static Singleton _instance;
    public static Singleton Instance
    {
        get
        {
            if (_instance == null)           // Thread A checks: null
                _instance = new Singleton(); // Thread B also checks: null — two instances!
            return _instance;
        }
    }
}

// FIX 1: Double-checked locking
public class Singleton
{
    private static volatile Singleton _instance;
    private static readonly object _lock = new();

    public static Singleton Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_lock)
                {
                    if (_instance == null)
                        _instance = new Singleton();
                }
            }
            return _instance;
        }
    }
}

// FIX 2: Lazy<T> (recommended)
public class Singleton
{
    private static readonly Lazy<Singleton> _lazy =
        new(() => new Singleton());

    public static Singleton Instance => _lazy.Value;
    private Singleton() { }
}

// FIX 3: Static field initialization (simplest)
public class Singleton
{
    public static readonly Singleton Instance = new();
    private Singleton() { }
}
```

**Modern perspective:** In ASP.NET Core with DI, register as `AddSingleton<T>()` instead of implementing the pattern manually.

---

### Q65. Task vs Thread — what's the difference?

**Expected Answer:**

| Aspect | Thread | Task |
|--------|--------|------|
| **Level** | OS thread (1:1 mapping) | Abstraction over ThreadPool work |
| **Creation cost** | ~1 MB stack allocation | Lightweight object (~200 bytes) |
| **Return values** | None (must use shared state) | `Task<T>` carries result |
| **Composition** | Manual (Join, signals) | `await`, `WhenAll`, `ContinueWith` |
| **Exceptions** | Crashes thread / unobserved | Captured in Task, propagated on await |
| **Cancellation** | `Thread.Abort()` (dangerous) | `CancellationToken` (cooperative) |

```csharp
// Thread — low-level, avoid in modern code
var thread = new Thread(() => DoWork());
thread.Start();
thread.Join();  // Block until done

// Task — preferred
var task = Task.Run(() => DoWork());
await task;  // Non-blocking wait

// Task with result
Task<int> task = Task.Run(() => Calculate());
int result = await task;
```

---

### Q66. What really happens with async/await?

**Expected Answer:**

`async/await` is compiler-generated state machine magic — it transforms your method into a state machine that can pause and resume.

```csharp
// What you write:
public async Task<string> GetDataAsync()
{
    var response = await httpClient.GetAsync("url");  // Pause here
    var content = await response.Content.ReadAsStringAsync();  // Pause here
    return content;
}

// What the compiler generates (simplified):
public Task<string> GetDataAsync()
{
    var stateMachine = new GetDataAsyncStateMachine();
    stateMachine._builder = AsyncTaskMethodBuilder<string>.Create();
    stateMachine._state = -1;  // Initial state
    stateMachine._builder.Start(ref stateMachine);
    return stateMachine._builder.Task;
}

private struct GetDataAsyncStateMachine : IAsyncStateMachine
{
    public int _state;
    public AsyncTaskMethodBuilder<string> _builder;
    // All local variables become fields...

    public void MoveNext()
    {
        switch (_state)
        {
            case -1:  // Start
                var awaiter = httpClient.GetAsync("url").GetAwaiter();
                if (!awaiter.IsCompleted)
                {
                    _state = 0;
                    _builder.AwaitUnsafeOnCompleted(ref awaiter, ref this);
                    return;  // Yield control back to caller
                }
                goto case 0;
            case 0:  // GetAsync completed
                var response = awaiter.GetResult();
                // ... continue to next await
        }
    }
}
```

**Key points:**
- `await` does NOT create a new thread. It registers a continuation.
- If the awaited task is already complete, no suspension occurs.
- The method **returns to the caller** at the first incomplete await.
- When the I/O completes, the continuation runs (possibly on a different thread).

---

### Q67. What is the sync-over-async anti-pattern?

**Expected Answer:**

Calling `.Result`, `.Wait()`, or `.GetAwaiter().GetResult()` on a Task from synchronous code. This **blocks a thread** and can cause **deadlocks**.

```csharp
// DEADLOCK in ASP.NET (non-Core) / WinForms / WPF:
public string GetData()
{
    // Blocks the current thread (which owns the SynchronizationContext)
    var result = GetDataAsync().Result;  // DEADLOCK!
    return result;
}

public async Task<string> GetDataAsync()
{
    var data = await httpClient.GetStringAsync("url");
    // After await, tries to resume on the captured SynchronizationContext
    // But that thread is blocked by .Result above!
    return data;
}
```

**Why it deadlocks:**
1. `.Result` blocks the calling thread (e.g., UI thread or ASP.NET request thread).
2. `await` captures the `SynchronizationContext` and tries to resume on the original thread.
3. That thread is blocked → deadlock.

**Solutions:**
1. **Go async all the way** — `async Task` from controller to data layer.
2. **`ConfigureAwait(false)`** — Don't capture context (library code):
   ```csharp
   var data = await httpClient.GetStringAsync("url").ConfigureAwait(false);
   ```
3. In ASP.NET Core, `SynchronizationContext` was removed, so `.Result` won't deadlock — but it still **blocks a ThreadPool thread**, causing ThreadPool starvation.

---

### Q68. What is ThreadPool starvation?

**Expected Answer:**

ThreadPool starvation occurs when all ThreadPool threads are **blocked** (waiting on `.Result`, `Thread.Sleep`, synchronous I/O) and new work items can't start.

```
ThreadPool (8 threads on 8-core machine):
Thread 1: [BLOCKED on .Result] ← waiting for I/O
Thread 2: [BLOCKED on .Result]
Thread 3: [BLOCKED on .Result]
...
Thread 8: [BLOCKED on .Result]

New request arrives → NO FREE THREADS → queued → timeout → 503!
```

**The ThreadPool growth rate is slow** — it adds ~1-2 threads per second. If all threads block simultaneously, recovery takes minutes.

**Symptoms:**
- Request latency spikes
- ThreadPool queue grows
- `ThreadPool.GetAvailableThreads()` shows 0 available

**Prevention:**
1. Never block on async code (no `.Result`, `.Wait()`)
2. Use `async/await` for all I/O
3. Use async database methods (`FetchAsync`, `ExecuteAsync`)
4. Monitor ThreadPool metrics in production

---

[⬆ Back to Index](#table-of-contents)

# PART 15 — ASP.NET MVC State & Security

---

### Q69. ViewData vs ViewBag vs TempData — when to use each?

**Expected Answer:**

| Feature | ViewData | ViewBag | TempData |
|---------|----------|---------|----------|
| **Type** | `ViewDataDictionary` | `dynamic` wrapper over ViewData | `TempDataDictionary` |
| **Type safety** | No (casting required) | No (dynamic) | No (casting required) |
| **Lifetime** | Current request only | Current request only | Until read (survives redirect) |
| **Across redirects** | No | No | Yes (one-time read) |
| **Underlying storage** | Dictionary<string, object> | Same as ViewData | Session or cookies |

```csharp
// Controller
public IActionResult Index()
{
    ViewData["Message"] = "Hello from ViewData";      // Dictionary
    ViewBag.Greeting = "Hello from ViewBag";          // Dynamic property
    TempData["Alert"] = "Saved successfully";         // Survives redirect

    return RedirectToAction("Dashboard");  // ViewData/ViewBag lost here
}

public IActionResult Dashboard()
{
    var alert = TempData["Alert"];  // "Saved successfully" — available after redirect
    var alert2 = TempData["Alert"]; // null! Already consumed (one-time read)

    // Keep it for the next request too:
    TempData.Keep("Alert");  // or TempData.Peek("Alert") to read without consuming
}
```

**TempData lifecycle trap:**
- `TempData` uses session (or cookie) storage internally.
- Reading a key marks it for deletion at the end of the request.
- `Peek()` reads without marking for deletion.
- `Keep()` prevents deletion of an already-read key.

**Best practice:** Prefer view models over ViewData/ViewBag. Use TempData sparingly — only for redirect messages.

---

### Q70. Explain model binding in ASP.NET MVC.

**Expected Answer:**

Model binding maps HTTP request data to action method parameters automatically.

```csharp
// The framework populates 'model' from request data:
[HttpPost]
public IActionResult Create(ProductModel model)
{
    // model.Name comes from form field, query string, route data, or JSON body
}
```

**Binding sources (in order of precedence):**
1. **Form values** — POST body (`application/x-www-form-urlencoded`)
2. **Route values** — `/products/{id}` → `id` parameter
3. **Query string** — `?page=2&sort=name`
4. **Body** — JSON (`[FromBody]`), required for complex types in Web API

**Custom binding source attributes:**
```csharp
public IActionResult Search(
    [FromRoute] int categoryId,
    [FromQuery] string searchTerm,
    [FromHeader] string apiKey,
    [FromBody] SearchFilter filter)
```

**Validation with data annotations:**
```csharp
public class ProductModel
{
    [Required(ErrorMessage = "Name is required")]
    [StringLength(100)]
    public string Name { get; set; }

    [Range(0.01, 10000)]
    public decimal Price { get; set; }
}

// In action:
if (!ModelState.IsValid)
    return BadRequest(ModelState);
```

---

### Q71. Forms authentication vs Windows authentication.

**Expected Answer:**

| Aspect | Forms Authentication | Windows Authentication |
|--------|---------------------|----------------------|
| **Identity source** | Custom (database, API) | Active Directory / Windows account |
| **Login UI** | Custom login page | Browser dialog (NTLM/Kerberos) |
| **Token** | Cookie (encrypted ticket) | Windows token (NTLM/Kerberos) |
| **Best for** | Internet-facing apps | Intranet / corporate apps |
| **Setup** | Code + configuration | IIS + AD configuration |

**Modern approach (ASP.NET Core):**
```csharp
// Cookie authentication (replaces Forms auth)
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/Account/Login";
        options.ExpireTimeSpan = TimeSpan.FromHours(1);
    });

// JWT Bearer (API authentication)
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidIssuer = config["Jwt:Issuer"],
            IssuerSigningKey = new SymmetricSecurityKey(keyBytes)
        };
    });
```

---

[⬆ Back to Index](#table-of-contents)

# PART 16 — Data, ORM, SQL & Performance Thinking

---

### Q72. Temporary tables vs table variables in SQL Server.

**Expected Answer:**

| Feature | Temp Table (#table) | Table Variable (@table) |
|---------|--------------------|-----------------------|
| **Storage** | tempdb (on disk) | Memory (small), tempdb (large) |
| **Statistics** | Yes (helps optimizer) | No (assumes 1 row) |
| **Indexes** | Full index support | Primary key + unique only |
| **Transactions** | Participates in transactions | Not affected by ROLLBACK |
| **Scope** | Session (# local) or global (##) | Batch/procedure only |
| **Parallelism** | Supported | Not supported |
| **Best for** | Large datasets, complex queries | Small datasets (< ~100 rows) |

```sql
-- Temp table — better for large result sets
CREATE TABLE #ActiveUsers (
    UserId INT PRIMARY KEY,
    Name NVARCHAR(100)
);
INSERT INTO #ActiveUsers SELECT Id, Name FROM Users WHERE IsActive = 1;
-- Optimizer can use statistics for better plans

-- Table variable — better for small, simple operations
DECLARE @Ids TABLE (Id INT);
INSERT INTO @Ids VALUES (1), (2), (3);
SELECT * FROM Products WHERE CategoryId IN (SELECT Id FROM @Ids);
```

**Key insight for interviews:** "I use temp tables when I need the query optimizer to make good decisions — the statistics matter for large datasets. Table variables for small lookup lists where the overhead of statistics isn't needed."

---

### Q73. Explain normalization forms (1NF through 3NF).

**Expected Answer:**

**1NF (First Normal Form):**
- Each column has atomic (indivisible) values
- No repeating groups or arrays

```
❌ Violates 1NF:
| OrderId | Products         |
| 1       | Pen, Pencil, Ink |

✅ 1NF:
| OrderId | Product |
| 1       | Pen     |
| 1       | Pencil  |
| 1       | Ink     |
```

**2NF (Second Normal Form):**
- Is in 1NF
- No partial dependencies (every non-key column depends on the **entire** primary key)

```
❌ Violates 2NF (composite key: OrderId + ProductId):
| OrderId | ProductId | ProductName | Quantity |
              ↑ ProductName depends only on ProductId, not the full key

✅ 2NF: Split into Orders and Products tables
```

**3NF (Third Normal Form):**
- Is in 2NF
- No transitive dependencies (non-key columns depend only on the key, not on other non-key columns)

```
❌ Violates 3NF:
| EmployeeId | DepartmentId | DepartmentName |
                               ↑ Depends on DepartmentId, not EmployeeId

✅ 3NF: Move DepartmentName to a Departments table
```

**Practical rule:** "Every non-key column must provide a fact about the key, the whole key, and nothing but the key — so help me Codd."

---

### Q74. LINQ to SQL vs LINQ to Objects — what's the difference?

**Expected Answer:**

| Aspect | LINQ to Objects | LINQ to SQL / EF |
|--------|----------------|-------------------|
| **Data source** | In-memory collections | Database |
| **Execution** | `IEnumerable<T>` delegates | `IQueryable<T>` expression trees |
| **Where clause** | C# delegate: `Func<T, bool>` | Expression: `Expression<Func<T, bool>>` |
| **Translation** | No translation — executes C# code | Translated to SQL |
| **Filtering** | Client-side (all data in memory) | Server-side (SQL WHERE) |

```csharp
// LINQ to Objects — C# code runs in-process
List<User> users = GetUsers();
var active = users.Where(u => u.IsActive);  // Func<User, bool>

// LINQ to SQL — expression tree translated to SQL
IQueryable<User> dbUsers = db.Users;
var active = dbUsers.Where(u => u.IsActive);  // Expression<Func<User, bool>>
// Generates: SELECT * FROM Users WHERE IsActive = 1
```

**Critical mistake:**
```csharp
// This downloads ALL users then filters in memory:
var users = db.Users.ToList().Where(u => u.IsActive);  // BAD!

// This filters in the database:
var users = db.Users.Where(u => u.IsActive).ToList();   // GOOD!
```

---

### Q75. ORM trade-offs and performance considerations.

**Expected Answer:**

| Factor | Full ORM (EF Core) | Micro-ORM (Dapper/NPoco) | Raw ADO.NET |
|--------|--------------------|-----------------------------|-------------|
| **Productivity** | High (LINQ, migrations, change tracking) | Medium (SQL + mapping) | Low (manual everything) |
| **Performance** | Lower (overhead from tracking, SQL generation) | Near-raw (thin mapping layer) | Fastest (no overhead) |
| **Control** | Less (generated SQL may be suboptimal) | Full SQL control | Full control |
| **Complexity** | High (learning curve, gotchas) | Low | Low (but verbose) |

**Common ORM performance traps:**
1. **N+1 queries** — Lazy loading triggers a query per navigation property.
2. **Over-fetching** — `SELECT *` when you only need 2 columns.
3. **Tracking overhead** — Change tracking for read-only queries (use `AsNoTracking()`).
4. **Complex LINQ** — Unreadable generated SQL, poor execution plans.

**With micro-ORMs like NPoco:**
```csharp
// Direct, readable, performant
var users = db.Fetch<UserDto>("SELECT Id, Name FROM Users WHERE IsActive = 1");

// Parameterized (safe from SQL injection)
var user = db.SingleOrDefault<User>("WHERE Id = @0", userId);
```

---

[⬆ Back to Index](#table-of-contents)

# PART 17 — Serialization, WCF & Remoting

> *Legacy but still asked, especially in interviews for enterprise .NET roles.*

---

### Q76. What is serialization and why do we need it?

**Expected Answer:**

Serialization is converting an object graph into a **byte stream** (or text) for storage or transmission. Deserialization is the reverse.

**Why needed:**
- **Network communication** — Send objects over HTTP, TCP, message queues
- **Persistence** — Save object state to file/database
- **Caching** — Store objects in Redis/Memcached
- **Deep cloning** — Serialize + deserialize = independent copy

**Serialization formats:**

| Format | Human-readable | Size | Speed | Interop |
|--------|---------------|------|-------|---------|
| **JSON** | Yes | Medium | Fast | Excellent (universal) |
| **XML** | Yes | Large | Slow | Good (SOAP, config) |
| **Binary** | No | Small | Fastest | .NET only |
| **Protocol Buffers** | No | Smallest | Fastest | Excellent (schema-based) |

```csharp
// JSON (System.Text.Json — modern .NET)
var json = JsonSerializer.Serialize(user);
var user = JsonSerializer.Deserialize<User>(json);

// JSON (Newtonsoft.Json — widely used)
var json = JsonConvert.SerializeObject(user);
var user = JsonConvert.DeserializeObject<User>(json);
```

---

### Q77. XmlSerializer vs BinaryFormatter — when to use which?

**Expected Answer:**

| Aspect | XmlSerializer | BinaryFormatter |
|--------|--------------|-----------------|
| **Format** | XML text | Binary stream |
| **Interop** | Cross-platform | .NET only |
| **Versioning** | Tolerant (ignores unknown elements) | Fragile (type changes break it) |
| **Performance** | Slower (text parsing) | Faster (binary) |
| **Security** | Safer | **DANGEROUS — deserialization attacks** |
| **What it serializes** | Public properties/fields only | All fields (including private) |

**Critical warning:** `BinaryFormatter` is **obsolete and dangerous** in modern .NET. It's a known vector for Remote Code Execution (RCE) attacks:

```csharp
// NEVER use in production — marked obsolete in .NET 5, removed in .NET 9
BinaryFormatter formatter = new();
formatter.Deserialize(untrustedStream);  // RCE vulnerability!

// Modern alternatives:
// - System.Text.Json (JSON)
// - MessagePack (binary, fast)
// - Protocol Buffers (cross-platform binary)
```

---

### Q78. WCF basics — concurrency and instancing modes.

**Expected Answer:**

WCF (Windows Communication Foundation) was the .NET Framework's service framework. While replaced by gRPC and ASP.NET Core Web API in modern .NET, it's still in legacy systems.

**Instancing modes (ServiceBehavior):**

| Mode | Behavior | Use Case |
|------|----------|----------|
| **PerCall** | New instance per request | Stateless services (default, safest) |
| **PerSession** | One instance per client session | Stateful conversations |
| **Single** | One instance for all clients | Singleton resource |

**Concurrency modes:**

| Mode | Behavior | Thread Safety |
|------|----------|--------------|
| **Single** | One thread at a time (locked) | Safe but slow |
| **Multiple** | Multiple threads simultaneously | YOU handle thread safety |
| **Reentrant** | Single + allows callbacks | Careful with deadlocks |

```csharp
[ServiceBehavior(
    InstanceContextMode = InstanceContextMode.PerCall,
    ConcurrencyMode = ConcurrencyMode.Multiple)]
public class MyService : IMyService
{
    // PerCall + Multiple: each request gets a new instance,
    // but if somehow shared state exists, we handle concurrency
}
```

**Modern replacement:** gRPC for high-performance service-to-service communication, ASP.NET Core Web API for HTTP APIs.

---

[⬆ Back to Index](#table-of-contents)

# PART 18 — Modern .NET Additions

> *Current interview trends. Understanding the "why" behind changes is what separates candidates.*

---

### Q79. .NET Framework vs .NET Core — what was the philosophical shift?

**Expected Answer:**

| Aspect | .NET Framework | .NET Core / .NET 5+ |
|--------|---------------|---------------------|
| **Platform** | Windows only | Cross-platform (Windows, Linux, macOS) |
| **Deployment** | Machine-wide install | Self-contained or shared runtime |
| **Open source** | Partially | Fully open source (MIT license) |
| **Web server** | IIS only (System.Web) | Kestrel (cross-platform) + any reverse proxy |
| **Performance** | Good | 2-10x faster (benchmarks) |
| **API surface** | Large (everything included) | Modular (NuGet packages) |
| **Updates** | Tied to Windows Update | Independent release cycle |

**The philosophical shift:**
1. **From monolith to modular** — Only include what you need (NuGet).
2. **From Windows to everywhere** — Run on Linux containers.
3. **From IIS to Kestrel** — Own your HTTP server.
4. **From convention to configuration** — Explicit `Program.cs` setup instead of hidden `Global.asax` magic.
5. **From closed to open** — Community contributions, transparent roadmap.

---

### Q80. Why was System.Web removed?

**Expected Answer:**

`System.Web` was the 20-year-old foundation of ASP.NET Framework. It was removed because:

1. **Tightly coupled to IIS** — Couldn't run on Linux/macOS.
2. **Massive surface area** — Even "Hello World" loaded the full framework.
3. **ViewState, WebForms, SOAP** — Carried legacy baggage that hurt performance.
4. **Global state** — `HttpContext.Current` was a static god-object that made testing and async difficult.
5. **Thread-per-request model** — Didn't scale well for async I/O.

**What replaced it:**
- `Microsoft.AspNetCore.Http` — Lightweight `HttpContext`
- Kestrel — Fast, cross-platform HTTP server
- Middleware pipeline — Composable, testable request handling
- Dependency Injection — Built-in, not bolted on

---

### Q81. Explain the built-in Dependency Injection in .NET Core.

**Expected Answer:**

.NET Core includes a built-in DI container — no third-party library required.

```csharp
var builder = WebApplication.CreateBuilder(args);

// Registration
builder.Services.AddTransient<IEmailService, SmtpEmailService>();  // New instance each time
builder.Services.AddScoped<IUserRepository, UserRepository>();      // One per request
builder.Services.AddSingleton<ICacheManager, MemoryCacheManager>(); // One for app lifetime

var app = builder.Build();
```

**Lifetimes:**

| Lifetime | Created | Disposed | Use For |
|----------|---------|----------|---------|
| **Transient** | Every request for the service | When scope ends | Lightweight, stateless services |
| **Scoped** | Once per scope (HTTP request) | When scope ends | Per-request state (DbContext, UserContext) |
| **Singleton** | Once on first request | App shutdown | Caches, configuration, thread-safe services |

**Captive dependency trap:**
```csharp
// WRONG: Scoped service injected into Singleton — scoped service lives forever
builder.Services.AddSingleton<MySingleton>();       // Lives forever
builder.Services.AddScoped<MyScopedService>();      // Should live per request

public class MySingleton
{
    private readonly MyScopedService _scoped;  // BUG! This scoped instance never refreshes
    public MySingleton(MyScopedService scoped) => _scoped = scoped;
}
```

**Rule:** A service can only depend on services with **equal or longer** lifetimes:
- Singleton → can depend on Singleton
- Scoped → can depend on Scoped or Singleton
- Transient → can depend on anything

---

### Q82. Configuration and logging providers in .NET Core.

**Expected Answer:**

**Configuration (layered, overridable):**
```csharp
// Default configuration sources (in order — later sources override earlier):
// 1. appsettings.json
// 2. appsettings.{Environment}.json
// 3. User secrets (Development only)
// 4. Environment variables
// 5. Command-line arguments

var builder = WebApplication.CreateBuilder(args);
var connStr = builder.Configuration["ConnectionStrings:Default"];
// Or strongly typed:
builder.Services.Configure<MySettings>(builder.Configuration.GetSection("MySettings"));
```

**Options pattern:**
```csharp
public class EmailSettings
{
    public string SmtpServer { get; set; }
    public int Port { get; set; }
}

// In DI:
builder.Services.Configure<EmailSettings>(config.GetSection("Email"));

// In service:
public class EmailService
{
    private readonly EmailSettings _settings;
    public EmailService(IOptions<EmailSettings> options)
    {
        _settings = options.Value;
    }
}
```

**Logging:**
```csharp
public class OrderService
{
    private readonly ILogger<OrderService> _logger;

    public OrderService(ILogger<OrderService> logger) => _logger = logger;

    public void ProcessOrder(int orderId)
    {
        _logger.LogInformation("Processing order {OrderId}", orderId);
        // Structured logging — orderId becomes a searchable property
    }
}
```

---

### Q83. Middleware vs Filters — when to use each?

**Expected Answer:**

| Aspect | Middleware | Filters |
|--------|-----------|---------|
| **Scope** | All requests | MVC/Razor Pages only |
| **Access to** | HttpContext only | ActionContext, model binding, action results |
| **Runs** | Before and after MVC | Within MVC pipeline |
| **Order** | Configuration order in `Program.cs` | Attribute or global registration |
| **Best for** | Cross-cutting concerns (CORS, logging, auth) | Action-specific logic (validation, caching) |

```
Request
├── Middleware 1 (Logging)
├── Middleware 2 (Auth)
├── Middleware 3 (CORS)
├── MVC Middleware (UseEndpoints)
│   ├── Authorization Filter
│   ├── Resource Filter
│   ├── Action Filter (before)
│   ├── ACTION METHOD
│   ├── Action Filter (after)
│   ├── Result Filter (before)
│   ├── RESULT (view/JSON)
│   └── Result Filter (after)
├── Middleware 3 (response)
├── Middleware 2 (response)
└── Middleware 1 (response)
```

**Rule of thumb:**
- Need to run for ALL requests (including non-MVC)? → Middleware
- Need access to MVC features (model binding, action results)? → Filter
- Need to modify request/response headers globally? → Middleware
- Need to validate a specific action's model? → Filter

---

### Q84. What are Minimal APIs?

**Expected Answer:**

Minimal APIs (introduced in .NET 6) allow building HTTP APIs without controllers — a simpler, more concise approach.

```csharp
// Traditional controller
[ApiController]
[Route("[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _service;

    public ProductsController(IProductService service) => _service = service;

    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        var product = await _service.GetByIdAsync(id);
        return product is null ? NotFound() : Ok(product);
    }
}

// Minimal API equivalent
app.MapGet("/products/{id}", async (int id, IProductService service) =>
{
    var product = await service.GetByIdAsync(id);
    return product is null ? Results.NotFound() : Results.Ok(product);
});
```

**When to use Minimal APIs:**
- Microservices with few endpoints
- Prototyping and small APIs
- Lambda/serverless functions
- When controller ceremony is overkill

**When to prefer controllers:**
- Large APIs with many endpoints
- Complex filter pipelines
- When you need areas/grouping
- Team familiarity with MVC patterns

---

### Q85. What is the observability mindset?

**Expected Answer:**

Observability = **Logs + Metrics + Traces** — understanding system behavior from external outputs.

**Three pillars:**

| Pillar | What it captures | Tools |
|--------|-----------------|-------|
| **Logs** | Discrete events (errors, warnings, info) | Serilog, Datadog, ELK |
| **Metrics** | Numerical measurements over time | Prometheus, Grafana, App Insights |
| **Traces** | Request flow across services | Jaeger, Zipkin, OpenTelemetry |

**Modern .NET observability:**
```csharp
// Structured logging (not string interpolation!)
_logger.LogInformation("Order {OrderId} processed in {Duration}ms",
    orderId, stopwatch.ElapsedMilliseconds);
// OrderId and Duration become searchable fields in log aggregator

// OpenTelemetry integration
builder.Services.AddOpenTelemetry()
    .WithTracing(b => b
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddOtlpExporter());
```

**Key mindset shift:** "Can I understand what happened in production without attaching a debugger?" If no → more observability needed.

---

[⬆ Back to Index](#table-of-contents)

# PART 19 — How Interviewers Think (Meta Layer)

> *Understanding the game behind the game.*

---

### Q86. Why do interviewers ask "simple" questions?

**Expected Answer:**

Simple questions are **depth probes**. They start shallow to find where your knowledge ends.

```
Interviewer: "What is the difference between a value type and reference type?"

Level 1 (junior): "Value types are on the stack, reference types on the heap."
    → Follow-up: "Always on the stack?"

Level 2 (mid): "Value types contain data directly, reference types hold a reference.
    Value types can be on the stack OR inline in a class on the heap."
    → Follow-up: "What about boxing?"

Level 3 (senior): "Value types have copy semantics, reference types have reference semantics.
    Storage depends on context — local variable on stack, field in class on heap.
    Boxing wraps a value type in a heap object, which has GC and performance implications.
    For high-perf code, we use Span<T> and stackalloc to avoid allocations entirely."
    → Follow-up: "Tell me about a time this mattered in production."
```

**The signal they're looking for:** Can you go deeper than the textbook answer? Do you understand **why** it matters, not just **what** it is?

---

### Q87. What are the common follow-up question trees?

**Expected Answer:**

Interviewers follow threads. Here are typical escalation paths:

**Thread 1: Memory**
```
"What's a value type?" → "What's boxing?" → "Why is boxing expensive?"
→ "How does the GC work?" → "Tell me about Gen0/1/2" → "What's the LOH?"
→ "How would you diagnose a memory leak in production?"
```

**Thread 2: Async**
```
"What's async/await?" → "What happens at the compiler level?"
→ "What's ConfigureAwait?" → "What's the sync-over-async anti-pattern?"
→ "What's ThreadPool starvation?" → "How would you detect this in production?"
```

**Thread 3: Architecture**
```
"What's dependency injection?" → "What are the lifetimes?"
→ "What's a captive dependency?" → "How does the built-in DI container work?"
→ "When would you use a different container?" → "How do you test code with DI?"
```

**Strategy:** When answering, leave **hooks** — brief mentions of deeper topics that invite the interviewer to go where you're strong.

---

### Q88. What signals indicate real experience vs memorized answers?

**Expected Answer (from interviewer's perspective):**

**Signals of real experience:**
1. **"In my project, we..."** — Real examples from production systems
2. **Trade-off thinking** — "We chose X because Y, knowing we'd lose Z"
3. **Debugging stories** — "We had a memory leak caused by event handlers in a long-lived service"
4. **Admitting limits** — "I haven't used that in production, but I understand the concept is..."
5. **Asking clarifying questions** — "Are you asking about the Framework or Core pipeline?"
6. **Nuanced answers** — "It depends" with clear criteria for when each option applies

**Signals of memorized answers:**
1. **Perfect textbook phrasing** — Real knowledge is messier
2. **Can't go deeper** — Falls apart on follow-ups
3. **No examples** — All theory, no practice
4. **Binary answers** — "Always use X" without context
5. **Defensive when challenged** — "That's what the book says" vs exploring the question

---

### Q89. Common traps candidates fall into.

**Expected Answer:**

| Trap | Example | Better Approach |
|------|---------|-----------------|
| **Answering too broadly** | Explaining all of .NET when asked about GC | Focus on GC, mention related topics briefly |
| **Not admitting uncertainty** | Guessing about something you don't know | "I'm not sure about the exact detail, but my understanding is..." |
| **Going too deep too fast** | IL bytecodes when asked "What is C#?" | Start high-level, let the interviewer probe deeper |
| **Not asking questions** | Assuming what the interviewer means | "Are you asking about the threading model or the async pattern?" |
| **Talking without structure** | Stream-of-consciousness answer | "There are three key aspects: first..., second..., third..." |
| **Only knowing the happy path** | "async/await makes things concurrent" | "async/await enables non-blocking I/O, but doesn't automatically parallelize CPU-bound work" |

---

### Q90. How to steer a technical discussion in your favor.

**Expected Answer:**

**Techniques:**

1. **Breadcrumb technique:** Drop mentions of topics you know well.
   - "When we implemented the caching layer, we chose to use Redis with a **circuit breaker pattern**..."
   - The interviewer may ask about circuit breakers — which you're prepared for.

2. **Acknowledge and bridge:** When asked about a weak area, acknowledge and redirect.
   - "I haven't worked extensively with WCF, but in our microservices architecture, we used gRPC for service-to-service communication, which solved similar problems of..."

3. **Ask clarifying questions:** Shows depth and buys thinking time.
   - "When you say 'scalability', are you thinking about horizontal scaling with load balancers, or vertical scaling with async optimizations?"

4. **Use the STAR format for stories:**
   - **Situation:** "We had a production service handling 10K requests/second..."
   - **Task:** "Response times spiked to 30 seconds during peak hours..."
   - **Action:** "I profiled with dotnet-counters and found ThreadPool starvation from sync-over-async calls..."
   - **Result:** "After converting to async, p99 latency dropped from 30s to 200ms."

---

# APPENDIX A — Quick Reference Card

## C# Keywords Cheat Sheet

```
Access:     public | private | protected | internal | protected internal | private protected
Types:      class | struct | interface | enum | record | delegate
Modifiers:  static | abstract | virtual | override | sealed | new | readonly | const
Async:      async | await
Null:       null | ?? | ?. | ??= | ! (null-forgiving)
Pattern:    is | switch expression | when | and | or | not
Memory:     ref | out | in | params | stackalloc | Span<T>
```

## Common Interview One-Liners

| Question | Quick Answer |
|----------|-------------|
| `const` vs `readonly` | `const` = compile-time, inlined; `readonly` = runtime, constructor-set |
| `sealed` class | Cannot be inherited — performance + design intent |
| `static` class | Cannot be instantiated, only static members |
| `record` vs `class` | Record: value equality, immutable by default, `with` expressions |
| `is` vs `as` | `is` = type check (bool); `as` = cast or null (no exception) |
| `ref` vs `out` | `ref` = must initialize before; `out` = must assign in method |
| `throw` vs `throw ex` | `throw` = preserves stack trace; `throw ex` = resets it |
| `==` vs `Equals()` | `==` = reference equality (default); `Equals()` = value equality (overridable) |
| `IEnumerable` vs `ICollection` | `IEnumerable` = iterate; `ICollection` = count, add, remove |
| `Array` vs `List<T>` | `Array` = fixed size, fast index; `List<T>` = resizable, flexible |

---

## APPENDIX B — Code Snippets for Quick Revision

### Dependency Injection Pattern
```csharp
public interface ITaxService
{
    decimal CalculateTax(decimal income);
}

public class FederalTaxService : ITaxService
{
    private readonly ITaxRateProvider _rateProvider;
    private readonly ILogger _logger;

    public FederalTaxService(ITaxRateProvider rateProvider, ILoggerFactory loggerFactory)
    {
        _rateProvider = rateProvider;
        _logger = loggerFactory.CreateLogger<FederalTaxService>();
    }

    public decimal CalculateTax(decimal income)
    {
        var rate = _rateProvider.GetRate(income);
        _logger.LogInformation("Calculating tax for income {Income} at rate {Rate}", income, rate);
        return income * rate;
    }
}

// Registration
services.AddTransient<ITaxService, FederalTaxService>();
services.AddSingleton<ITaxRateProvider, CachedTaxRateProvider>();
```

### Async/Await Pattern
```csharp
public async Task<UserDto> GetUserAsync(int userId, CancellationToken ct = default)
{
    var user = await _repository.GetByIdAsync(userId, ct);
    if (user is null)
        throw new NotFoundException($"User {userId} not found");

    return MapToDto(user);
}
```

### IDisposable Pattern (Modern)
```csharp
public class DatabaseConnection : IDisposable
{
    private SqlConnection? _connection;
    private bool _disposed;

    public DatabaseConnection(string connectionString)
    {
        _connection = new SqlConnection(connectionString);
    }

    public void Dispose()
    {
        if (!_disposed)
        {
            _connection?.Dispose();
            _connection = null;
            _disposed = true;
        }
    }
}

// Usage — always with 'using'
using var conn = new DatabaseConnection(connStr);
```

### Thread-Safe Singleton
```csharp
public sealed class AppConfiguration
{
    private static readonly Lazy<AppConfiguration> _instance =
        new(() => new AppConfiguration());

    public static AppConfiguration Instance => _instance.Value;

    public string ApiBaseUrl { get; private set; }

    private AppConfiguration()
    {
        // Load configuration
        ApiBaseUrl = Environment.GetEnvironmentVariable("API_URL") ?? "https://api.example.com";
    }
}
```

---

> **Good luck with your interviews!** Remember: depth beats breadth. It's better to know 10 topics deeply than 50 topics superficially. Interviewers remember candidates who showed genuine understanding and real-world experience.

[⬆ Back to Index](#table-of-contents)
