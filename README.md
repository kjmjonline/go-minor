## <a id="readme">README</a>

![Go language badge](http://shields.io/badge/language-Go-cyan.svg
"Go language badge")
![MIT license badge](http://shields.io/badge/license-MIT-blue.svg
"MIT license badge")

The **_minor_** package contains minor[^1] enhancements to a) the
![Go language standard-library](http://pkg.go.dev/std
"Go language standard library API");
and b) popular open source libraries.

<details>
<summary>Table of Contents</summary>

<p/>
**Table of Contents**
- <a href="#version" alt="version">Version</a>
- <a href="#synopsis" alt="synopsis">Synopsis</a>
- <a href="#description" alt="description">Description</a>
- <a href="#installation" alt="installation">Installation</a>
- <a href="#funcs" alt="functions">Public Functions</a>
  - <a href="#capture" alt="capture output">CaptureOutput</a>
  - <a href="#filepath" alt="">FilePathInCwd</a>
  - <a href="#ignore" alt="ignore unused">IgnoreUnused</a>
  - <a href="#setlog"
       alt="set global zerolog to file">SetGlobalZerologToFile</a>
- <a href="#dependencies" alt="dependencies">Dependencies</a>
- <a href="#incompat" alt="incompatibilities">Incompatibilities</a>
- <a href="#bugs" alt="bugs and limitations">Bugs and Limitations</a>
- <a href="#thanks" alt="acknowledgements">Acknowledgements</a>
- <a href="#author" alt="author">Author</a>
- <a href="#copyright"
     alt="copyright and license">Copyright and License</a>
</details>

### <a name="version">Version</a>

This documentation is for _go-minor_ version **0.1.0**

### <a name="synopsis">Synopsis</a>

```go
import (
    "log"

    "github.com/kjmjonline/go-minor/v0.1.0"
)

func main() {
    // Let's use FilePathInCwd to get the fully qualified file name
    // of the file to write logs to in the current working directory
    var filepath string
    var err error
    if filepath, err = minor.FilePathInCwd("my-project.log"); err !=  nil {
        log.Fatal(err)
    }

    // Now we will initialize the zerlogo logging to this file
    if err = minor.SetGlobalZerologToFile(filepath) {
        log.Fatal(err)
    }

    // At this point we can log using the zerolog global logger
    log.Debug().Msg(
        "at this point we can log using the global zerolog logger",
        )

    ...

    // We can also use minor.CaptureOutput to capture and test any output
}
```

### <a name="description">Description</a>

This package contains a variety of functions. These are trivial functions
that are needed by kjmjonline packages.

The
SetGlobalZerologToFile
function sets up the
zerolog
global logger with settings that kjmjonline uses by default.

FilePathInCwd
can be used to determine the file path for a file in the current working
directory.

CaptureOutput
is a utility function. It is expected that it will be used by test
functions to capture, and then verify, the output of functions
under test.

And lastly,
IgnoreUnused
can be used to ignore unused entities.

### <a name="installation">Installation</a>

```bash
go get github.com/kjmjonline/go-minor/v0.1.0
```

This will install `go-minor` and its dependencies to your `go/pkg`
directory.

### <a id="funcs">Public Functions</a>

#### <a id="capture">CaptureOutput</a>

Captures, and returns, the `stdout` and `stderr` output of a function.

```go
import (
    "fmt"

    "github.com/kjmjonline/go-minor/v0.1.0"
)

func sayHello() {
    fmt.Print("Hello, stranger!")
}

func main() {
    greeting, err := minor.CaptureOutput(sayHello)
    // `greeting` will contain "Hello, stranger!" here
}
```

#### <a name="filepath">FilePathInCwd</a>

Returns the full path to the given _fileName_ in the current work directory
(i.e., equivalent to `cwd` in &#42;nix_ systems).

The current directory is the directory that this program was started from. 

```go
import (
    "fmt"

    "github.com/kjmjonline/go-minor/v0.1.0"
)

func main() {
    fileName := "blort.txt"

    var filePath string
    var err error
    if filePath, err = minor.FilePathInCwd(fileName); err != nil {
        panic(err)
    }
    fmt.Print(filePath)

    // If the current directory (the directory this program was started
    // from) is "/tmp" then the`filePath` returned would be:
    //   "/tmp/blort.txt".
}
```

#### <a name="ignore">IgnoreUnused</a>

This function circumvents Go errors that are raised when trying to compile
code that contains any unused constants, variables, amd/or functions.

To silence these errors pass the name of each such identifier to the
`IgnoreUnused()`
function. 

```go
import (
    "fmt"

    // We can ignore unused package import errors by preceding the
    // unused package with un underscore character:
    _ "os/path" 

    "github.com/kjmjonline/go-minor/v0.1.0"
)

const unusedConst = 42

type unusedType struct{}

var trulyUnusedVar string

func someUnusedFunc() string {
    return "this function is not used"
}

func main() {
    # We can ignore unused types like this:
    var _ unusedType

    # And here we ignore any unused constants, variables, and functions
    minor.IgnoreUnused(
        unusedConst,
        trulyUnusedVar,
        someUnusedFunc,
        )
}
```

#### <a name="setlog">SetGlobalZerologToFile</a>

This function sets up the global zerolog logger.

The file used for logging is appended to if it already exists, otherwise
it is created.

Logging is set up to create log entries with the current time timestamp,
and file name and line numbers where the log entries were created. The
timestamps use the RFC 3339 Nano time format, wbhich is a format that has
millisecond accuracy.

```go
import (
    "errors"
    "fmt"
    "log"

    "github.com/kjmjonline/go-minor/v01.0"
    "github.com/rs/zerolog/log"
    "github.com/rs/zerolog/pkgerrors"
)

func main() {
    err := minor.SetGlobalZerologToFile("mylog", zerolog.DebugLevel)
    if err != nil {
        log.Fatal(err)
    }
    log.Print("global zerolog has been created")

    err := errors.New("Whoa!")
     := errors.WithStack(err)
    log.Error().Stack().Err(wrapped).Msg("An error with a stacktrace")
}
```

### <a name="dependencies">Dependencies</a>

go-minor uses some packages that are not part of the Go standard library.
These libraries are automatically downloaded when go-minor is installed.
They are:
- github.com/rs/zerolog

What? That's it!

### <a name="incompat">Incompatibilities</a>

This package has no known incompatibilities with any other packages or
operating systems.

Please report any that you may find.

### <a name="bugs">Bugs and Limitations</a>

This package has no known bugs or limitations.

Please report any that you may find.

### <a name="thanks">Acknowledgements</a>

The CaptureOutput, FilePathInCwd, and IgnoreUnused functions were adopted
from other sources and changed slightly. Please report if any
acknowledgements ffor these functions should be added to this documentation.

### <a name="author">Author</a>

Justin Hanekom

Email: justinhanekom7@outlook.com

### <a name="copyright">Copyright and License</a>

Copyright (c) 2024 Justin Hanekom

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files
(the "Software"), to deal in the Software without restriction,
including without limitation the rights to use, copy, modify, merge,
publish, distribute, sublicense, and/or sell copies of the Software,
and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

[^1]: _miniscule_, really!
