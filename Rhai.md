

---

## **Rhai Mini-Lesson (15 minutes)**

### **1️⃣ What Rhai is**

- Lightweight scripting language for Rust.
    
- Designed to let you run scripts **dynamically** at runtime.
    
- Great for game logic, AI, configuration, modding, etc.
    

Key idea: You have a Rust `Engine` that runs scripts, and a `Scope` that stores **variables accessible to the script**.

---

### **2️⃣ Core Concepts**

**Engine**

```rust
let engine = Engine::new();
```

- Responsible for parsing and running scripts.
    

**Scope**

```rust
let mut scope = Scope::new();
scope.push("x", 42);
```

- Holds variables the script can see and modify.
    
- Can push Rust values that implement `Clone` (or register mutable access via functions).
    

**Compile scripts**

```rust
let ast = engine.compile(r#"
fn update() {
    x += 1;
}
"#).unwrap();
```

- AST = Abstract Syntax Tree, precompiled script you can run multiple times.
    

---

### **3️⃣ Running Scripts**

- **Direct evaluation**:
    

```rust
engine.eval_with_scope::<()>(&mut scope, "x += 1").unwrap();
```

- **AST execution**:
    

```rust
engine.eval_ast_with_scope::<()>(&mut scope, &ast).unwrap();
```

- **Calling functions inside AST**:
    

```rust
engine.call_fn::<()>(&mut scope, &ast, "update", ()).unwrap();
```

- This lets you define `update()` in a script and call it every frame (perfect for entities).
    

---

### **4️⃣ Passing Rust values to Rhai**

1. **Simple types** (`i32`, `f64`, `bool`, `String`) just push them into scope:
    

```rust
scope.push("dt", delta_time as f64);
```

2. **Custom structs** (like `Transform`):
    

- Register the type and getters/setters:
    

```rust
engine.register_type::<Transform>();
engine.register_get_set("x", |t: &mut Transform| t.x, |t: &mut Transform, v| t.x = v);
engine.register_get_set("y", |t: &mut Transform| t.y, |t: &mut Transform, v| t.y = v);
engine.register_get_set("z", |t: &mut Transform| t.z, |t: &mut Transform, v| t.z = v);
```

- Push **a clone** into scope, run the script, then pull it back.
    

---

### **5️⃣ Example: Moving an entity**

```rhai
fn update() {
    transform.x += 100.0 * dt;
    transform.y += 50.0 * dt;
}
```

- `transform` is a Rust struct you registered.
    
- `dt` is passed from Rust.
    
- Works every frame in your `update()` loop.
    

---

### **6️⃣ Multiple scripts per entity**

- Each script is a separate AST:
    

```rust
let move_script = engine.compile("fn update() { transform.x += 1.0; }").unwrap();
let rotate_script = engine.compile("fn update() { transform.z += 0.1; }").unwrap();
entity.scripts = vec![move_script, rotate_script];
```

- Call `update()` for each AST per entity every frame.
    

---

### **7️⃣ Quick Tips**

- **Debug:** use `println!("transform: {:?}", transform)` inside Rust, not Rhai.
    
- **Avoid collisions:** each script should have unique function names if needed.
    
- **Start simple:** move a triangle, then add rotation, bouncing, etc.
    
- **Scope is per frame per entity:** don’t share it unless intentional.
    

---

If you can get comfortable with this mini-pattern:

```rust
let mut scope = Scope::new();
scope.push("dt", dt);
scope.push("transform", entity.transform.clone());

for script in &entity.scripts {
    engine.eval_ast_with_scope::<()>(&mut scope, script)?;
    engine.call_fn::<()>(&mut scope, script, "update", ())?;
}

entity.transform = scope.get_value::<Transform>("transform").unwrap();
```

:

---

