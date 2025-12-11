# Beyond the Magic: A Beginner’s Guide to Helper Functions and Composition

In the world of TypeScript, features like Decorators can automate tasks behind the scenes. While powerful, this "magic" can sometimes be difficult to understand. When things break, the lack of visibility makes debugging harder.

If you want to write code that is easy to read, easy to fix, and easy to explain to others, mastering the fundamentals is essential. Today, we are exploring two powerful alternatives to implicit logic: **Helper Functions** and **Composition**.

## 1. Helper Functions: The Art of Explicitness

A standard Decorator acts automatically—it modifies behavior simply by being attached to a method. While convenient, it can obscure what is actually happening.

A **Helper Function**, by contrast, is explicit. It only runs when you call it. This gives you full control over _when_ and _how_ logic is applied.

### Why is this better for beginners?

- **No Surprises:** You can see exactly where the function is called in the code.
    
- **Easy to Trace:** You don't have to guess how a result was calculated. You can simply look at the line of code and follow the execution path.
    

### The Example: Validation

In this example, instead of hiding validation rules inside a complex system or decorator, we create a standalone function called `isValidEmail`. It has a single responsibility: checking if an email string is formatted correctly.

Inside our `UserService`, we manually call this function.

```
// THE HELPER FUNCTION
// It takes a string and returns true or false. Simple.
function isValidEmail(email: string): boolean {
  // A standard pattern (Regex) to check for an @ symbol and a dot.
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}

class UserService {
  registerUser(email: string, name: string): void {
    
    // THE EXPLICIT CHECK
    // We call the helper right here. If it fails, we stop.
    // There is no ambiguity—you can see exactly why the error would be thrown.
    if (!isValidEmail(email)) {
      throw new Error("Invalid email format.");
    }
    
    // ... proceed with registration logic ...
    console.log(`User ${name} registered with email ${email}`);
  }
}

const service = new UserService();
// This works perfectly
service.registerUser("test@example.com", "Narges"); 

// This will crash with "Invalid email format."
// service.registerUser("invalid-email", "Charlie"); 
```

**Key Takeaway:** By using a helper function, the dependency is clear. Anyone reading `registerUser` immediately understands that the method relies on `isValidEmail` for validation.

## 2. Composition: Modular Software Design

Composition is the practice of combining simple, independent units of code to build more complex behavior. Rather than writing a massive, monolithic function, you build your system using smaller, reusable components.

In coding, we use Composition to wrap simple logic inside other functions to extend their capabilities (such as adding logging or caching) without modifying the original code.

### Technique A: Function Composition (Higher-Order Functions)

This technique involves creating a "wrapper" function.

1. **The Core Function:** Performs the actual business logic (e.g., a calculation).
    
2. **The Wrapper Function:** Accepts the core function as an argument, adds extra behavior (like logging), and returns a new function.
    

#### The Example: Logging

We want to log every time a function is called. Instead of writing `console.log` inside every single function, we create a generic wrapper called `withLogging`.

```
// THE WRAPPER
// It accepts a function 'fn' as input and returns a NEW function.
function withLogging<Args extends any[], Return>(fn: (...args: Args) => Return) {
  
  // This is the new function that replaces the old one
  return function (...args: Args): Return {
    console.log(`Calling ${fn.name} with args:`, args); // 1. Do the logging
    const result = fn(...args);                         // 2. Run the original function
    console.log(`${fn.name} returned:`, result);        // 3. Log the result
    return result;                                      // 4. Return the result
  };
}

// THE CORE LOGIC
// This function strictly performs the calculation.
function calculateDiscountedPrice(price: number, discount: number): number {
  return price * (1 - discount);
}

// COMBINE THEM
// We create a new function by wrapping the core logic with the logger.
const calculatePriceWithLog = withLogging(calculateDiscountedPrice);

// Usage
calculatePriceWithLog(100, 0.10); 
```

**Key Takeaway:** `calculateDiscountedPrice` remains pure. If you need to remove logging later, you simply stop using `withLogging`. You do not need to edit the calculation logic itself.

### Technique B: Object Composition (The Proxy Pattern)

