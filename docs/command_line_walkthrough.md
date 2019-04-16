# Command Line Walkthrough
This tutorial will show you how to perform the FizzBuzz code kata using Python 3, Behave, and PyHamcrest
on the command line.  This example uses MacOS in a terminal window.

## 1. Create project directory
```bash
$ mkdir fizzbuzz_behave_00N # where 00N is a number to separate attempts
$ cd fizzbuzz_behave_00N
```

## 2. Setup venv
```bash
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org --upgrade pip
$ pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org behave PyHamcrest
```

## 3. Create directory structure
```bash
$ mkdir -p features/steps lib
```

## 4. Populate kata features file
```bash
$ curl -sSL https://gist.githubusercontent.com/ssmythe/74fbd90c355f7481bf448fb67c5903f8/raw/79991d039267838dd38bcfb8a10fb857d7ae741e/fizzbuzz.feature > features/fizzbuzz.feature
```

## 5. Run behave - fail because steps not yet created
```bash
$ behave
```

## 6. Have behave generate steps
```bash
$ behave --no-color 1>/dev/null 2>features/steps/fizzbuzz_steps.py
```
* In features/steps/fizzbuzz_steps.py, replace
```python
You can implement step definitions for undefined steps with these snippets:
```
...with...
```python
from behave import *
from hamcrest import *
from lib.fizzbuzz import *
```
This will import the behave BDD features and hamcrest assertion library.
This will also import the FizzBuzz class file we'll be implementing.

## 7. Run behave - fail because FizzBuzz class not implemented
```bash
$ behave
```

## 8. Create Empty FizzBuzz Class
* Create lib/fizzbuzz.py that contains:
```python
class FizzBuzz:
    """
    Empty class
    """
```

## 9. Run behave - fail because Given the number "1" step not implemented
```bash
$ behave
```

### 10. Implement Given the number "1" step
We want to pass in the number between quotes and save it in the behave context object.

We do this by replacing the "1" with "{number:d}", where "{number}" uses the Parse library to parse
strings using a specification based on the Python format() syntax, and ":d" represents a decimal
integer.  The value parsed is an int.

We map that number value into the step method.

Then, we save the integer number value into the behave context object for use in then next step.

* Update features/steps/fizzbuzz_steps.py to contain:
```python
from behave import *
from hamcrest import *
from lib.fizzbuzz import *


@given(u'the number "{number:d}"')
def step_impl(context, number):
    context.number = number


@when(u'we run the number through do_fizzbuzz')
def step_impl(context):
    raise NotImplementedError(u'STEP: When we run the number through do_fizzbuzz')


@then(u'we should get back "1"')
def step_impl(context):
    raise NotImplementedError(u'STEP: Then we should get back "1"')
```

## 11. Run behave - fail because When we run the number through do_fizzbuzz not implemented
```bash
$ behave
```

### 12. Implement When we run the number through do_fizzbuzz step
We want to call our do_fizzbuzz method with number we saved off in the previous step.

First we instantiate a FizzBuzz object, call the do_fizzbuzz method on the object, passing it the
number we saved off in the previous step, and store that result into the behave context object.

* Update features/steps/fizzbuzz_steps.py to contain:
```python
from behave import *
from hamcrest import *
from lib.fizzbuzz import *

@given(u'the number "{number:d}"')
def step_impl(context, number):
    context.number = number

@when(u'we run the number through do_fizzbuzz')
def step_impl(context):
    fb = FizzBuzz()
    context.results = fb.do_fizzbuzz(context.number)

@then(u'we should get back "{output}"')
def step_impl(context, output):
    assert_that(context.results, equal_to(output))
```


## 13. Run behave - fail because the do_fizzbuzz method not implemented in the FizzBuzz class
```bash
$ behave
```

## 14. Implement the do_fizzbuzz method in the FizzBuzz class
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    def do_fizzbuzz(self, number):
        return str(number)
