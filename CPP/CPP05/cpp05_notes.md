<h1>CPP05</h1>
<h2>Nested Classes</h2>
<ol>
	<li>
		A class can be nested within another class (the nested class itself is like any other member which has an access specifier in the enclosing class).
		<ul>
			<li>The nested class is therfore local to the enclosing class.</li>
			<li>This concept is similar for namespaces and structs etc.</li>
		</ul>
	</li>
	<br>
	<li>
		The members of an enclosing class have no special access to members of a nested class, thus obeying the usual rules. However, the members of nested class can access members of enclosing class (just like other members of enclosing class).
	</li>
</ol>

```c++
class A {
	int	x;
	class B {
		int	y;
		void	f(A *a) {
			std::cout << a->x; // Nested class able to access member of enclosing class
		}
	};
};
// Each class has its own 'this' pointer and cannot use one another's
```
```c++
class A {
	int	x;
	class B {
		int	y;
	};
	void	f(B *b) {
		std::cout << b->y; // Enclosing class is unable to access member of nested class
	}
};
```

<h2>Exceptions</h2>
<ol>
	<li>
		Exceptions allow us to do error handling in programs, deviating away from the traditional and rather unclean ways in the <i>C</i> language:
		<ol>
			<li>
				<code>Terminating the program with exit()</code>
				: difficult to incorporate informative error messages and program that cannot afford to crash cannot be unconditionally terminated.
			</li>
			<li>
				<code>Returning error values</code>
				: the most common way of handling. Error values are returned and checked in conditional statemenst to decide what action will be taken. With large programs, this would be inconvenient as we would have to keep returning and checking until the very top of the call stack, which can lead to bloated codebase.
			</li>
			<li>
				<code>Calling error-handler functions</code>
				: unless the function can somehow completely resolve the problem in it, it would still have to result in termination of program, returning some values to be checked, or throw an exception.
			</li>
		</ol>
		<ul><li>Traditionally, an unsystematic combination of these approaches co-exist in a program.</li></ul>
	</li>
	<br>
	<li>
		In <i>C++</i>, we can handle program errors in a "consolidated" way, that is by exception inspection. This is done by using <code>try-catch</code> blocks. When an exception occurs in the <code>try</code> block, control is transferred to the <code>catch</code> block where we can handle the exception.
	</li>
	<br>
	<li>
		If no exception is thrown, the code continues normally, and all handlers are ignored. Otherwise the code continues in the <code>catch</code> block and after it.
	</li>
	<br>
	<li>
		Exception is thrown using the <code>throw</code> keyword:
	</li>
</ol>

```c++
try {
	throw 20; // Can also be thrown in a function
}
catch (int e) { }
// catch is just like a normal function, taking in an argument. The argument type is checked against whatever is thrown from the try block (if they match the exception is caugh by the handler).

// We can also throw all sorts of exception such as int, char, class object, struct object and etc.
```

<ol start="5">
	<li>
		Multiple catch expressions can be chained so that we can handle accordinly for different exceptions:
	</li>
</ol>

```c++
try {
	if (a)
		throw 20;
	if (b)
		throw 'a';
}
catch (int a) { }
catch (char b) { }
catch (...) { } // catches all kinds of exception (not a good practice unless it is in main() to ensure cleanup and no stray exceptions are left uncaught).
```

<ol start="6">
	<li>
		It is also possible to nest <code>try-catch</code> blocks. This allows us to forward exceptions to the external level(s):
	</li>
</ol>

```c++
try {
	try {
		throw 20;
	}
	catch (int a) {
		throw; // throw without argument
	}
}
catch (int b) { } // handling in external level instead
```

<ol start="7">
	<li>
		Exceptions are handled by <code>stack unwinding</code>. When an exception occurs, the program will first check whether it can be handled in the current function (if the <code>try-catch</code> block is within the current function). If not, the program next checks whether the function's caller (next function up the call stack) can handle the exception. And if not again, it keeps going up the call stack until a handler is found, thereby "unwinding" the stack frame. <code>Stack unwinding</code> invokes the destructors for every fully constructed object so that they are properly destroyed. However, this can be implementation-defined so in some systems the destructors are not invoked. This is to preserve data for things like debugging.
		<ul>
			<li>
				If no matching handler is found, the program will be terminated with <code>std::terminate()</code>.
			</li>
			<li>
				If a matching handler is found, the program will continue from that point (remember that all data between the point where exception is thrown and the handler is lost).
			</li>
		</ul>
	</li>
</ol>

