# Builder Pattern

Builder pattern separates the construction of a complex object from its
representation so that the same construction process can create different
representations.

In Go, normally a configuration struct is used to achieve the same behavior,
however passing a struct to the builder method fills the code with boilerplate
`if cfg.Field != nil {...}` checks.

## Implementation

```go
package car

type Speed float64

const (
    MPH Speed = 1
    KPH       = 1.60934
)

type Color string

const (
    BlueColor  Color = "blue"
    GreenColor       = "green"
    RedColor         = "red"
)

type Wheels string

const (
    SportsWheels Wheels = "sports"
    SteelWheels         = "steel"
)

type Builder interface {
    Color(Color) Builder
    Wheels(Wheels) Builder
    TopSpeed(Speed) Builder
    Build() Interface
}

type Interface interface {
    Drive() error
    Stop() error
}
```

## Usage

```go
assembly := car.NewBuilder().Paint(car.RedColor)

familyCar := assembly.Wheels(car.SteelWheels).TopSpeed(50 * car.MPH).Build()
familyCar.Drive()

sportsCar := assembly.Wheels(car.SportsWheels).TopSpeed(150 * car.MPH).Build()
sportsCar.Drive()
```
