# cl-phpunittest

A REALLY simple file for unit testing PHP.

## Overview

Download `cl-phpunittest.php` to a test directory.

In the test directory write code like this:

```php
<?php
include( 'cl-phpunittest.php' );
include( '../my-file-to-test.php' );

check( "<string with what I'm testing>", <value of what I hope to get> );
...

checkreport();
?>
```

If you are testing multiple file, try something like:

```php
<?php
include( 'cl-phpunittest.php' );

include( 'test-my-first-file.php' );
include( 'test-my-second-file.php' );
...

checkreport();
?>
```

Alternatively, you may be able to use `checkglobber()`.

It ain't pretty!!!

## checkglobber()

If you have lots of test files, your main test file can look
like the following and `checkglobber()` will glob() looking for test files
named `test-*.php` in the current directory and execute them:

```php
<?php
include( 'cl-phpunittest.php' );

checkglobber();

checkreport();
?>
```

__N.B. An issue with `checkglobber()`__ is that included files end up being in the
scope of the `checkglobber()` function.  This is generally not a probelm unless
an included file attempts to set global variables (without explicitly
identifying the variables as global).  To get around this problem, instead of:

```php
<?php
$my_global_variable = "Foo";
?>
```

do:

```php
<?php
global $my_global_variable;
$my_global_variable = "Foo";
?>
```

Alternatively, rather than calling `checkglober()`, use the following code in
its place:

```php
foreach( glob( 'test-*.php' ) as $test_file )
    include_once( $test_file );
```

## checkfeature( $heading, $func )

Groups together a bunch of related tests.  The `$heading` is printed out and
`$func` is run.

Example:

```php
checkfeature( "Example Tests", function() {
    check( ... );
    check( ... );
    ...
});
```

## checkheading( $heading )

Outputs a heading for a section of tests.

## check( $result, $expected )

The primary testing function.

The `$result` parameter is the result of an executed test.

The `$expected` parameter is the expected result.  It can be any PHP type.

Example:

```php
check( strlen( 'Test' ), 4 );
```

## check( $description, $result, $expected )

The 3 argument form of `check()` takes a leading test description argument.

The `$description` argument describes the test.

The `$result` parameter is result of the test. It is NOT `eval`ed by the
function.

The `$expected` parameter is the expected result.  It can be any PHP type.

Example:

```php
check( "strlen() should indicate 'Test' is 4 characters long", strlen( 'Test' ), 4 );
```

## checkeval( $statement, $expected )

A short form of the 3 argument form of `check()`.

The `$statement` parameter is a string containing a PHP statement to be
exceuted using PHP's `eval()`.

The `$expected` parameter is the expected result.  It can be any PHP type.

Example:

```php
checkeval( "strlen( 'Test' )", 4 );
```

## checkstr( $result, $expected )

A legacy version of `check( $result, $expected )`.

Example:

```php
checkstr( $output, "String 1" );
```

## checkrtrim( $result, $expected )

Like `check( $result, $expected )` but right trims the `$result` string before testing.

## checkrtrim( $description, $result, $expected )

Like `check( $description, $result, $expected )` but right trims the `$result` string before testing.

## checkevalrtrim( $statement, $expected )

Like `checkeval( $statement, $expected )` but right trims the string returned by evaluating `$statement`
before testing.

## checkstrtrim( $result, $expected )

Like `checkstr()` but right trims the input strings before testing.

## checkfailed( $reason )

Record that a test not directly implemented with the `check???()` functions
has failed.

Example:

```php
if( $a != $b || $c != $d )
    checkfailed( 'Complex test condition was not met' );
```

## checktodo( $task )

Record a TODO task in the output.

## checkreport()

Display tally of number of tests performed and number of fails.

## checkwrap( [ $wrap_length ] )

Specifies that future output messages should be wrapped at `$wrap_length`
characters.  Wrapping is disabled by default.  Using `checkwrap( 0 )` disables
wrapping if previously enabled.

If called with a parameter `checkwrap` returns the old wrap value.
If called without a parameter `checkwrap()` returns the current wrap value.

If `checkwrap()` is called in a `checkfeature()` function the original wrap
setting is restored once the function has completed.

## checkoutputclose()

Stop printing results to the logging file and close it.

## class CheckStopwatch

`CheckStopwatch` is a simple stopwatch class to help compare implementation
options.

The stopwatch starts running as soon as it is constructed.

You can read the elapsed time at any time by calling `ElapsedMilliseconds()`
method.  This does not stop the stopwatch running.

To stop the stopwatch call the `Stop()` method.  After calling `Stop()`,
repeated calls to `ElapsedMilliseconds()` will return the same time.

To re-start the stopwatch call the `Start()` method.

## Testing

cd to the `test` directory then run `php test-cl-phpunittest.php`.  The test
run will show failing tests.  This is intentional to detect failing test
scenarios.  The primary test is comparing the generated test output with
the reference test output (which is done in the final few lines of the test
file).
