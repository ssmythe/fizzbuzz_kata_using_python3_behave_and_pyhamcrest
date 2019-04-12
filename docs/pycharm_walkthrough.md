# FizzBuzz code kata
This tutorial will show you how to perform the FizzBuzz code kata using Python 3, Behave, and PyHamcrest
using the JetBrains editor Pycharm.

## Create project
1. Create project with name "fizzbuzz_behave_00N"
(where 00N is a number to separate attempts)

## Setup BDD
1. In PyCharm > Preferences... > Languages & Frameworks > BDD
2. Choose "behave"

## Setup venv
1. In PyCharm > Preferences... > Project Interpreter
2. Click the gear icon to the right of the interpreter path
3. Choose Add...
4. Click OK
5. Click "Install packaging tools..."
6. Click OK
7. In terminal window, type
```
$ pip install --upgrade pip
$ pip install behave PyHamcrest
```

# Set behave to auto-run on code changes
1. Right-click on "features" and choose Run 'features'
2. Click Toggle auto-test icon to continuously run tests

