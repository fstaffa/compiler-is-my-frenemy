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

### Functional vs OO

* Functional languages call functions with arguments
* OO languages call methods on objects and these methods have arguments

***

## Javascript
* dynamic, weakly typed, OO
* primary about ES 5

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

### Monkey patching

---

### Monkey patching example

	[lang=js]
	var original = Queue.prootype.enqueue;
	Queue.prototype.enqueue = function(order, priority) {
		if (typeof priority === 'undefined') {
			// notify of problem
		}
		original(order, priority);
	}

' even if we don't have sourcecode, potentially during runtime

---


### Monkey patching example

* adding new behavior
* changing current behavior
	* testing
	* AOP
	...

---


### Why did this happen?

---

### Duck typing

When I see a bird that walks like a duck and swims like a duck and quacks like a duck, I call that bird a duck.

---

### Duck typing

	[lang=js]
	queue.enqueue(item);

	priorityQueue.enqueue(item //missing argument);

	Queue.prototype.enqueue = function(order, priority) {
		var item = { priority: priority, item: order };
		this.queue.push(item);
		this.queue.sort(function(a, b) { return a.priority < b.priority; });
	}

---

### Duck typing
* good for brevity and testing
* can lead to errors

---

### Javascript argument handling

	[lang=js]
	Queue.prototype.enqueue = function(order, priority) {
		var item = { priority: priority, item: order };
		this.queue.push(item);
		this.queue.sort(function(a, b) { return a.priority < b.priority; });
	}
		
	queue.enqueue(item) // priority undefined

' also additional arguments not specified in method are ignored

---

### Unknown methods return undefined

	[lang=js]
	var o = {};
	var a = o.doesNotExist; //undefined
	o.func(); //undefined is not a function error

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

### Types

	[lang=js]
	? function(?)

	var a = ?;

***

## Ruby
* dynamic, strongly typed, OO
* very magical

---

### Safer

	[lang=ruby]
	priorityQueue.enqueue(item); #throws

---

### Convention over configuration

	[lang=ruby]
	class OrderController < Controller
		def add
			...
		end
		def update
			...
		end
	end

---

### Method missing

	[lang=ruby]
	class Person < ActiveRecord::Base
	end

	person = Person.find_by_email(123)
	person.email

---

### Method missing

* hook on object which handles unknown calls
* available in ES 6 using proxies

### Mixin
* alternative to inheritance

---

### Types

	[lang=ruby]
	? function(arg1, arg2, ...) # runtime

	a = ?

***

## OO dynamic languages
* flexible
* no compile time checking
* few refactoring are safe

***

## Clojure
* Lisp like language on JVM
* dynamic, strongly typed, functional

---

### Calculating price of order
OO language

	[lang=rb]
	price = order.calculate_price()

***

### Protocols

---

### Multimethods

***

## Erlang

* more static than clojure, weakly typed, functional
* compiled
* 'let it crash'

---

### Static method definition

---

### Types

***

## Functional dynamic languages


***

## Java
* static, strongly typed, OO

---

	[lang=java]
	public class PriorityQueue<T> {
		public void enqueue(T item, int priority) {
			array.add(item);
			array.sort()
		}

	public T dequeue(
	}

---

### Interfaces

	[lang=java]
	public interface PriorityQueue {
	...
	}

	public class PriorityQueueImpl {
	...
	}

---

### Generics

	[lang=java]
	public class PriorityQueue {
		public object dqeueue()
	}

	var order = (Order)priorityQueue.dequeue();

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

