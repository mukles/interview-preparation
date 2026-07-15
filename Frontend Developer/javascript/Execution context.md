

> **Execution Context → Call Stack → Web APIs → Queues → Event Loop**

---

# Mid-Level Answer

> **Interviewer:** Can you explain how JavaScript executes code?

### Answer

JavaScript is a **single-threaded, synchronous language**. It executes one task at a time using a **Call Stack**. Before any code runs, JavaScript creates an **Execution Context**, which contains all the information needed to execute that code.

There are three important concepts:

1. **Execution Context**
    
2. **Call Stack**
    
3. **Event Loop**
    

---

# 1. Execution Context

An Execution Context is the environment where JavaScript code is executed.

There are three types:

- Global Execution Context (created once)
    
- Function Execution Context (created whenever a function is called)
    
- Eval Execution Context (rarely used)
    

When the JavaScript engine starts, it first creates the **Global Execution Context**.

For example:

```javascript
var a = 10;

function greet(name) {
    return `Hello ${name}`;
}

greet("Mukles");
```

The engine first creates the Global Execution Context.

---

## Each Execution Context has two phases

### Creation Phase

During this phase JavaScript:

- Creates the global object (`window` in browsers or `global` in Node.js).
    
- Sets the value of `this`.
    
- Allocates memory for variables.
    
- Stores function declarations in memory.
    

Example:

```javascript
console.log(a);

var a = 5;
```

During the Creation Phase:

```text
a → undefined
```

Only memory is allocated.

---

### Execution Phase

Now JavaScript executes the code line by line.

```javascript
var a = 5;
```

Now

```text
a = 5
```

The value changes from `undefined` to `5`.

---

# Function Execution Context

Whenever a function is called,

```javascript
function add(a, b) {
    return a + b;
}

add(5, 10);
```

JavaScript creates a **new Execution Context** for that function.

That Function Execution Context is pushed onto the Call Stack.

When the function finishes,

its Execution Context is destroyed.

---

# Call Stack

The Call Stack keeps track of all active Execution Contexts.

Example

```javascript
function one() {
    two();
}

function two() {
    console.log("Hello");
}

one();
```

Stack

```text
Global

↓

one()

↓

two()

↓

console.log()
```

After execution

```text
console.log()

↓

removed

↓

two()

↓

removed

↓

one()

↓

removed

↓

Global
```

---

# Event Loop

Now suppose we write

```javascript
console.log("Start");

setTimeout(() => {
    console.log("Timeout");
}, 0);

Promise.resolve().then(() => {
    console.log("Promise");
});

console.log("End");
```

Execution

### Step 1

Global Execution Context executes.

Output

```text
Start
```

---

### Step 2

`setTimeout`

Browser handles it.

JavaScript continues.

---

### Step 3

Promise

Promise callback enters the **Microtask Queue**.

---

### Step 4

Output

```text
End
```

---

Current state

```text
Call Stack
---------
Empty

Microtask Queue
--------------
Promise

Callback Queue
-------------
Timeout
```

---

The Event Loop checks:

> Is the Call Stack empty?

Yes.

First, it executes **all Microtasks**.

Output

```text
Promise
```

Then it executes the Callback Queue.

Output

```text
Timeout
```

Final Output

```text
Start
End
Promise
Timeout
```

---

# Why do Promises execute before `setTimeout`?

Because Promise callbacks go into the **Microtask Queue**, while `setTimeout` callbacks go into the **Macrotask (Callback) Queue**.

The Event Loop always empties the Microtask Queue before processing the next macrotask.

---

# Real Example from Your Experience

Since you've worked on **Brritto AI Tutor**, you can connect the concept to real work:

> "For example, when a user submits a question in our AI Tutor application, we make an asynchronous API request using `fetch` or `axios`. While waiting for the server response, JavaScript doesn't block the UI. The browser handles the network request, and once the Promise resolves, its callback is placed in the Microtask Queue. After the current synchronous code completes, the Event Loop executes the callback, updates the React state, and React re-renders the chat interface. This keeps the application responsive even during network operations."
