# Broadcast Pattern

 In Go, you can create a broadcast concurrency pattern using goroutines and channels. This pattern allows you to send a message to multiple recipients simultaneously.

## Implementation

```go
// Broadcast sends a message to all recipients simultaneously
func Broadcast(message string, recipients ...chan string) {
    var wg sync.WaitGroup
    for _, recipient := range recipients {
        wg.Add(1)
        go func(recipient chan string) {
            defer wg.Done()
            recipient <- message
        }(recipient)
    }
    wg.Wait()
}
```

## Usage

```go
func main() {
    // Create channels for recipients
    recipient1 := make(chan string)
    recipient2 := make(chan string)
    recipient3 := make(chan string)

    // Start goroutines to receive messages
    go func() {
        fmt.Println("Recipient 1 received:", <-recipient1)
    }()
    go func() {
        fmt.Println("Recipient 2 received:", <-recipient2)
    }()
    go func() {
        fmt.Println("Recipient 3 received:", <-recipient3)
    }()

    // Broadcast a message to all recipients
    Broadcast("Hello, World!", recipient1, recipient2, recipient3)
}
```

1. The *Broadcast* function takes a message and a variable number of recipient channels.
1. It uses a *sync.WaitGroup* to wait for all goroutines to finish sending the message.
1. Each recipient channel receives the message simultaneously through a goroutine.
