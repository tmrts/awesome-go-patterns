# Singleton Pattern

Singleton creational design pattern restricts the instantiation of a type to a single object.

## Implementation

```go
package singleton

type singleton map[string]string

var instance singleton

func New() singleton {
	if instance == nil {
		instance = make(singleton)
	}

	return instance
}
```

## Usage

```go
s := singleton.New()

s["this"] = "that"

s2 := singleton.New()

fmt.Println("This is ", s2["this"])
// This is that
```

## Rules of Thumb

- Singleton pattern represents a global state and most of the time reduces testability.
