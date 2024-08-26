# Facade Pattern

[Façade pattern](https://en.wikipedia.org/wiki/Facade_pattern) provides a simplified interface to a complex subsystem.

## Implementation

`LogDecorate` decorates a function with the signature `func(int) int` that
manipulates integers and adds input/output logging capabilities.

```go
// Subsystem1 represents a complex subsystem
type Subsystem1 struct{}

func (s *Subsystem1) Operation1() {
    fmt.Println("Subsystem1: Operation1")
}

// Subsystem2 represents another complex subsystem
type Subsystem2 struct{}

func (s *Subsystem2) Operation2() {
    fmt.Println("Subsystem2: Operation2")
}

// Subsystem3 represents yet another complex subsystem
type Subsystem3 struct{}

func (s *Subsystem3) Operation3() {
    fmt.Println("Subsystem3: Operation3")
}

// Facade provides a simplified interface to the complex subsystems
type Facade struct {
    subsystem1 *Subsystem1
    subsystem2 *Subsystem2
    subsystem3 *Subsystem3
}

func NewFacade() *Facade {
    return &Facade{
        subsystem1: &Subsystem1{},
        subsystem2: &Subsystem2{},
        subsystem3: &Subsystem3{},
    }
}

func (f *Facade) OperationA() {
    fmt.Println("Facade: OperationA")
    f.subsystem1.Operation1()
    f.subsystem2.Operation2()
}

func (f *Facade) OperationB() {
    fmt.Println("Facade: OperationB")
    f.subsystem2.Operation2()
    f.subsystem3.Operation3()
}
```

## Usage

```go
func main() {
    facade := NewFacade()
    facade.OperationA()
    facade.OperationB()
}
```

1. _Subsystem1_, _Subsystem2_, and _Subsystem3_ represent complex subsystems with their own operations.
1. The _Facade_ struct provides a simplified interface to these subsystems.
1. The _NewFacade_ function initializes the subsystems and returns a new instance of the Facade.
1. The _Facade_ struct has methods _OperationA_ and _OperationB_ that internally call the operations of the subsystems, providing a simplified interface to the client

This way, the client code can interact with the Facade instead of dealing with the complexities of the subsystems directly