This approach is used for Classes. It involves creating a wrapper class that looks identical to the original class but sits between the caller and the real service.

1. **The Real Service:** Performs the actual operation (e.g., fetching data).
    
2. **The Intermediary (Wrapper):** Intercepts the call. It can decide to return data immediately (from a cache) or forward the request to the Real Service.
    

#### The Example: Caching

We use an `interface` to ensure both the Real Service and the Caching Wrapper adhere to the same contract.

```
// THE CONTRACT
// Both the Real service and the Caching wrapper must follow these rules.
interface DataService {
    fetchData(id: number): Promise<string>;
}

// THE REAL WORKER
// It handles the resource-intensive work (fetching from an API).
class RealDataService implements DataService {
    async fetchData(id: number): Promise<string> {
        // ... actual API call logic ...
        return `Data for ${id} from API`;
    }
}

// THE WRAPPER (Intermediary)
class CachingDataService implements DataService {
    private cache: Map<number, string> = new Map();

    // COMPOSITION HAPPENS HERE:
    // The CachingService "has a" RealDataService inside it.
    // It takes the real service as a parameter in the constructor.
    constructor(private innerService: DataService) {}

    async fetchData(id: number): Promise<string> {
        // 1. Check if we already have the data locally
        if (this.cache.has(id)) {
            console.log(`Fetching ${id} from cache`);
            return this.cache.get(id)!;
        }
        
        // 2. If not, request it from the Real Service
        console.log(`Fetching ${id} from real service`);
        const data = await this.innerService.fetchData(id);
        
        // 3. Save the result for future requests
        this.cache.set(id, data);
        return data;
    }
}

// Usage:
const realService = new RealDataService();
// We wrap the real service inside the caching service
const cachedService = new CachingDataService(realService);

cachedService.fetchData(1); // Goes to API, then caches
cachedService.fetchData(1); // Second time: Returns directly from CachingDataService
```

**Key Takeaway:** This is powerful because `RealDataService` does not need to contain any caching logic. You can stack multiple layers—Caching, Logging, and Error Handling—without ever modifying the core API client.

## 3. Real-World Guide: Use Cases and Best Practices

Now that you understand the concepts, here is a practical guide on when to apply each tool.

### Helper Functions

**Best Used For:**

- **Pure Logic:** Calculations (e.g., tax, discount), string formatting (e.g., `formatDate`), or regex validation.
    
- **Shared Utilities:** Code that is repeated across many files (e.g., `capitalizeName`).
    

**Best Practices:**

- **Keep it Pure:** Input in, output out. Avoid changing global variables inside a helper.
    
- **Name Clearly:** Use verbs that describe the action (e.g., `get...`, `is...`, `format...`).
    

### Composition (Higher-Order Functions)

**Best Used For:**

- **Cross-Cutting Concerns:** Tasks that apply to _many_ different functions, like Logging, Error Handling, or Performance Timing.
    
- **Middleware:** If you have used Express.js or Redux, you have likely used this pattern to process requests before they hit your main logic.
    

**Best Practices:**

- **Preserve Signatures:** Ensure the "wrapper" function accepts the same arguments and returns the same type as the original, so they are interchangeable.
    

### Composition (Object Wrapping)

**Best Used For:**

- **Service Layers:** Wrapping API clients or Database repositories.
    
- **Feature Toggles:** Swapping between a "Real" service and a "Test" or "Mock" service for testing.
    
- **Resilience:** Adding "Retries" or "Circuit Breakers" to network calls without complicating the main code.
    

**Best Practices:**

- **Use Interfaces:** As seen in the example (`DataService`), using an interface ensures the wrapper and the real service look identical to the rest of the application.
    

## Quick Comparison

|   |   |   |
|---|---|---|
|**Approach**|**What is it?**|**Best for...**|
|**Helper Functions**|Calling a utility function explicitly.|Validation, formatting, calculations.|
|**Composition (HOF)**|Wrapping a function inside another function.|Logging, measuring speed (performance).|
|**Composition (Objects)**|Wrapping a class inside another class.|Caching, database connections, API calls.|