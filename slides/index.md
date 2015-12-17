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
> A type system is a tractable syntactic method for proving the absence of
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

> When I see a bird that walks like a duck and swims like a duck and quacks like a duck, I call that bird a duck.

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
	1 < undefined === false
	undefined < 1 === false

---

### Crazy examples

	[lang=js]
	1 + {} === "1[object Object]"
	{} + 1 === 1
	[] + [] === ""
	[] + {} === "[object Object]"
	{} + {} // NaN

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

	order = priority_queue.dequeue();
	order.id //throws if object does not have property

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
		attr_reader :str
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
	class ChildOrder < Order
		def calculate_price
			...
		end
	end

	price = order.calculate_price
	
---

### Display order

	[lang=ruby]
	order.display(customer)
	customer.display(order)
	display.display(order, customer)

* No built in mechanism
* ? visitor pattern

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

### Data representation

	[lang=clojure]
	[:normal, 10, ...] ;; vector
	{ :type :normal, :person { :name "John", :surname "Doe"}}

* can define custom types

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

### Map representation

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

---

### Types

	[lang=clojure]
	(defn ? method [? arg1, ? arg2]) ;; runtime


***

## Erlang

* more static than clojure, strongly typed, functional
* compiled
* 'let it crash'

---

### Data representation

	[language=erlang]
	{ normal, 24, [ pizza, cola]} % tuple

	-record(order, {type, person, contents, ...}). % record, compiler hack

---

