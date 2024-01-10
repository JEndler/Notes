---
title: Some Notes about Clean Coding.
---
## Motivation

*Code is written once, read many times*
-> Time benefit to document and clean code.

**Agenda for the Meeting**
-> closely aligned to clean-code-developer.com

roughly 5 blocks of 6 sessions each.

---

# Clean Coding

**Values**: **Evolvability**
Code has to be adapted often.
Adapting is often easy in the beginning, harder towards the end
- cost increase is sort of exponential, changing stuff later becomes really hard later

> The simpler the software can be adapted, the higher the evolvability.

**Values**: **Correctness**

correct outputs and correct resource consumption.

**Values**: **Production Effiency**

Counterbalance to other values.
try to automate repetitive tasks.

**Values**: **Continuous Improvement**

reflect on what you are doing occaisonally.

**Naming**

Naming of entities in your code is hard.

>Good naming requires good descriptive skills and shared cultural background.

Well chosen names are documentation!

*Rules:*
- Use names that reveal intention.
- additional documentation should not be necessary


# Session 2

**DRY -> Don't Repeat Yourself**

Motivation: *Evolvability*
- Less Code is more readable
- Changing repeated code is tedious
- Code is documentation
- Extend the code easily by reusing extracted code

Motivation: *Correctness*
- Smaller code snippets/functions are easier to test
- Single source of truth

### Simple Refactor Patterns:

**Function Extraction**
easy as it sounds, extract a function
easy to introduce constants etc.

**Decorators**

-> A decorator is a function that takes another function as its argument, and returns yet another function.

Decorators are useful as they alleow the externsion of an existing function, without any modification to the original source code.

```python
from time import time


def timer_func(func):
	# This function shows the execution time of
	# the function object passed
	def wrap_func(*args, **kwargs):
		t1 = time()
		result = func(*args, **kwargs)
		t2 = time()
		print(f'Function {func.__name__!r} executed in {(t2-t1):.4f}s')
		return result
	return wrap_func


@timer_func
def long_time(n):
	for i in range(n):
		for j in range(100000):
			i*j


long_time(5)

```

- [x] #task Add Decorator Example


**Context Managers**
- aka *with* statement
- import contextlib.
- nice to use with connections for example
	- with connection:
		- do stuff()

*small example*
```python
def ssh_client(**connection_parameters, ):
	ssh = SSHClient()
	ssh.load_system_host_ekys(filename=host_keys_filename)
	ssh.connect(**connection_paramteres)

	yield # the stuff inside the with-statement runs here

	ssh.close()
```


> use enums instead of string parameters:
> func(mode: str) -> func(mode: enum)


# Session 3

Main Theme: **KISS - Keep it simple, stupid**

Motivation: Evolvability, Correctness, Production Efficiency

> ![Pasted image 20220504111507](Pasted%20image%2020220504111507.png)

dataclasses.dataclass -> ```__post__init``` method.

Use that for logging. Logger.info()

- [x] #task implement this for csgoscraper. ✅ 2023-05-29

- [x] #task pathlib paths, look at / concatenation operator. ✅ 2023-05-29

---

# Session 4

**Beware of Optimization**

- Low level optimization can make the code harder to read.
- Low level optimization can make the code harder to generalize.

This also impacts production efficiency.
- Spending too much time on unnecessary optimization

**Theory**

- Prefer readability over optimizing performance
- never optimize without *profiling* the code
- be careful in particular if it is unclear that optimizing a part of the code really helps with overall performance

-> **Make it run, Make it right, Make it fast.**

1. Try to implement a feature without considering clean coding principles
2. Refactor to adhere to clean coding principles like proper naming etc
3. Optimize only after everything is running cleanly

Practice - Root Cause Analysis

Rather than just eliminating symptoms of a problem, try to find its root cause.

Done via the Five Why's:
	Repeat the question "Why?" five times
	Should help to dig up problems below surface level.

