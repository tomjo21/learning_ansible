# YAML

YAML (YAML Ain't Markup Language) is a human-readable data serialization format that is commonly used for configuration files and data exchange between languages with different data structures.

## YAML Syntax

### Strings, Numbers, and Booleans

```yaml
string: Hello, World!
number: 42
boolean: true
```

### List

```yaml
fruits:
  - Apple
  - Orange
  - Banana
```

### Dictionary

```yaml
person:
  name: John Doe
  age: 30
  city: New York
```

### List of Dictionaries

YAML allows nesting of lists and dictionaries to represent more complex data structures.

```yaml
family:
  parents:
    - name: Jane
      age: 50
    - name: John
      age: 52
  children:
    - name: Jimmy
      age: 22
    - name: Jenny
      age: 20
```