### Static method definition

	[lang=erlang]
	calculate_price(#{type=normal, items=items, ...}) ->
		...
	calculate_price(#{type=child, items=items, ...}) ->
		...

	calculate_price() % does not compile
	calculate_price(order, argument2) % throws

---

### Types

	[lang=erlang]
	? function(? arg1, ? arg2, ...)

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

### Wrappers around basic functionality

* IO, network
* complicates testing

---


### Interfaces limitation

* explicit
* can't add to existing types

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

### Null

	[lang=java]
	priorityQueue.enqueue(null, priority);
	Order order = priorityQueue.dequeue();
	order.getId(); // error 

' does not happen that often, but is very painful and requires good documentation

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
* final (sealed in C#)
* access modifiers
* static methods

---

### Types

	[lang=java]
	public X method(X1 arg1, X2 arg2, ...) // compile check

	public T deserialize<T>(X arg, Class clazz) // no compile check between T and clazz, will fail at runtime

	X x = ...
	x.method(); //compile check

***

## C#
* static, strongly typed, OO
* running on .NET
* runtime generics

---

### Implicit conversion

	[lang=cs]
	var bytes = new byte[] { 10, 1, 2, 172 };
	long ip = bytes[0] | bytes[1] << 8 | bytes[2] << 16 | bytes[3] << 24;

---

### Implicit conversion

	[lang=cs]
	var bytes = new byte[] { 10, 1, 2, 172 };
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

***

## Dynamic vs static OO languages

---

### Gradual typing
* allowing static types in parts of application
* Typescript, Dialyzer, core.typed

---

### Structural typing
* Typescript, Go
* objects are same if they have same properties
* interfaces are implemented implicitly

---

### Structural typing

	[lang=typescript]
	interface Order {
		calculatePrice() : int;
		}
	class NormalOrder {
		display() : void {}

		calculatePrice() : number {
			return 1;
		};
	}

---

### IDE
* can give hints and warnings based on heuristic, not logic
* don't have to be precise

---

### C# dynamic keyword

	[lang=cs]
	dynamic x = new Order();
	dynamic id = Order.Id(); // runtime check
	dynamic id = Order.id(); // fails

---

### Dependency injection container

	[lang=cs]
	var container = new Container();
	container.Resolve<IOrderService>(); // throws, not registered

' not really a problem, adding new dependency means that we plan to use it and almost always get this error

---

### Convention based DI

	[lang=cs]
	public class OrdersController < Controller
	{
		public ActionResult View(int id)
		{
			...
		}
		...
	}

' ok in known frameworks, problematic when used in application without documentation

---

### What refactorings are safe?

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
	let incrementFirstAndAdd x y =
		let x1 = x + 1 // int
		x1 + y // int

---

### Hindley-Milner type inference

	[lang=fs]
	let toUpper s =
		s.ToUpper() //error, indeterminate lookup

---

### No implicit conversion

	[lang=fs]
	let parts = [|10L, 1L, 2L, 174L|]
	let address = parts.[0] ||| (parts.[1] <<< 8) ||| (parts.[2] <<< 16) ||| (parts.[3] <<< 24)

---

### Data representation

	[lang=fs]
	("normal", 24) // tuple (string, int)

	type Order = { type : string; person : Person, contents : ...} // record

---

### Wrapping primitive types

	[lang=fs]
	type OrderId = OrderId of int

	let loadOrder (id : OrderId) = ...

	let order = loadOrder 1 //fails
	let order = loadOrder OrderId(1) 

---

### Wrapping primitive types

	[lang=fs]
	(OrderId(1), Person(...)) // tuple (OrderId, Person)

---

### Discriminated unions

	[lang=fs]
	type Order =
	| Normal of OrderId * Customer * Item list
	| Discounted of OrderId * Customer * Item list * Discount
	| Child of ...

	let calculatePrice = function
		| Normal(_, _, items) -> ...
		| Discounter(_, _, items, discount) ->	
	    // compiler warning because match is incomplete

---

### Type providers

	[lang=fs]
	type OrderService = WsdlService<"http://address-to-service/Orders.asmx?WSDL">

	let orderDto = OrderService.Orders. ...
	OrderService.Orders.Save orderDto

***

## Haskell
* strong, static, functional
* pure, lazy

---

### Typeclasses

	[lang=haskell]
	class  Eq a  where
		(==), (/=) :: a -> a -> Bool

		x /= y     =  not (x == y)
		x == y     =  not (x /= y)

	instance Eq Order where
		(Order id1, _, _) == (Order id2, _, _) = id1 = id2

---

### Typeclasses

* similar to interfaces
* can be defined along with type
* can be added to existing types

---

### Side effects in method signature

	[lang=haskell]
	sum :: int -> int -> int
	sum x y = x + y

	sumAndShow :: int -> int -> IO int
	sumAndShow x y = do
		let total = x + y
		putStrLn (show total)
		total

***

## Idris
* strong, static, functional
* based on Haskell
* not lazy, dependent types

---

### First class types

---

### First class types

	[lang=idris]
	printf : (s : String) -> FormatArgs (formatString s) -> String

---

### Generics

	[lang=cs]
	public class PriorityQueue<T>

* type parametrized by type

---

### Dependent types

	[lang=idris]
	append : Vect n a -> Vect m a -> Vect (n + m) a
	append Nil       ys = ys
	append (x :: xs) ys = x :: append xs xs // compilation error, cant unify n + m with n + n

* type paramatrized by any value

---

### Dependent types

	[lang=idris]
	append : Vect n a -> Vect m a -> Vect (n + m) a
	append Nil       ys = ys
	append (x :: xs) ys = x :: append xs ys

***

### Dynamic vs static functional languages comparison
* static languages have better type checking
* dynamic languages can use simple syntax instead of complex types

---

### Dynamic vs static functional languages comparison
* set Order.Person.Address.City

---

### Clojure

	[lang=clojure]
	(assoc-in order [:person :address :city] "Praha")

---

### Haskell

	[lang=haskell]
	...

[Lenses](https://www.fpcomplete.com/school/to-infinity-and-beyond/pick-of-the-week/a-little-lens-starter-tutorial)

***

## Summary

---

### Dynamic vs static typing
* duck typing
* monkey patching
* structural typing
* explicit interfaces
* generics
* dependent types

---

### Weak vs strong typing

* implicit conversion

---

### Javascript

	[lang=js]
	1 + {} === "1[object Object]"
	{} + 1 === 1
	[] + [] === ""
	[] + {} === "[object Object]"
	{} + {} // NaN

---

### C#

	[lang=cs]
	long x = 1; //automatic cast
	x + 1; //works
	"string" + 1; // "string1"

	long ip = bytes[0] | bytes[1] << 8 | bytes[2] << 16 | bytes[3] << 24; // overflow

---

### F#

	[lang=fs]
	let x = 1L //int64
	x + 1 //compilation error, int64 + int

***

## OO vs functional

---

### OO
* single dispatch simple
* dispatch on different argument complicated

---

### Functional
* pattern matching based on value
* discriminated unions

---

## Questions