```

## 15. Run behave - fail because Then we should get back "1" step not implemented
```bash
$ behave
```

## 16. Implement Then we should get back "1" step
We want to check the expected output from the feature to the results we got from the last step.

We do this by replacing the "1" with "{output}", where "{output}" uses the Parse library to parse
strings using a specification based on the Python format() syntax, where the default type is string.
 The value parsed is an str.

We map that output value into the step method.

Then, we use a hamcrest assert_that the results from the previous step is equal_to the expected output.

* Update features/steps/fizzbuzz_steps.py to contain:
```python
from behave import *
from hamcrest import *
from lib.fizzbuzz import *

@given(u'the number "{number:d}"')
def step_impl(context, number):
    context.number = number

@when(u'we run the number through do_fizzbuzz')
def step_impl(context):
    fb = FizzBuzz()
    context.results = fb.do_fizzbuzz(context.number)

@then(u'we should get back "{output}"')
def step_impl(context, output):
    assert_that(context.results, equal_to(output))
```

## 17. Run behave - pass
```bash
$ behave
```

Great job!  Our first test has passed.  Now onto to the second scenario.

## 18. Uncomment the second scenario for "2"
* Uncomment the first scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Second scenario
    Given the number "2"
    When we run the number through do_fizzbuzz
    Then we should get back "2"
```

## 19. Run behave - pass
```bash
$ behave
```

Why did this pass without code changes?  Because the FizzBuzz requirements only deal with values that
are multiples of 3 and 5.

## 20. Uncomment the third scenario for "3"
* Uncomment the third scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Third scenario
    Given the number "3"
    When we run the number through do_fizzbuzz
    Then we should get back "Fizz"
```

## 21. Run behave - fail because the multiple of 3 logic not implemented in the do_fizzbuzz method
```bash
$ behave
```

You'll notice that behave reports expecting "Fizz" but got "3".  Let's add code to deal with this.

## 22. Implement the multiple of 3 logic in do_fizzbuzz 
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    def do_fizzbuzz(self, number):
        if number % 3 == 0:
            return "Fizz"
        return str(number)
```

This simple test to use the modulo operator ("%") returns a 0 if there's no remainder.  So by testing
if dividing the number by 3 has a 0 remainder, then we return the string "Fizz".

## 23. Run behave - pass
```bash
$ behave
```

## 24. Uncomment the fourth scenario for "5"
* Uncomment the fourth scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Fourth scenario
    Given the number "5"
    When we run the number through do_fizzbuzz
    Then we should get back "Buzz"
```

## 25. Run behave - fail because the multiple of 5 logic not implemented in the do_fizzbuzz method
```bash
$ behave
```

You'll notice that behave reports expecting "Buzz" but got "5".  Let's add code to deal with this.

## 26. Implement the multiple of 3 logic in do_fizzbuzz 
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    def do_fizzbuzz(self, number):
        if number % 5 == 0:
            return "Buzz"
        if number % 3 == 0:
            return "Fizz"
        return str(number)
```

This simple test to use the modulo operator ("%") returns a 0 if there's no remainder.  So by testing
if dividing the number by 5 has a 0 remainder, then we return the string "Buzz".

## 27. Run behave - pass
```bash
$ behave
```

## 28. Uncomment the fifth scenario for "15"
* Uncomment the fifth scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Fifth scenario
    Given the number "15"
    When we run the number through do_fizzbuzz
    Then we should get back "FizzBuzz"
```

## 29. Run behave - fail because the multiple of 5 logic not implemented in the do_fizzbuzz method
```bash
$ behave
```

You'll notice that behave reports expecting "FizzBuzz" but got "Buzz".  Let's add code to deal with this.

## 30. Implement the multiple of 15 logic in do_fizzbuzz 
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    def do_fizzbuzz(self, number):
        if number % 3 == 0 and number % 5 == 0:
            return "FizzBuzz"
        if number % 5 == 0:
            return "Buzz"
        if number % 3 == 0:
            return "Fizz"
        return str(number)
```

This simple test to use the modulo operator ("%") returns a 0 if there's no remainder.  So by testing
if dividing the number by 3 has a 0 remainder and dividing the number by 5 has a 0 remainder, then we
return the string "FizzBuzz".

