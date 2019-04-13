# FizzBuzz code kata
This tutorial will show you how to perform the FizzBuzz code kata using Python 3, Behave, and PyHamcrest
using the JetBrains PyCharm IDE.

## 1. Create project
* Create project with name "fizzbuzz_behave_00N"
(where 00N is a number to separate attempts)

## 2. Setup BDD
* In PyCharm > Preferences... > Languages & Frameworks > BDD
* Choose "behave"

## 3. Setup venv
* In a PyCharm terminal window, type
```
$ pip install --upgrade pip
$ pip install behave PyHamcrest
```

## 4. Create directory structure
In the PyCharm terminal window from above:
```bash
$ mkdir -p features/steps lib
```

## 5. Populate kata features file
In the PyCharm terminal window from above:
```bash
$ curl -sSL https://gist.githubusercontent.com/ssmythe/74fbd90c355f7481bf448fb67c5903f8/raw/79991d039267838dd38bcfb8a10fb857d7ae741e/fizzbuzz.feature > features/fizzbuzz.feature
```

## 6. Set behave to auto-run on code changes
* Right-click on "features" and choose Run 'features'
* Click Toggle auto-test icon to continuously run tests

## 7. Review auto-run output - Tests failed - Undefined because steps not yet created
Auto-run will show the test hierarchy on the left pane and output on the right.
```text
Undefined
```

## 8. Have PyCharm generate steps
* In features/fizzbuzz.feature, left-click on yellow the number "1" until a light bulb appears.
* Click on the light bulb and choose "Create all step definitions..." and click "OK"
* Review features/steps/fizzbuzz.py

## 9. Change the top part of features/steps/fizzbuzz.py from:
```python
from behave import *

use_step_matcher("re")
```
...to...
```python
from behave import *
from hamcrest import *
from lib.fizzbuzz import *
```
This will import the behave BDD features and hamcrest assertion library.
This will also import the FizzBuzz class file we'll be implementing.

## 10. Review auto-run output - Tests failed - FizzBuzz class not implemented
```text
ModuleNotFoundError: No module named 'lib.fizzbuzz'
```

## 11. Create Empty FizzBuzz Class
* Right-click on lib directory and choose New > Python File.  In "Name", enter "fizzbuzz"
and click "OK"
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    """
    Empty class
    """
```

## 12. Review auto-run output - Tests failed - Given the number "1" step not implemented
```text
NotImplementedError: STEP: Given the number "1"
```

## 13. Implement Given the number "1" step
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

## 14 Review auto-run output - Tests failed -  When we run the number through do_fizzbuzz not implemented
```text
NotImplementedError: STEP: When we run the number through do_fizzbuzz
```

## 15. Implement When we run the number through do_fizzbuzz step
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

## 16. Review auto-run output - Tests failed - do_fizzbuzz method not implemented in the FizzBuzz class
```text
AttributeError: 'FizzBuzz' object has no attribute 'do_fizzbuzz'
```

## 17. Implement the do_fizzbuzz method in the FizzBuzz class
* Update lib/fizzbuzz.py to contain:
```python
class FizzBuzz:
    def do_fizzbuzz(self, number):
        return str(number)
```

## 18. Review auto-run output - Tests failed - Then we should get back "1" step not implemented
```text
NotImplementedError: STEP: Then we should get back "1"
```

## 19. Implement Then we should get back "1" step
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

## 20. Review auto-run output - Tests pass
```text
Process finished with exit code 0
```

Great job!  Our first test has passed.  Now onto to the second scenario.

## 21. Uncomment the second scenario for "2"
* Uncomment the first scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Second scenario
    Given the number "2"
    When we run the number through do_fizzbuzz
    Then we should get back "2"
```

## 22. Review auto-run output - Tests pass
```bash
Process finished with exit code 0
```

Why did this pass without code changes?  Because the FizzBuzz requirements only deal with values that
are multiples of 3 and 5.

## 23. Uncomment the third scenario for "3"
* Uncomment the third scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Third scenario
    Given the number "3"
    When we run the number through do_fizzbuzz
    Then we should get back "Fizz"
```

## 24. Review auto-run output - Tests failed - the multiple of 3 logic not implemented in the do_fizzbuzz method
```text
Assertion Failed: 
Expected: 'Fizz'
     but: was '3'
```

You'll notice that behave reports expecting "Fizz" but got "3".  Let's add code to deal with this.

## 25. Implement the multiple of 3 logic in do_fizzbuzz 
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

## 26. Review auto-run output - Tests  pass
```text
Process finished with exit code 0
```

## 27. Uncomment the fourth scenario for "5"
* Uncomment the fourth scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Fourth scenario
    Given the number "5"
    When we run the number through do_fizzbuzz
    Then we should get back "Buzz"
```

