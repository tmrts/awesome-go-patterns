# Functional Options

Functional options are a method of implementing clean/eloquent APIs in Go.
Options implemented as a function set the state of that option.

## Implementation

### Options

```go
package file

type Options struct {
	UID         int
	GID         int
	Flags       int
	Contents    string
	Permissions os.FileMode
}

type Option func(*Options)

func UID(userID int) Option {
	return func(args *Options) {
		args.UID = userID
	}
}

func GID(groupID int) Option {
	return func(args *Options) {
		args.GID = groupID
	}
}

func Contents(c string) Option {
	return func(args *Options) {
		args.Contents = c
	}
}

func Permissions(perms os.FileMode) Option {
	return func(args *Options) {
		args.Permissions = perms
	}
}
```

### Constructor

```go
package file

func New(filepath string, setters ...Option) (*os.File, error) {
	// Default Options
	args := &Options{
		UID:         os.Getuid(),
		GID:         os.Getgid(),
		Contents:    "",
		Permissions: 0666,
		Flags:       os.O_CREATE | os.O_EXCL | os.O_WRONLY,
	}

	for _, setter := range setters {
		setter(args)
	}

	f, err := os.OpenFile(filepath, args.Flags, args.Permissions)
	if err != nil {
		return nil, err
	}
	defer f.Close()

	if _, err := f.WriteString(args.Contents); err != nil {
		return nil, err
	}

	f.Chown(args.UID, args.GID)
	return f, nil
}
```

## Usage

```go
func main() {
	emptyFile, err := file.New("/tmp/empty.txt")
	if err != nil {
		panic(err)
	}
	fmt.Printf("file %v created\n", emptyFile.Name())

	fillerFile, err := file.New("/tmp/file.txt", file.UID(1000), file.Contents("Lorem Ipsum Dolor Amet"))
	if err != nil {
		panic(err)
	}
	fmt.Printf("file %v created\n", fillerFile.Name())

	filePermissions, err := file.New("/tmp/file-permissions.txt", file.Permissions(0777), file.Contents("With Permissions 0777"))
	if err != nil {
		panic(err)
	}
	fmt.Printf("file %v created\n", filePermissions.Name())
}
```
