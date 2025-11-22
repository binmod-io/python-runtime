# Binmod Runtime Python

Python runtime for executing Binmod WebAssembly modules.

## Quick Start

### Installation

```bash
pip install binmod
```

### Basic Usage

```python
from binmod import Module, ModuleEnv

# Load a binmod module
mod = Module.from_file(
    "my_module.wasm",
    name="my_module",
    environment=ModuleEnv.inherit(),
    namespace="test",
    host_fns={
        "host_log": lambda msg: print(f"Host log: {msg}"),
        "host_add": lambda a, b: a + b,
    }
)
mod.instantiate()

# Call a function from the module. Note: All parameters and outputs must be
# serializable to JSON.
result = mod.api.greet("Alice", 5)
print(result)  # Output: Hello, Alice you are 5 years old!

# Or use the async API
result = await mod.api_async.greet("Bob", 10)
print(result)  # Output: Hello, Bob you are 10 years old!
```

Note: The WebAssembly module must be compiled with the WASI Preview 1 target.

## License
This project is licensed under MIT License.

## Support & Feedback
If you encounter any issues or have feedback, please open an issue.
