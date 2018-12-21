# Single Responsibility Principle

## Learning Goals

- Recognize the meaning of the single responsibility principle
- Consider some Object Oriented examples that uphold and violate the principle

## Introduction

At the core of Object Oriented programming, we are taking related data
and behaviors that previously acted independently, and binding them
together in a relevant way. With OO, we can wrap _meaning_ around
what would otherwise be variables and functions, ignorant of how they
work with each other.

Using multiple classes, we can build systems of related objects,
each encapsulating their own set of data and behaviors, all working
together through dependencies.

Sometimes, it can be difficult to figure out just what data and behaviors to
encapsulate together. When should a class be split into _two_ classes? How much
should each class _do?_

The single responsibility principle offers a simple answer: a class should
only have _one_ responsibility. If it has more than one, it should
be split into two or more classes.

In this lesson, we're going to take a look at the single responsibility
principle and how it affects the design of our code. While following
the principle can force us to create more classes, it ensures that
our code easier to change and maintain.

## Recognize the Meaning of the Single Responsibility Principle

The single responsibility principle is as follows:

> Every module or class should have responsibility over a single part of the
> functionality provided by the software, and that responsibility should be
> entirely encapsulated by the class.

The idea behind this principle is that if a class has more than one
responsibility and one day we decide to modify or rewrite just _one_ of those
responsibilities, it could interfere with the classes ability to perform the
its _other_ responsibilities.

## Code that Violates the Single Responsibility Principle

Consider the following `Car` class:

```js
class Car {
	constructor(vin, make, model, miles, gas) {
		this._vin = vin;
		this._make = make;
		this._model = model;
		this._miles = miles;
		this._gas = gas;
		this._engineRunning = false;
		this._engineStatus = 'good';
		this._gear = 'park';
	}

	startCar() {
		// if there is gas, the engine is 'turned on' and an interval is started to 'burn gas'
		if (this.gas > 0) {
			this._engineRunning = true;
			console.log('Engine started');
			this._burnGas = setInterval(() => {
				this.gas -= 0.001;
				if (this.gas <= 0) {
					this._engineRunning = false;
				}
			}, 100);
		} else {
			this._engineStatus = 'fuel low';
		}
	}

	stopCar() {
		this._engineRunning = false;
		console.log('Engine stopped');
		clearInterval(this._burnGas);
	}

	get info() {
		return `${this._make} ${this._model} - ${this._vin}`;
	}

	get fuelGuage() {
		return this._gas;
	}

	get gas() {
		return this._gas;
	}

	set gas(gas) {
		this._gas = gas.toFixed(3);
	}

	get miles() {
		return this._miles;
	}

	set miles(miles) {
		this._miles = miles;
	}

	get gear() {
		return this._gear;
	}

	set gear(gear) {
		this._gear = gear;
	}
}

let honda = new Car('1HGCM82633A', 'Honda', 'Civic', 37104, 12.4);

honda.startCar();
// => LOG: Engine started
honda.gas;
// => '12.363'
honda.gear = 'first';
// => 'first'
honda.gas;
// => '12.337'
honda.gas;
// => '12.319'
honda.gear = 'park';
// => 'park'
honda.stopCar();
// => LOG: Engine stopped

honda.info;
// => 'Honda Civic - 1HGCM82633A'
```

The above code works, and we could continue to add to the `Car` class. For
instance, we could implement methods for representing driving (slowly increasing
mileage while burning more gas). But there is _too much_ going on. What are the
`Car` classes responsibilities?

- It stores information about the car and an `info` getter to access it
- It stores and updates how much gas is in the car
- It stores and updates mileage
- It stores the current gear
- It stores information on the state of the engine
- It contains methods for starting and stopping the engine

When we first start creating an Object Oriented application, we are often
inclined to start off this way. A car is a tangible, 'whole' _thing_ and it
_does_ seem to make sense that we encapsulate all of its behaviors and data
together.

There is trouble though. For starters, the class is already pretty large, and if
we add anything more, its only going to get bigger and more complicated. Another
concern: some of these responsibilities are intertwined - when the engine is
running, gas is being burned. Modifying one behavior might change another. In
this example, it might be trivial, but in a more complex program, this could
mean _bugs_.

## Applying the Single Responsibility Principle

How might we fix the previous example and make sure it follows the single
responsibility principle? Well, if we consider the responsibilities again:

- It stores information about the car and an `info` getter to access it
- It stores and updates how much gas is in the car
- It stores and updates mileage
- It stores the current gear
- It stores information on the state of the engine
- It contains methods for starting and stopping the engine

Each of these could really be its own class, **_separate from the `Car` class_**.
We could give each a class name:

- `CarInfo`: stores information about the car and an `info` getter to access it
- `FuelTank`: stores and updates how much gas is in the car
- `Odometer`: stores and updates mileage
- `Transmission`: stores the current gear
- `Engine`: stores information about the engine
- `Ignition`: contains methods for starting and stopping the engine

We can still keep the `Car` class to represent the 'whole' concept and to act as
a top level container for the other classes, but its responsibility changes. The
`Car` class could simply have one _new_ responsibility:

- Contain all components that make up a car

