# ECMAScript Proposal: Public, Private, Protected, Static, Abstract, and Friend

## Introduction

This introduces tried and tested ideas from other languages for allowing developers to control code encapsulation.

This proposal ignores implementation details or feasibility to see more what an ideal system would look like and if it's possible to work toward such features.

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
	constructor()
	{
		this.x; // identical to this.constructor.x = 0;
	}
}

class B extends A
{
	
}
```

### Private static

```js
class A
{
	private static x;
}

class B extends A
{
	static x;
}
```

### Static constructor

A static constructor is invoked before first use of the class whether that's an instance being created or a static member accessed. It takes no arguments and can be used to set static members.

```js
class A
{
	static x = [];
	static constructor()
	{
		console.log('static constructor');
		for (let i = 0; i < 100; ++i)
		{
			this.x.push(i);
		}
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

## Abstract

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

## Friend

```js
class A
{
	friend B;
	friend C.f(a);
	friend f();
	private a;
}
```

Using friend for a whole class B allows B to access any private member in A.

```js
class B
{
	constructor(a)
	{
		a.a = 0;
	}
}

let a = new A();
let b = new B(a);
```

Using friend with an class method allows that method to access private members in A.

```js
class C
{
	f(a)
	{
		a.a = 0;
	}
}

let a = new A();
let b = new B();
b.f(a);
```

A friend function allows a function in scope to access the private members.

```js
function f(a)
{
	a.a = 0;
}
```