## 31. Run behave - pass
```bash
$ behave
```

## 32. Refactor to simplify logic
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    def do_fizzbuzz(self, number):
        results = ""
        if number % 3 == 0:
            results += "Fizz"
        if number % 5 == 0:
            results += "Buzz"
        return results or str(number)
```

Python has a very nice behavior with the "or" operator and strings, where the expressions will give "results"
if it is not empty (an empty string returns False, a non-empty string returns True), otherwise, return
"str(number)".

## 33. Run behave - pass
```bash
$ behave
```

## 34. Write the program to FizzBuzz the numbers 1 to 100
Now that we have the core logic in our FizzBuzz class, let's write a simple wrapper script that uses
this class to iterate through the numbers 1 to 100, running it through the do_fizzbuzz method.

* Create fizzy.py to contain:
```python
#!/usr/bin/env python3

from lib.fizzbuzz import *

fb = FizzBuzz()

for i in range(1, 101):
    print(fb.do_fizzbuzz(i))
```

Make the script executable:
```bash
$ chmod +x ./fizzy.py
```

## 35. Run the script and see how it meets the FizzBuzz kata requirements!
```bash
$ ./fizzy.py
```

## 36. Stage 2 - new requirements and the sixth scenario - number contains a "3"
Now let's refactor the code to include the new requirements

* Add the following to the end of the Feature description in features/fizzbuzz.feature
```text
Stage 2 - new requirements

* A number is fizz if it is divisible by 3 or if it has a 3 in it
```

...and...

```gherkin
  Scenario: Sixth scenario
    Given the number "13"
    When we run the number through do_fizzbuzz
    Then we should get back "Fizz"
```
We're using 13, because it's a prime number and it has a 3 in it.

## 37. Run behave - fail because do_fizzbuzz doesn't have contains a "3" implemented
```bash
$ behave
```

## 38. Implement the number contains a "3" logic in do_fizzbuzz 
At first, we can just use compound logic to test for both the remainder and if the number contains
the number "3".  Python has a handy "string" in string feature, so we'll use that:

* Update lib/fizzbuzz.py to contain:
```python
    def do_fizzbuzz(self, number):
        results = ""
        if number % 3 == 0 or "3" in str(number):
            results += "Fizz"
        if number % 5 == 0:
            results += "Buzz"
        return results or str(number)
```

## 39. Run behave - pass
```bash
$ behave
```

## 40. Stage 2 - seventh scenario - number contains a "5"
Let's add a seventh require about number contains a "5"

* Add the following to the end of the Feature description in features/fizzbuzz.feature
```text
Stage 2 - new requirements

* A number is fizz if it is divisible by 3 or if it has a 3 in it
* A number is buzz if it is divisible by 5 or if it has a 5 in it
```

...and...

```gherkin
  Scenario: Seventh scenario
    Given the number "59"
    When we run the number through do_fizzbuzz
    Then we should get back "Buzz"
```

We're using 59, because it's a prime number and it has a 5 in it.

## 41. Run behave - fail because do_fizzbuzz doesn't have contains a "5" implemented
```bash
$ behave
```

## 42. Implement the number contains a "5" logic in do_fizzbuzz 
At first, we can just use compound logic to test for both the remainder and if the number contains
the number "5".  Python has a handy "string" in string feature, so we'll use that:

* Update lib/fizzbuzz.py to contain:
```python
    def do_fizzbuzz(self, number):
        results = ""
        if number % 3 == 0 or "3" in str(number):
            results += "Fizz"
        if number % 5 == 0 or "5" in str(number):
            results += "Buzz"
        return results or str(number)
```

## 43. Run behave - pass
```bash
$ behave
```

## 44. Run fizzy.py and see how the output has changed for the Stage 2 requirements
```bash
$ ./fizzy.py
```

----

Now that you've gone through this walkthrough, try it on your own from scratch!
  
See how far you can get without referencing the walkthrough!
  
Enjoy!