## 28. Review auto-run output - Tests failed - the multiple of 5 logic not implemented in the do_fizzbuzz method
```text
Assertion Failed: 
Expected: 'Buzz'
     but: was '5'
```

You'll notice that behave reports expecting "Buzz" but got "5".  Let's add code to deal with this.

## 29. Implement the multiple of 3 logic in do_fizzbuzz 
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

## 30. Review auto-run output - Tests pass
```text
Process finished with exit code 0
```

## 31. Uncomment the fifth scenario for "15"
* Uncomment the fifth scenario in features/fizzbuzz.feature to look like:
```gherkin
  Scenario: Fifth scenario
    Given the number "15"
    When we run the number through do_fizzbuzz
    Then we should get back "FizzBuzz"
```

## 32. Review auto-run output - Tests failed - the multiple of 5 logic not implemented in the do_fizzbuzz method
```text
Assertion Failed: 
Expected: 'FizzBuzz'
     but: was 'Buzz'
```

You'll notice that behave reports expecting "FizzBuzz" but got "Buzz".  Let's add code to deal with this.

## 33. Implement the multiple of 15 logic in do_fizzbuzz 
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

## 34. Review auto-run output - Tests pass
```text
Process finished with exit code 0
```

## 35. Write the program to FizzBuzz the numbers 1 to 100
Now that we have the core logic in our FizzBuzz class, let's write a simple wrapper script that uses
this class to iterate through the numbers 1 to 100, running it through the do_fizzbuzz method.

* Right-click on root directory and choose New > Python File.  In "Name", enter "fizzy"
and click "OK"
* Update fizzy.py to contain:
```python
#!/usr/bin/env python3

from lib.fizzbuzz import *

fb = FizzBuzz()

for i in range(1, 101):
    print(fb.do_fizzbuzz(i))
```

## 36. Right-click on fizzy.py, and choose "Run fizzy.py" and see how it meets the FizzBuzz kata requirements!
```text
1
2
Fizz
4
Buzz
Fizz
...<snip>...
Buzz
Fizz
97
98
Fizz
Buzz
```

## 37. Stage 2 - new requirements and the sixth scenario - number contains a "3"
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

## 38. Run behave tests on features
* Right-click on "features" and choose Run 'features'
* Click Toggle auto-test icon off and then back on to continuously run tests

## 39. Review auto-run output - Tests fail - do_fizzbuzz doesn't have contains a "3" implemented
```text
Assertion Failed: 
Expected: 'Fizz'
     but: was '13'
```

## 40. Implement the number contains a "3" logic in do_fizzbuzz 
At first, we can just use compound logic to test for both the remainder and if the number contains
the number "3".  Python has a handy "string" in string feature, so we'll use that:

* Update lib/fizzbuzz.py to contain:
```python
    def do_fizzbuzz(self, number):
        if number % 3 == 0 and number % 5 == 0:
            return "FizzBuzz"
        if number % 5 == 0:
            return "Buzz"
        if number % 3 == 0 or "3" in str(number):
            return "Fizz"
        return str(number)
```

## 41. Review auto-run output - Tests pass
```text
Process finished with exit code 0
```

## 42. Stage 2 - seventh scenario - number contains a "5"
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

## 43. Review auto-run output - Tests fail - do_fizzbuzz doesn't have contains a "5" implemented
```text
Assertion Failed: 
Expected: 'Buzz'
     but: was '59'
```

## 44. Implement the number contains a "5" logic in do_fizzbuzz 
At first, we can just use compound logic to test for both the remainder and if the number contains
the number "5".  Python has a handy "string" in string feature, so we'll use that:

* Update lib/fizzbuzz.py to contain:
```python
    def do_fizzbuzz(self, number):
        if number % 3 == 0 and number % 5 == 0:
            return "FizzBuzz"
        if number % 5 == 0 or "5" in str(number):
            return "Buzz"
        if number % 3 == 0 or "3" in str(number):
            return "Fizz"
        return str(number)
```

## 45. Review auto-run output - Tests pass
```text
Process finished with exit code 0
```

## 46. Right-click on fizzy.py, and choose "Run fizzy.py" and see how it meets the FizzBuzz kata requirements!
```text
1
2
Fizz
4
Buzz
Fizz
...<snip>...
Buzz
Fizz
97
98
Fizz
Buzz
```

----

Now that you've gone through this walkthrough, try it on your own from scratch!
  
See how far you can get without referencing the walkthrough!
  
Enjoy!
