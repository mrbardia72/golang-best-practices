## Go modules: Best Practices
[resource](https://utkarshmani1997.medium.com/go-modules-best-practices-3d32302f836a)
* Creating go.mod file:
  * Run `go mod init github.com/org/repo` inside the root folder of your project
* Adding dependencies to go.mod:
  * Run `go mod tidy` to download all the dependencies transitively imported by packages in your module
* Downloading dependencies:
  * Import a package in your project and run go build to build the binary and it will automatically pull the required dependencies for your package and put it as require directive in `go.mod` file and the checksum of the dependencies and their `go.mod` file into `go.sum` file
* Checksum database:
  * The go command uses this to verify the correctness of the dependencies from the checksum database `sum.golang.org` to ensure that the code you just downloaded wasn’t tampered with while building the binaries and hence you will get reproducible builds every time.
* Upgrading/Downgrading dependencies:
  * To `upgrade` a dependency to the latest version run `go get example.com/your/dependency `It will automatically update the version of the dependency in `go.mod` file. 
  * If you want to `update` all the dependencies of a dependency run `go get -u example.com/your/dependency`
  * go get also supports upgrades or downgrades to a specific version by adding version or commit or branch suffix to the package argument. For example:
    * go get example.com/your/dependency@v1.2.0
    * go get example.com/your/dependency@ksjdbcdbciuwb
    * go get example.com/your/dependency@development

## Given a version number MAJOR.MINOR.PATCH, increment the:
[resource](https://semver.org/)
1. MAJOR version when you make incompatible API changes
2. MINOR version when you add functionality in a backwards compatible manner
3. PATCH version when you make backwards compatible bug fixes

## go.mod file reference
[resource](https://go.dev/doc/modules/gomod-ref)
#### replace
Replaces the content of a module at a specific version (or all versions) with another module version or with a local directory. Go tools will use the replacement path when resolving the dependency.
* #### Syntax
  * `replace module-path [module-version] => replacement-path [replacement-version]`
  * module-path
    * The module path of the module to replace.
  * module-version
    * Optional. A specific version to replace. If this version number is omitted, all versions of the module are replaced with the content on the right side of the arrow.
  * replacement-path
    * The path at which Go should look for the required module. This can be a module path or a path to a directory on the file system local to the replacement module. If this is a module path, you must specify a replacement-version value. If this is a local path, you may not use a replacement-version value.
  * replacement-version
    * The version of the replacement module. The replacement version may only be specified if replacement-path is a module path (not a local directory).
      
* #### Examples
* Replacing with a fork of the module repository
  * In the following example, any version of example.com/othermodule is replaced with the specified fork of its code.
    ```
    require example.com/othermodule v1.2.3
    replace example.com/othermodule => example.com/myfork/othermodule v1.2.3-fixed
    ```
  * When you replace one module path with another, do not change import statements for packages in the module you’re replacing.
  * For more on using a forked copy of module code, see Requiring external module code from your own repository fork.
* Replacing with a different version number
  * The following example specifies that version v1.2.3 should be used instead of any other version of the module.
    ```
    require example.com/othermodule v1.2.2
    replace example.com/othermodule => example.com/othermodule v1.2.3
    ```
  * The following example replaces module version v1.2.5 with version v1.2.3 of the same module.
  * replace example.com/othermodule v1.2.5 => example.com/othermodule v1.2.3

  * Replacing with local code
  * The following example specifies that a local directory should be used as a replacement for all versions of the module.
  ```
  require example.com/othermodule v1.2.3
  replace example.com/othermodule => ../othermodule
  ```
  * The following example specifies that a local directory should be used as a replacement for v1.2.5 only.
  ```
  require example.com/othermodule v1.2.5
  replace example.com/othermodule v1.2.5 => ../othermodule
  ```
* #### Notes
* Use the replace directive to temporarily substitute a module path value with another value when you want Go to use the other path to find the module’s source. This has the effect of redirecting Go’s search for the module to the replacement’s location. You needn’t change package import paths to use the replacement path.
* Use the exclude and replace directives to control build-time dependency resolution when building the current module. These directives are ignored in modules that depend on the current module.
* The replace directive can be useful in situations such as the following:
    * You’re developing a new module whose code is not yet in the repository. You want to test with clients using a local version.
    * You’ve identified an issue with a dependency, have cloned the dependency’s repository, and you’re testing a fix with the local repository.
#### exclude
Specifies a module or module version to exclude from the current module’s dependency graph.

* #### Syntax
    * `exclude module-path module-version`
* module-path
  * The module path of the module to exclude.
* module-version
  *The specific version to exclude.
* #### Example
  * Exclude example.com/theirmodule version v1.3.0
    * exclude example.com/theirmodule v1.3.0
* #### Notes
    * Use the exclude directive to exclude a specific version of a module that is indirectly required but can’t be loaded for some reason. For example, you might use it to exclude a version of a module that has an invalid checksum.
    * Use the exclude and replace directives to control build-time dependency resolution when building the current module (the main module you’re building). These directives are ignored in modules that depend on the current module.
    * You can use the go mod edit command to exclude a module, as in the following example.
      * `go mod edit -exclude=example.com/theirmodule@v1.3.0`
#### retract
Indicates that a version or range of versions of the module defined by go.mod should not be depended upon. A retract directive is useful when a version was published prematurely or a severe problem was discovered after the version was published.
* #### Syntax
    * retract version // rationale
    * retract [version-low,version-high] // rationale
* version
  * A single version to retract.
* version-low
  * Lower bound of a range of versions to retract.
* version-high
  * Upper bound of a range of versions to retract. Both version-low and version-high are included in the range.
* rationale
  Optional comment explaining the retraction. May be shown in messages to the user.
  * #### Example
    * Retracting a single version `retract v1.1.0 // Published accidentally.`
    * Retracting a range of versions `retract [v1.0.0,v1.0.5] // Build broken on some platforms.`

* #### Notes
* Use the retract directive to indicate that a previous version of your module should not be used. Users will not automatically upgrade to a retracted version with go get, go mod tidy, or other commands. Users will not see a retracted version as an available update with go list -m -u.
* Retracted versions should remain available so users that already depend on them are able to build their packages. Even if a retracted version is deleted from the source repository, it may remain available on mirrors such as proxy.golang.org. Users that depend on retracted versions may be notified when they run go get or go list -m -u on related modules.
* The go command discovers retracted versions by reading retract directives in the go.mod file in the latest version of a module. The latest version is, in order of precedence:
  * Its highest release version, if any
  * Its highest pre-release version, if any
  * A pseudo-version for the tip of the repository’s default branch.