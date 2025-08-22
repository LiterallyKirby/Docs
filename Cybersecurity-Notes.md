# **WEEK 1 Notes: Heap vs Stack & gdb**

## **Heap and Stack**

### **HEAP (dynamic memory)**

* Allocated with `malloc` (or `Box` in Rust)

* Persists until manually freed

heap: 27          ← value stored on heap  
↑ pointer stored in main: heap

### **STACK (grows downward)**

* Automatic, local variables

* Each function call gets its own stack frame

helper() frame:  
    y: 24          ← copy of z (passed on stack)  
    a: 32          ← local variable

main() frame:  
    x: 23  
    z: 24  
    zptr → \&z  
    heap → pointer to heap

---

## **gdb Cheat Sheet**

### **Program Control**

| Command | Description |
| ----- | ----- |
| `run` | Start the program inside gdb |
| `break <line/function>` | Set a breakpoint at a line or function |
| `next` | Execute the next line (step over function calls) |
| `step` | Step into a function call |
| `continue` | Resume execution until next breakpoint |
| `finish` | Run until current function returns |

### **Inspecting Variables & Memory**

| Command | Description |
| ----- | ----- |
| `print <var>` | Print the value of a variable |
| `print &<var>` | Print the memory address of a variable |
| `x/<format> <address>` | Examine memory at a specific address (e.g., `x/4x &x`) |
| `set <var> = <value>` | Change the value of a variable in memory |
| `info locals` | List all local variables in the current function |
| `info args` | List function arguments |
| `info registers` | Show CPU registers (for low-level memory inspection) |

### **Stack Navigation**

| Command | Description |
| ----- | ----- |
| `backtrace` or `bt` | Show the call stack |
| `frame <n>` | Move to a specific frame on the call stack |
| `up / down` | Navigate up/down the call stack frames |

### **Other Useful Commands**

| Command | Description |
| ----- | ----- |
| `quit` | Exit gdb |
| `list` or `l` | Show source code around the current line |
| `info breakpoints` | List all breakpoints |
| `delete <num>` | Delete a breakpoint |
| `watch <var>` | Stop execution when a variable changes |

---

## **Stack vs Heap gdb Quickmap**

| Memory Type | What to Inspect | gdb Commands |
| ----- | ----- | ----- |
| **Stack** | Local variables, function arguments, stack frame layout | `print <var>` / `print &<var>` / `info locals` / `info args` / `backtrace` / `frame <n>` |
| **Heap** | Dynamically allocated memory (`malloc`/`Box`) | `print <pointer>` / `print *<pointer>` / `set *<pointer> = <value>` / `x/<format> <pointer>` |
| **Function Calls** | See stack frame growth/shrink | `step` / `next` / `backtrace` / `finish` |
| **Memory Watch** | Track changes in stack or heap | `watch <var>` or `watch *<pointer>` |

Tip: **Stack addresses** usually start with `0x7fff…` on x86\_64 Linux, **heap addresses** usually start with `0x60…` or `0x555…` depending on the program. Use `print &var` or `print pointer` to verify.

---

## **WEEK 2 Notes: Memory Scanning & ptrace in Rust**

### **Memory Scanning Basics**

* Memory scanning \= reading another process’s memory to find values (like Cheat Engine).

* You can scan for integers, floats, or other data types.

* Requires permissions: you must own the target process or use root.

---

### **Memory Regions**

Linux divides process memory into regions, each with permissions:

| Region | Typical Contents | Notes |
| ----- | ----- | ----- |
| Stack | Function frames, local variables | Grows downward; high addresses (`0x7fff…`) |
| Heap | Dynamically allocated memory | Allocated with `malloc` (C) or `Box` (Rust) |
| Data | Global/static variables | Fixed size, often read/write |
| Text | Executable code | Usually read-only |

