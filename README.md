# Binmod Runtime for Python

Python runtime for loading and executing Binmod WebAssembly modules in your applications.

## Overview

The Binmod Python Runtime allows you to embed WebAssembly-based plugins into your Python applications. Load modules written in any language with a Binmod MDK (Rust, Python, and more coming soon).

## Installation

```bash
pip install binmod
```

## Load a Module

```python
from binmod import Module, ModuleEnv

mod = Module.from_file(
    "my_calculator.wasm",
    name="my_calculator",
    environment=ModuleEnv.inherit(),
    namespace="calculator",
    host_fns={
        "log": lambda msg: print(f"[Module] {msg}"),
        "get_pi": lambda: 3.14159,
    }
)
mod.instantiate()
```

**Key points:**
- **name**: Identifier for your module
- **namespace**: Groups related host functions together
- **host_fns**: Dictionary mapping function names to Python callables that the module can invoke
- All host function parameters and return values must be JSON-serializable

## Call Module Functions

**Synchronous API:**
```python
area = mod.api.circle_area(5.0)
print(f"Circle area: {area}")

sum_result = mod.api.add(10, 20)
print(f"Sum: {sum_result}")

# Alternatively, using the call method
area = mod.call("circle_area", (5.0,))
sum_result = mod.call("add", (10, 20))
```

**Asynchronous API:**
```python
area = await mod.api_async.circle_area(5.0)
print(f"Circle area: {area}")

sum_result = await mod.api_async.add(10, 20)
print(f"Sum: {sum_result}")

# Alternatively, using the async call method
area = await mod.call_async("circle_area", (5.0,))
sum_result = await mod.call_async("add", (10, 20))
```

## Complete Example

```python
from binmod import Module, ModuleEnv

# Define host functions that the module can call
def log_message(msg):
    print(f"[Module] {msg}")

def get_pi():
    return 3.14159

# Load the module
mod = Module.from_file(
    "my_calculator.wasm",
    name="my_calculator",
    environment=ModuleEnv.inherit(),
    namespace="calculator",
    host_fns={
        "log": log_message,
        "get_pi": get_pi,
    }
)

# Instantiate the module
mod.instantiate()

# Call module functions
area = mod.api.circle_area(5.0)
# Or alternatively using the call method
# area = mod.call("circle_area", (5.0,))
print(f"Circle area: {area}")

sum_result = mod.api.add(10, 20)
# Or alternatively using the call method
# sum_result = mod.call("add", (10, 20))
print(f"10 + 20 = {sum_result}")
```

## Error Handling

```python
from binmod import FnError

try:
    result = mod.api.circle_area(5.0)
except FnError as e:
    print(f"Module execution error: {e.message}")
```

## Module Compatibility

WebAssembly modules must be compiled with the WASI Preview 1 target. Modules created with any Binmod MDK are compatible with this runtime.

## Backwards Compatibility

This runtime is currently in an early stage. Future versions may introduce breaking changes until the API is stabilized. The API is
considered stable starting from version 1.0.0.

## License

MIT License

## Support

If you encounter issues or have questions, please open an issue on GitHub.