<h3>Exception Specifications (Deprecated)</h3>
<ol>
	<li>
		Exception specifications are originally used to allow programmers to state their intentions about the exception type that can be thrown by a function. It can be specified by appending the <code>throw</code> specifier at the end of the function declaration. This is more accurately known as "dynamic exception specification".
	</li>
</ol>

```c++
double	func(char arg) throw(int);
// If the function throws an int, a handler will be looked for or std::terminate() will be called

// If the function throws something other than int, an 'unexpected handler' will be called (std::unexpected()). This includes the case where the throw argument is empty (throw()).
```

<ol start="2">
	<li>
		<code>std::unexpected()</code> by default will call <code>std::terminate()</code>, which would terminate/abort the program. We can change the behavior by calling <code>std::set_unexpected()</code> which takes in a function pointer. Thus we can handle the unexpected exception accordingly, such as by rethrowing the proper exception specified in the function. If we do not rethrow the proper exception, the program will terminate.
	</li>
	<br>
	<li>
		<code>std::terminate()</code> will cause the program to terminate. We can specify a user-defined function with <code>std::set_terminate()</code> just like <code>std::set_unexpected()</code> but the program will terminate one way or another (no changing the behavior such that termination is prevented).
	</li>
	<br>
	<li>
		Dynamic exception specification is deprecated  due to incomplete compiler implementations, some incompatibility with template functions, common misunderstanding about how they worked, and the fact that the standard library mostly did not even use them.
	</li>
	<br>
	<li>
		Only <code>throw()</code> (with an empty argument) has significant meaning still. In modern <i>C++</i>, it is equivalent to <code>noexcept</code> or more accurately <code>noexcept(true)</code>. It tells the compiler that the function does not throw any exceptions ('non-throwing' function), and a warning will be generated if the function attempts to.
		<ul>
			<li>
				Insisting on throwing an exception from a non-throwing function will cause <code>std::terminate()</code> to be called (even if there is an exception handler). And if <code>std::terminate()</code> is called, stack unwinding may or may not occur, meaning that objects may or may not be destructed properly prior to termination (this depends on the implementation).
			</li>
			<li>
				"The promise that a non-throwing function makes to not throw exceptions that are visible to the caller is a contractual promise, not a promise enforced by the compiler."
			</li>
			<li>
				Marking functions as non-throwing can enable the compiler to perform some optimizations. As non-throwing functions cannot throw an exception out of them, the compiler does not have to worry about keeping the runtime stack in an unwindable state, which can allow it to produce faster code.
			</li>
		</ul>
	</li>
	<li>
		Like functions that differ in return type only cannot be overloaded, functions that differ in exception specification cannot be overloaded as well.
	</li>
</ol>
<h3>Standard Exceptions</h3>
<ol>
	<li>
		As mentioned earlier, we can throw class objects. <i>C++</i> provides a base class specifically designed to declare objects to be thrown as exceptions. It is called <code>std::exception</code> and is defined in the <code>&lt;exception&gt;</code>. This class has a virtual member function called <code>what()</code> that returns a <code>char *</code> and that can be overwritten/overloaded in derived classes to contain some sort of description of the exception.
	</li>
</ol>

```c++
class A : public std::exception {
	public:
		virtual const char *what() const throw() {
			return ("Exception thrown");
		}
};

int main(void) {
	try {
		throw A();
	}
	catch (const std::exception &e) {
		std::cout << e.what() std::endl;
	}
}
// The exception is caught in the catch block by reference. This is a good practice as the exception is not recreated again which saves on memory.

// As the user-defined exception inherits std::exception, other classes derived from std::exception (such as std::bad_alloc, std::bad_cast etc.) will also be caught (think subtyping polymorphism). If we pass by value of a base class but our exception is from the derived class, this would not work properly. Also if we do not want such a general catch, we can specify the class object itself (e.g. catch (const A &e)).
```

<ol start="2">
	<li>
		The <code>std::exception</code> class is declared as:
	</li>
</ol>

```c++
class exception {
	public:
		exception(void) throw();
		exception(const exception &) throw();
		exception	&operator=(const exception &) throw();
		virtual ~exception() throw();
		virtual const char	*what() const throw();
};
// As both the destructor and what() are declared as virtual and non-throwing, and if we were to override these functions in the derived class, the standard requires that the overriden functions must have the same restrictions or be more restrictive, not less. This is because the compiler will decide whether it should handle exceptions during compile time, mainly by examining the function signatures, and it is unable to determine which function to run until runtime (where dynamic binding of the correct function by lookup of virtual table occurs).

// Having the same restriction level or more between the base and derived class is known as the "Liskov Substitution Principle", which states that the subclasses should behave in the same way as the objects of superclass (without breaking the application of superclass). This principle can also be applied generally.
```