Separating these responsibilities out does change how some of our methods
behave, but we can achieve effectively the same results as before:

```js
class Info {
	constructor(vin, make, model) {
		this._vin = vin;
		this._make = make;
		this._model = model;
	}

	get all() {
		return `${this.make} ${this.model} - ${this.vin}`;
	}

	get vin() {
		return this._vin;
	}

	get make() {
		return this._make;
	}

	get model() {
		return this._model;
	}
}

class FuelTank {
	constructor(gas) {
		this._gas = gas;
	}

	get gas() {
		return this._gas;
	}

	set gas(gas) {
		this._gas = gas.toFixed(3);
	}
}

class Odometer {
	constructor(miles = 0) {
		this._miles = miles;
	}

	get miles() {
		return this._miles;
	}

	set miles(miles) {
		this._miles = miles;
	}
}

class Transmission {
	constructor(gear = 'park') {
		this._gear = gear;
	}

	get gear() {
		return this._gear;
	}

	set gear(gear) {
		this._gear = gear;
	}
}

class Engine {
	constructor(fuelTank) {
		this._running = false;
		this._status = 'good';
		this._fuelTank = fuelTank;
	}

	get fuelTank() {
		return this._fuelTank;
	}

	get running() {
		return this._running;
	}

	set running(state) {
		this._running = state;
		if (this._running) {
			this._burnGas = setInterval(() => {
				this.fuelTank.gas -= 0.001;
				if (this.fuelTank.gas <= 0) {
					this._running = false;
				}
			}, 100);
		} else {
			clearInterval(this._burnGas);
		}
	}

	get status() {
		return this._status;
	}

	set status(status) {
		this._status = status;
	}
}

class Ignition {
	constructor(engine) {
		this._engine = engine;
	}

	get engine() {
		return this._engine;
	}

	start() {
		if (this.engine.fuelTank.gas > 0) {
			this.engine.running = true;
			console.log('Engine started');
		}
	}

	stop() {
		this.engine.running = false;
		console.log('Engine stopped');
	}
}

class Car {
	constructor(
		info,
		fuelTank,
		ignition,
		transmission = new Transmission(),
		odometer = new Odometer()
	) {
		this._info = info;
		this._fuelTank = fuelTank;
		this._ignition = ignition;
		this._transmission = transmission;
		this._odometer = odometer;
	}

	get info() {
		return this._info;
	}

	get fuelTank() {
		return this._fuelTank;
	}

	get ignition() {
		return this._ignition;
	}

	get transmission() {
		return this._transmission;
	}

	get odometer() {
		return this._odometer;
	}
}

let info = new Info('1HGCM82633A', 'Honda', 'Civic');
let tank = new FuelTank(12.4);
let odometer = new Odometer(31704);
let transmission = new Transmission();
let engine = new Engine(tank);
let ignition = new Ignition(engine);
let honda = new Car(info, tank, ignition, transmission, odometer);

honda.fuelTank.gas;
// => '12.4'
honda.ignition.start();
// LOG: Engine started
honda.fuelTank.gas;
// => '12.386'
honda.transmission.gear = 'first';
// => 'first'
honda.fuelTank.gas;
// => '12.363'
honda.transmission.gear = 'park';
// => 'park'
honda.ignition.stop();
// LOG: Engine stopped

honda.info;
// => Info { _vin: '1HGCM82633A', _make: 'Honda', _model: 'Civic' }
```

Notice now that our methods have been distributed over many classes, we can
still use `honda` as the starting point to access each one. Through `honda`, we
can get to all the functionality we had.

This configuration is just one option - depending on what information and
behaviors we need, we could get more detailed or realistic and add even more
classes. We could de-compose the engine down into individual parts, for
instance.

Exactly how we shape associations between classes is up to us, and that is one
of the benefits of applying SRP. Instead of all the responsibilities jumbled
into one class, we are encouraged to specifically define how each responsibility
is related to another. The `Transmission` doesn't need to be connected to the
odometer. Both the `Car` and `Engine`, however, have direct connections to the
`FuelTank`. We've established the meaning and purpose of each thing by providing
their context to other classes.

We've isolated behaviors, so if we choose to modify one class, we've reduced the
chance for unexpected side effects. If there are bugs, we can clearly follow
behavior from class to class and know what other classes might be affected.

## Conclusion

In general, when a class begins to fill up with a lot of methods or properties,
it is a good time to ask yourself some questions:

- What is this class doing?
- Does it have more than one responsibility?
- Can I separate out these responsibilities into distinct classes?

Following SRP tends to create applications with many small classes. In general,
this tends to make the purpose of a single class easier to understand. SRP
encourages us to extract out the inner workings of one BIG class into meaningful
relationships between many smaller classes.

One caveat about the single responsibility principle: _sometimes_, creating too
many classes can _add_ complexity and be detrimental. The purpose of SRP is to
reduce complexity and more easily change parts of an application, not make
things more complicated. Use it as a tool to 'untangle' your classes and
responsibilities, not has a hard rule you must always follow.

[srp]: https://en.wikipedia.org/wiki/Single_responsibility_principle
[crc]: https://en.wikipedia.org/wiki/Class-responsibility-collaboration_card
