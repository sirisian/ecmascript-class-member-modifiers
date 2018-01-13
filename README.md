# ECMAScript Proposal: Public, Private, Protected, Static, Abstract, and Friend

## Introduction

This introduces tried and tested ideas from other languages for allowing developers to control code encapsulation.

## 'this' behavior

One important difference in this proposal is that static members and methods can be accessed from any instance of the class and also using "this" inside of the class. What this means is whether a variable, say 'x', is public, private, or static inside of the class you access it like this.x.

## Public

```js
class A
{
	x;
	y = 0; // initial values can be set here or in the constructor
	
	constructor()
	{
		this.x = 0;
	}
}
```

## Private
```js
class A
{
	private x;
	
	constructor()
	{
	}
	
	private f()
	{
	}
}

class B extends A
{
	x;
	y = 0;
	
	f()
	{
		this.x = 0;
	}
}
```

### Private constructor

Used in utility classes, singleton, factory, and various other designs. A private constructor stops instances from being made. It also stops classes from extending it since constructing the class would involved invoking the private constructor.

```js
class A
{
	private constructor()
	{
	}
}

// let a = new A(); // Invalid
```

## Protected

```js
class A
{
	protected x;
}

class B extends A
{
	constructor()
	{
		this.x = 0;
	}
}
```

## Static

```js
class A
{
	static x;
	private static y;
	constructor()
	{
		this.x; // identical to this.constructor.y = 0;
	}
}

class B extends A
{
	
}
```

### Static constructor

A static constructor is invoked before first use of the class whether that's an instance being created or a static member accessed. It can be used to set

```js
class A
{
	static constructor()
	{
		console.log('static constructor');
	}
	constructor()
	{
		console.log('constructor');
	}
}

let a = new A();
// static constructor
// constructor
```

# Abstract

```js
abstract class A
{
	
}

class B extends A
{
	
}

// let a = new A(); // Error: A is abstract
let b = new B();
```

# Friend

```js

```

```js
class Example
{
	w; // public
	private x;
	static y; // Optional in this example since assigning this.y in the static constructor would define a static member y
	private static z;
  
	static constructor()
	{
		this.y = 0; // identical to this.constructor.y = 0;
		this.z = 0; // identical to this.constructor.z = 0;
	}
  
	constructor(w, x, y, z)
	{
		this.w = w;
		this.x = x;
		this.y = y; // identical to this.constructor.y = y;
		this.z = z; // identical to this.constructor.z = z;
	}
	
	private static Example1(y, z)
	{
		// this.w = 0; // error w is not static
		// this.x = 0; // error x is not static
		this.y = y;
		this.z = z;
	}
	
	static Example2(y, z)
	{
		Example1(y, z);
	}
	
	get X()
	{
		return this.x;
	}
	
	set X(x)
	{
		this.x = x;
	}
	
	private Example3()
	{
		return this.w + this.x + this.y + this.z;
	}

  	Example4()
	{
		return this.Example3();
	}
	
	Example5(example)
	{
		example.x = 5; // Without types is it possible to know if x is private?
	}
	
	Example6(example:Example)
	{
		example.x = 5; // With types this is the same class and in general languages allow this.
	}
	
	Example7(example:Example2)
	{
		example.x = 5; // error x is private
	}
}

class Example2
{
	private x;
}

Example.y = 0; // identical to Example.constructor.y = 0;
Example.z = 0; // identical to Example.constructor.z = 0;
// Example.Example1(0, 0); // error Example1 is private
Example.Example2(0, 0);

let example = new Example(0, 0, 0, 0);
example.w = 0;
example.x = 0;
example.y = 0;
// example.z = 0; // error z is private
// example.Example1(0, 0); // error Example1 is private
example.Example2(0, 0);
let x = example.X;
example.X = 0;
// example.Example3(); // error Example3 is private
example.Example4();
example.Example5(example);
example.Example6(example);
let example2 = new Example2();
example.Example5(example2);
example.Example6(example2);
// example.Example7(example2); // error x is private (in Example2)
```
