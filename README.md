# ECMAScript Proposal: Public, Private, Protected, Static, and Friend

## Introduction

This introduces tried and tested ideas from other languages for allowing developers to control code encapsulation.

This proposal ignores implementation details or feasibility to see more what an ideal system would look like and if it's possible to work toward such features.

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
	f() // public
	{
		// super.f(); // Invalid
		this.x = 0;
	}
}
```

### Private constructor

Used in utility classes, singleton, factory, and various other designs. A private constructor stops instances from being made. It also stops classes from extending it since constructing the class would involve invoking the private constructor.

```js
class A
{
	private constructor()
	{
	}
}

// let a = new A(); // Invalid: A's constructor is private
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

### Protected constructor

A protected constructor creates an abstract class that can't be instantiated, but it can be extended.

```js
class A
{
	protected constructor()
	{
	}
}

class B extends A
{
}

// let a = new A(); // Invalid: A's constructor is protected
let b = new B();
```

## Static

```js
class A
{
	static x;
	constructor()
	{
		this.constructor.x = 0; // or A.x = 0;
	}
}

class B extends A
{
	constructor()
	{
		this.constructor.x = 1; // Can access static super class public members in extended classes
	}
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
	static x; // Distinct from A.x
}

// A.x = 0; // Invalid, x is private
B.x = 0;
```

"static private x;" would not be allowed so the order of the modifiers matters.

### Protected static


```js
class A
{
	protected static x;
}

class B extends A
{
	constructor()
	{
		this.x = 0;
	}
}

// A.x = 0; // Invalid, x is private
// B.x = 0; // Invalid, x is private
```

Similarly as private "static protected x;" is invalid syntax.

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
			this.constructor.x.push(i);
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

## Friend

```js
class A
{
	friend B; // friend class
	friend C.f; // friend class method
	friend f; // friend function
	private a;
}
```

Using friend for a whole class B allows B to access private members in A.

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

Using friend with a class method allows that method to access private members in A.

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

Using a friend is also a way to bypass a private constructor. This is seen in other languages like C++.

```js
class A
{
	friend B;
	private constructor()
	{
	}
}

class B
{
	a = new A();
}
```

## Example using public, private, and static together

```js
class A
{
	x;
	private y;
	static z;
	f()
	{
		return this.x + this.y + this.constructor.z;
	}
}
let a = new A();
a.f();
```

## Abstract function syntax

```js
class A
{
	x(); // No method body indicates this is an abstract method
	// private x(); // Illegal, private methods cannot be abstract
}
class B extends A
{
	x()
	{
	}
}
/*
class C extends A // Invalid, must define abstract methods
{
}
*/
// let a = new A(); // Abstract function x undefined.
let b = new B();
b.x();
```

This does bring up a question if "abstract" as a keyword modifier on classes and functions is necessary. One argument for including it is to ensure that protected constructor isn't bypassed using a friend modifier. Including it creates two similar ways to create abstract classes. One way being a protected constructor and the other with an abstract function or class modifier.

## Extending modifiers

```js
class A 
{
	x;
	private y;
	protected z;
}

class B extends A
{
    // x is public
    // y is not accessible
    // z is protected
};

class C extends private A
{
    // x is private
    // y is not accessible
    // z is private
};

class D extends protected A
{
    // x is protected
    // y is not accessible
    // z is protected
};
```
