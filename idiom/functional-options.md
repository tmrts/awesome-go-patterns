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

func WithUID(userID int) Option {
	return func(args *Options) {
		args.UID = userID
	}
}

func WithGID(groupID int) Option {
	return func(args *Options) {
		args.GID = groupID
	}
}

func WithContents(c string) Option {
	return func(args *Options) {
		args.Contents = c
	}
}

func WithPermissions(perms os.FileMode) Option {
	return func(args *Options) {
		args.Permissions = perms
	}
}
```

### Constructor

```go
package file

func New(filepath string, setters ...Option) error {
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
		return err
	} else {
		defer f.Close()
	}

	if _, err := f.WriteString(args.Contents); err != nil {
		return err
	}

	return f.Chown(args.UID, args.GID)
}
```

## Usage

```go
emptyFile, err := file.New("/tmp/empty.txt")
if err != nil {
    panic(err)
}

fillerFile, err := file.New("/tmp/file.txt", file.WithUID(1000), file.WithContents("Lorem Ipsum Dolor Amet"))
if err != nil {
    panic(err)
}
```
