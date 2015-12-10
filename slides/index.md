- title : Compiler is my frenemy
- description : Compiler is my frenemy
- author : Filip ©taffa
- theme : night
- transition : default

***

# Compiler is my frenemy
Filip ©taffa

***

## Types in languages

---

### Type
> A type system is a tractable syntactit method for proving the absence of
> certain program behariors by classifying phrases according to the kinds of values they compute.

---

### Dynamic vs Static

* When errors appear (compile vs runtime)

---

### Weak vs Strong

* what errors are detected
' fuzzy definition

---

### Functional vs OOP

* Functional languages call functions with arguments
* OOP languages call methods on objects and these methods have arguments

***

## Javascript

---

### Duck typing

---

### Example

* food ordering system
* effectivelly queue

---

	[lang=js]
	Queue.prototype.enqueue = function(order) {
		this.queue.push(order);
	}
	Queue.prototype.dequeue = function() {
		return this.queue.shift();
	}

---

### Add priority

	[lang=js]
	Queue.prototype.enqueue = function(order, priority) {
		var item = { priority: priority, item: order };
		this.queue.push(item);
		this.queue.sort(function(a, b) { return a.priority < b.priority; });
	}
	Queue.prototype.dequeue = function() {
		return this.queue.shift().item;
	}
---

### And update all usages
![Mwahaha](images/mwahaha-squirrel.jpg)

---

### Text/IDE
' false positives (and even negatives in IDE)

---

### Tests

	[lang=js]
	function() {
		var queue = createQueue();
		var sut = createSut(queue);
		var order = createOrder();
		sut.MethodWhichAddsOrder(order, ...);
		expect(queue.dequeue()).toEqual(order);
	}

---

### How to identify it?
' assume that queue is a library and we can't change it

---

### Change existing code

	[lang=js]
	var original = Queue.prootype.enqueue;
	Queue.prototype.enqueue = function(order, priority) {
		if (typeof priority === 'undefined') {
			// notify of problem
		}
		original(order, priority);
	}

' even if we don't have sourcecode, potentially during runtime


### Why did this happen?

---

### Javascript argument handling

* ignores missing or additional arguments

---

### Implicit conversion

	[lang=js]
	1 < undefined
	undefined < 1

---

### Implicit conversion

	[lang=js]
	1 < undefined = false
	undefined < 1 = false

---

### Crazy examples

	[lang=js]
	[] + []
	[] + {}
	{} + {}

---

### Crazy examples

	[lang=js]
	[] + [] = ""
	[] + {} = [object Object]
	{} + {} = NaN

Video [Wat](https://www.destroyallsoftware.com/talks/wat)

---

## Ruby

---

### Method missing

* available in ES 6 using proxies

***

## OOP dynamic languages


***

## Clojure

***

## Functional dynamic languages

---

### Data are dynamic, function names and arity static

***

## Java


---

### Final keyword

***

## C#

---

#Implicit conversion


***

## Comparison

***

## F#

---

### Hindley-Milner type interence

---

### Wrapping primitive types

---

### Type providers

***

## Haskell

---

# Side effects in method signature

***

## Idris

---

### First class types

---

### Dependent types

---

### Effects library

***

