## Let’s have fun testing your shell scripts!

[ShellSpec](https://github.com/shellspec/shellspec) is a **full-featured BDD unit testing framework** for dash, bash, ksh, zsh and **all POSIX shells** that provides first-class features such as **code coverage**, **mocking**, **parameterized test**, **parallel execution** and more. It was developed as a dev/test tool for cross-platform shell scripts and shell script libraries. ShellSpec is a new modern testing framework released in 2019, but it’s already stable enough. With lots of practical CLI features and simple yet powerful syntax, it provides you with a fun shell script test environment.

-   [Why I created ShellSpec](https://shellspec.info/why.html)
-   [ShellSpec vs shUnit2 vs Bats-core](https://shellspec.info/comparison.html)

## Get started!

Try **[Online Demo](https://shellspec.info/demo)** on your browser.

## Overview

### Unit testing framework

ShellSpec is a **unit testing framework** designed to facilitate testing shell scripts (especially shell functions). Use the Behavior-Driven Development (BDD) style syntax to accelerate development with Test-driven development (TDD). It can test small scripts with one file to large scripts with multiple files. Of course, it can be used for various purposes such as functional test of external command and system test.

### Compatible with all POSIX shells

ShellSpec is implemented using POSIX-compliant features and works with all POSIX shells, not just Bash. For example, it works with POSIX-compliant shell **dash**, ancient **bash 2.03**, the first POSIX shell **ksh88**, **[busybox-w32](https://frippery.org/busybox/)** ported natively for Windows, etc. It helps you developing shell scripts that work with multiple POSIX shells and environment.

-   `bash` _\>=2.03_, `bosh/pbosh` _\>=2018/10/07_, `posh` _\>=0.3.14_, `yash` _\>=2.29_, `zsh` _\>=3.1.9_
-   `dash` _\>=0.5.4_, `busybox ash` _\>=1.20.0_, `busybox-w32`, `GWSH` _\>=20190627_
-   `ksh88`, `ksh93` _\>=93s_, `ksh2020`, `mksh/lksh` _\>=R28_, `pdksh` _\>=5.2.14_
-   `FreeBSD sh`, `NetBSD sh`, `OpenBSD ksh`, `loksh`, `oksh`

### Minimal dependency

Most features are implemented in pure shell scripts and use only minimal POSIX-compliant commands. As a result, it works even in restricted environments such as tiny Docker images and embedded systems.

_Requirements_: `cat`, `date`, `env`, `ls`, `mkdir`, `od` (or `hexdump`), `rm`, `sleep`, `sort`, `time`

### Tested on numerous real shells and OSs

The latest shells have been tested with CI ([TravisCI](https://travis-ci.com/shellspec/shellspec) / [CirrusCI](https://cirrus-ci.com/github/shellspec/shellspec)). In addition, the shell used by Debian in the past has been tested using Docker. The oldest Debian is 2.2!

The operating systems that have been confirmed to work are Linux, macOS, Windows, BSD, Solaris and AIX.

See [tested version details](https://github.com/shellspec/shellspec/blob/master/docs/shells.md)

### Testing a single file script

Shell scripts often consist of a single file. ShellSpec also supports testing functions and mocking of contained in such scripts. (needs a few script modification.)

### DSL syntax

Writes test code in its own DSL. It is compatible with shell scripts and allows you to embed shell script code. This DSL close to natural language doesn’t just provide readability. One of the purposes is to avoid the [pitfalls](http://mywiki.wooledge.org/BashPitfalls) for beginner of shell script developer. And by absorbing the differences between the shells, you can write reliable tests for support multiple shells with a single test code.

The following are some of the things that DSL does.

-   Nestable block with scope and mocking
-   Before/After hooks, Data helper, Parameterized test, etc
-   Directives to improve embedded shell scripts
-   Display line number without relying on `$LINENO`
-   Absorption of differences in shell options
-   Numerous workarounds for shell bugs

```
Describe 'sample'
  Describe 'bc command'
    add() { echo "$1 + $2" | bc; }

    It 'performs addition'
      When call add 2 3
      The output should eq 5
    End
  End

  Describe 'implemented by shell function'
    Include ./mylib.sh # add() function defined

    It 'performs addition'
      When call add 2 3
      The output should eq 5
    End
  End
End
```

### Mocking

There are two mock features, a fast function-based mock for shell functions and a flexible command-based mock for external commands. The mock is released automatically when exit the block, therefore avoiding the mistake of releasing the mock. This is one of the features that many other frameworks don’t have, and it makes using mocks simpler.

```
unixtime() { date +%s; }

Describe 'function-based mock'
  date() {
    echo 1546268400
  }

  It 'is just define a function'
    When call unixtime
    The output should eq 1546268400
  End
End

Describe 'command-based mock'
  Mock date
    echo 1546268400
  End

  It 'creates executable command on the preferred path'
    When call unixtime
    The output should eq 1546268400
  End
End
```

### Data helper

Data Helper can easily provide stdin data. It doesn’t break the indent unlike here documents.

```
Describe 'Data helper'
  Data # Use Data:expand instead if you want to expand variables.
    #|item1 123
    #|item2 456
    #|item3 789
  End

  It 'provides stdin data'
    When call awk '{total+=$2} END{print total}'
    The output should eq 1368
  End
End
```

### Parameterized test

Parameterized tests (aka Data-Driven test) provide the ability to run the same test with different parameters. The syntax is surprisingly simple and you can easily rewrite regular tests into parameterized tests. It can also be defined by matrix definition or dynamically definition by shell script code.

```
Describe 'parameters'
  Parameters
    "#1" 1 2 3
    "#2" 4 5 9
  End

  It "performs a parameterized test ($1)"
    When call echo "$(($2 + $3))"
    The output should eq "$4"
  End
End

Describe 'matrix parameters'
  Parameters:matrix
    foo bar
    1 2
  End

  It "generates matrix parameters ($1 : $2)"
    When call touch "name_$1_$2"
    The file "name_$1_$2" should be exists
  End
End

Describe 'dynamic parameters'
  Parameters:dynamic
    for i in 1 2 3; do
      %data "#$i" "$i" "$(($i*2))" "$(($i + $i*2))"
    done
  End

  It "generates parameter by shell script code ($1)"
    When call echo "$(($2 + $3))"
    The output should eq "$4"
  End
End
```

### Directives

Directives are convenient instructions that you can use in embedded shell scripts. `%=` (`%putsn`) is a portable version of `echo` that absorbs the differences between shells. `%text` can output multiple lines of text without breaking the indentation. These solve problem with shell script code in writing test code.

```
Describe 'directives'
  It 'makes embedded shell script easier'
    output() {
      %= "foo"
      %= "bar"
      %= "baz"
    }

    result() {
      %text
      #|foo
      #|bar
      #|baz
    }

    When call output
    The output should eq "$(result)"
  End
End
```

### Sandbox mode

Make empty the `PATH` environment variable (except internally used path) and prevent execution of external commands. This will prevent mistakes such as executing dangerous commands unintentionally during development. This mode assumes use of mocks, but you can also execute real commands if you need to.

```
Describe 'sandbox mode'
  sed() { # External command cannot be executed without mock
    @sed "$@" # Run real command
  }

  It 'cannot run external commands without mocking'
    Data "foo"
    When call sed 's/f/F/g'
    The output should eq "Foo"
  End
End
```

_Note: The `@sed` command above is a “support command” which generated by `shellspec --gen-bin @sed`._

### Quick mode

Enabling quick mode allows you to rerun only the failed tests quickly. The test to be re-executed is automatically determined. There is no hassles.

The combination of quick mode and pending is an efficient way to perform Test-Driven Development (TDD). Tests that are set on pending will be rerun on both failures and successes. This means that you can do a TDD cycle, RED -> GREEN -> REFACTOR.

### Parallel execution

Execution speed is also important, and it runs at a comfortable speed that you cannot think of as a shell script. In addition, using parallel execution can reduce test execution time by executing multiple tests in parallel.

### Random execution

You can find problems that depend on the test order by randomly changing the test execution order. You can also run the tests in the same order as last time by specifying a seed.

### Execution tracing

Suppresses unnecessary output and provides a truly useful xtrace features. It is useful for debugging.

### Profiler

You can use the profiler to find and improve slow tests.

### Modern reporting

Test results are displayed in modern, colorful and easy-to-read reports, such as simple dot style and documentation style. Failed tests are displayed in detail with line numbers so you can quickly identify the problem test.

#### Progress (dot) formatter

#### Documentation formatter

### Generator

Test results can be output to a file separately from the report display function on the screen. The same formatter as the reporting can be used, and it can be easily linked with CI using TAP ([Test Anything Protocol](https://testanything.org/)) format and jUnit XML format. You can also output multiple formats in one test run.

#### TAP formatter

```
1..8
ok 1 - calc.sh add() 1 + 1 = 2
ok 2 - calc.sh add() 1 + 10 = 11
ok 3 - calc.sh sub() 1 - 1 = 0
ok 4 - calc.sh sub() 1 - 10 = -9
ok 5 - calc.sh mul() 1 * 1 = 1
ok 6 - calc.sh mul() 1 * 10 = 10
ok 7 - calc.sh div() 1 / 1 = 1
not ok 8 - calc.sh div() 1 / 10 = 0.1 # FAILED
```

#### jUnit XML formatter

```
<?xml version="1.0" encoding="UTF-8"?>
<testsuites tests="8" failures="1" time="0.31" name="">
  <testsuite id="0" tests="8" failures="1" skipped="0" name="spec/calc_spec.sh" hostname="localhost"
 timestamp="2019-07-06T13:39:59">
    <testcase classname="spec/calc_spec.sh" name="calc.sh add() 1 + 1 = 2"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh add() 1 + 10 = 11"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh sub() 1 - 1 = 0"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh sub() 1 - 10 = -9"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh mul() 1 * 1 = 1"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh mul() 1 * 10 = 10"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh div() 1 / 1 = 1"></testcase>
    <testcase classname="spec/calc_spec.sh" name="calc.sh div() 1 / 10 = 0.1">
      <failure message="The output should equal 0.1">expected: &quot;0.1&quot;
     got: 0

# spec/calc_spec.sh:48</failure>
    </testcase>
  </testsuite>
</testsuites>
```

### Code coverage

Easily measure code coverage with [Kcov](https://github.com/SimonKagstrom/kcov) integration. Coverage reports are output as [HTML](https://circleci.com/api/v1.1/project/github/shellspec/shellspec/latest/artifacts/0/coverage/index.html?branch=master) files and compatible formats with coverage measurement services such as [Coveralls](https://coveralls.io/github/shellspec/shellspec?branch=master), [Code Climate](https://codeclimate.com/github/shellspec/shellspec) and [Codecov](https://codecov.io/gh/shellspec/shellspec).

_Note: This feature is only available with Bash, Ksh, Zsh and Kcov is required._

[![Coverage report](https://shellspec.info/coverage.png)](https://circleci.com/api/v1.1/project/github/shellspec/shellspec/latest/artifacts/0/coverage/index.html?branch=master)

### Run tests inside a Docker container (experimental)

By executing the test with the specified Docker image, you can perform the test in the same environment without worrying about the influence on the host environment.

_Note: Docker required._

## Projects using ShellSpec

-   [jenkins-x/terraform-google-jx](https://github.com/jenkins-x/terraform-google-jx) - A Terraform module for creating Jenkins X infrastructure on Google Cloud
-   [snyk/snyk](https://github.com/snyk/snyk/tree/master/test/smoke) - CLI and build-time tool to find & fix known vulnerabilities in open-source dependencies

-   Subprojects
    -   [ShellMetrics](https://github.com/shellspec/shellmetrics) - Cyclomatic Complexity Analyzer for shell scripts
    -   [ShellBench](https://github.com/shellspec/shellbench) - A benchmark utility for POSIX shell comparison
-   Related projects
    -   [getoptions](https://github.com/ko1nksm/getoptions) - An elegant option parser and generator for shell scripts
    -   [readlinkf](https://github.com/ko1nksm/readlinkf) - readlink -f implementation for shell scripts