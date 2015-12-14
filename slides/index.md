- title : Compiler is my frenemy
- description : Compiler is my frenemy
- author : Filip Štaffa
- theme : night
- transition : default

***

# Compiler is my frenemy
Filip Štaffa

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
	priority_queue.enqueue(item); #throws

---

### Less implicit conversion

	[lang=ruby]
	1 < "asdf" # error
	1 < 1.2 # true

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

---

### Mixin

	[lang=ruby]
	class SizeMatters
		include Comparable
		attr :str
		def <=>(anOther)
			str.size <=> anOther.str.size
		end
	end

---

### More Order functionality
* Different types of orders
* Calculate price
* Display
  * depends on whom we display it to (different types of people)

---

### Calculate price

	[lang=ruby]
	def ChildOrder < Order
		def calculate_price
			...
		end
	end
	
---

### Display order
* No built in mechanism
* ? visitor

---

### Types

	[lang=ruby]
	? function(arg1, arg2, ...) # runtime

	a = ?

***

## OO dynamic languages
* flexible
* no compile time checking
* few refactorings are safe

***

## Clojure
* Lisp like language on JVM
* dynamic, strongly typed, functional

---

### Calculating price of order
OO language

	[lang=rb]
	price = order.calculate_price()

---

### Protocols

	[lang=clojure]
	(defprotocol Order
		(calculate-price [this]))

	(deftype ChildOrder []
		Order
		(calculate-price [this] ...))
	(deftype NormalOrder []
		Order
		(calculate-price [this] ...))

---

### Dynamic functional representation

	[lang=clojure]
	{ :type :normal-order, :items [..], :customer { ...}, ...}
	{ :type :child-order, :items [..], :customer { ...}, ...}

---

### Multimethods

	[lang=clojure]
	(defmulti calculate-price (fn [order] (:type order)))
	(defmethod calculate-price :normal
		[order] (...))
	(defmethod calculate-price :child
		[order] (...))

---

### Dynamic function resolution

	[lang=clojure]
	(defn square [x] (* x x))
	(deftest test-square-of-sum
		(expect [square (returns 16 (has-args [3]))]
			(is (= 16 (square-of-sum 2 1))))

### Types


***

## Erlang

* more static than clojure, weakly typed, functional
* compiled
* 'let it crash'

---

### Static method definition

	[lang=erlang]
	calculate_price(#{type=normal, items=items, ...}) ->
		...
	calculate_price(#{type=child, items=items, ...}) ->
		...

	calculate_price() % throws
	calculate_price(order, argument2) % throws

---

### Types

	[lang=erlang]
	? function(arg1, arg2, ...)

	a.?

***

## Functional dynamic languages
* uniform data representation
  * maps, records, tuples, ...
* structural matching based on contents

***

## Java
* static, strongly typed, OO
* running on JVM

---

	[lang=java]
	public class PriorityQueue<T> {
		public void enqueue(T item, int priority) {
			list.add(item);
			list.sort();
		}

		public T dequeue(){
			...
		}
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
	Order order = priorityQueue.dequeue();

---

### Without generics

	[lang=java]
	public class PriorityQueue {
		public Object dequeue()
	}

	Order order = (Order)priorityQueue.dequeue();

---

### Final keyword

	[lang=java]
	int startIndex = 1;
	int newIndex = startIndex + 1;
	startIndex = someCalculation(newIndex); //error, should assign to newIndex
	return newIndex;

---

### Final keyword

	[lang=java]
	final int startIndex = 1;
	int newIndex = startIndex + 1;
	startIndex = someCalculation(newIndex); //compiler error
	return newIndex;

---

### Other keywords
* final, sealed
* access modifiers
* static methods

***

## C#
* static, strongly typed, OO
* running on .NET

---

### Implicit conversion

	[lang=cs]
	var bytes = new byte[] {10, 1, 2, 172};
	long ip = bytes[0] | bytes[1] << 8 | bytes[2] << 16 | bytes[3] << 24;

---

### Implicit conversion

	[lang=cs]
	var bytes = new byte[] {10, 1, 2, 172};
	long ip = bytes[0] | bytes[1] << 8 | bytes[2] << 16 | bytes[3] << 24;

	byte << 8 // int
	byte | int //int

---

### Simple type inference

	[lang=cs]
	var list = new List<string>();
	var str = "asdf";

	var fun = (string x) => 5; // error
	Func<int, int> = x => 5;

---

### Generics
* runtime

***

## F#
* strongly typed, static, functional (with OO features)
* running on .NET

---

### Hindley-Milner type inference
* infering types based on usage
* does not work well on OO languages

---

### Hindley-Milner type inference

	[lang=fs]
	let incrementFirstAndMultiply x y =
		let x1 = x + 1 // int
		x1 + y // int

---

### Wrapping primitive types

	[lang=fs]
	type OrderId = OrderId of int

	let loadOrder (id : OrderId) = ...

	let order = loadOrder 1 //fails
	let order = loadOrder OrderId(1) 

---

### Type providers

---

### Discriminated unions

---

***

## Haskell

---

### Typeclasses

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

