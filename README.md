# Color [![GoDoc](https://godoc.org/github.com/murphybytes/color?status.svg)](https://godoc.org/github.com/murphybytes/color)

Color is based on the [github.com/fatih/color](https://github.com/fatih/color) package. Unfortunately the original
color package is archived and is no longer supported. Like the original, this package lets you use colorized outputs in 
terms of [ANSI Escape Codes](http://en.wikipedia.org/wiki/ANSI_escape_code#Colors) in Golang but offers a number of 
improvements from the original. Posix and Windows platforms are supported.  

Color seeks to remain *mostly* backward compatible with fatih/color but has a number of changes to support concurrency,
improved performance and a more idiomatic style. 

## Changes And Improvements 

The methods of the new `Color` struct do not mutate the sender. This
supports better concurrency support and improved performance. Towards this end `Color.Add` was removed. 

You don't need to remember to wrap arguments that implement `io.Writer` in `colorable.NewColorable`.  Functions that took
`io.Writer` in the past now expect a `*Console` argument which consistently provides the colorable functionality. Color
can be disabled by calling `Console.DisableColors` (see below).

Package public global variables are removed.  `color.NoColor` was removed. Colored output can be disabled using 
the `Console.DisableColors` method instead. `color.Output` and `color.Error` were removed, use `Stdout()` and `Stderr()` 
instead.  

Instances of `Console` can be passed to third party packages that take `io.Writer`  and log ANSI color information. The color
information will be interpreted correctly on Windows. In addition, color information can be stripped by calling
`Console.DisableColors(true)`.

Performance is improved significantly, as much as 400%.  

`fatih/color` has race conditions.  This package was developed with `test.Parallel` and `-race` enabled for tests. Thus 
far no race conditions are known and so this package is suitable for use in a multi goroutine environment. 

## Examples

### Standard colors

```go
// Print with default helper functions
color.Cyan("Prints text in cyan.")

// A newline will be appended automatically
color.Blue("Prints %s in blue.", "text")
```

### Mix and reuse colors

```go
// Create a new color object
c := color.New(color.FgCyan, color.Underline)
c.Println("Prints cyan text with an underline.")
```

### Use your own output (io.Writer)

```go
// Use your own io.Writer output
wtr := NewConsole(os.Stderr)
color.NewWithWriter(wtr, color.FgBlue)
color.Println("Hello! I'm blue.")
```

### Custom print functions (PrintFunc)

```go
// Create a custom print function for convenience
red := color.NewStderr(color.FgRed).PrintfFunc()
red("Warning")
red("Error: %s", err)

// Mix up multiple attributes
notice := color.New(color.Bold, color.FgGreen).PrintlnFunc()
notice("Don't forget this...")
```
### Insert into noncolor strings (SprintFunc)

```go
// Create SprintXxx functions to mix strings with other non-colorized strings:
yellow := color.New(color.FgYellow).SprintFunc()
red := color.New(color.FgRed).SprintFunc()
fmt.Printf("This is a %s and this is %s.\n", yellow("warning"), red("error"))

info := color.New(color.FgWhite, color.BgGreen).SprintFunc()
fmt.Printf("This %s rocks!\n", info("package"))

// Use helper functions
fmt.Println("This", color.RedString("warning"), "should be not neglected.")
fmt.Printf("%v %v\n", color.GreenString("Info:"), "an important message.")
```
### Disable/Enable color
 
There might be a case where you want to explicitly disable/enable color output. 

`Color` has support to disable/enable colors on a per `Console` basis.  
definitions. For example suppose you have a CLI app and a `--no-color` bool flag. You 
can easily disable the color output with:

```go

var flagNoColor = flag.Bool("no-color", false, "Disable color output")
color.Stdout().DisableColors(*flagNoColor)

```
## Credits

 * [Fatih Arslan](https://github.com/fatih)
 * Windows support via @mattn: [colorable](https://github.com/mattn/go-colorable)

## License

The MIT License (MIT) - see [`LICENSE.md`](https://github.com/murphybytes/color/blob/master/LICENSE.md) for more details


