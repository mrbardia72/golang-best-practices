## Optimize Golang Code For Better Performance
#### Syntax and Folder Structure
1. Define data types of variables
2. Use comments
3. Maintain naming conventions
* You can keep the following things in mind:
  * Avoid using underscores.
  * Use Mixed Case Capital acronyms
  * Short local variables or single letters for loop argument or index
  * Modularization
  * Splitting up projects

#### Package
1. Take care of documentation
2. Managing multiple files in the same package
* Should you break a specific package into numerous files?
   * Prevent long files: Standard library’s net/http package comprises 15734 lines in 47 files.
   * Divide code and tests: net/http/cookie_test.go and net/http/cookie.go, both are parts of the http package. Ideally, test code is only compiled at test time.
   * Divided package documentation: When the package has more than one file, it is an agreement to create a doc.go comprising the package documentation.
   * Pack your packages properly

#### Code Practices to maintain readability
* Avoid Nesting
* Do not repeat unnecessary code
* Prioritize essential code

#### Handling Go errors
1. Using multiple return values
2. Wrap Golang errors
* For wrapping errors, fmt.Errorf provides %w verb to inspect and unwrap errors. Functions like
  * errors.Unwrap: For inspecting and exposing the underlying errors in the code.
  * errors.Is: For comparing every error value of the error chain against the sentinel value. The Is method implemented on the error is used to post the error itself as the sentinel value in case we don’t have a sentinel value.
  * errors.As: The function is used to cast a particular error type. For that, it looks for the very first error encountered in the error chain and sets that specific error as a sentinel value, and returns true.

#### State Management With Goroutines
* Avoid Goroutine Leaks
  * The goroutine is blocked on chan write
  * The goroutine carries a source to the chan
  * The chan will never be accumulated with garbage.

#### Golang CI/CD
1. Use Go Modules
2. Use Artifactory Repository Layouts

#### Testing in Go App
1. Keep your tests in a different package
2. A different file for internal tests
3. Write table-driven tests