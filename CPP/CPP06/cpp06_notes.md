<h1>CPP06</h1>
<h2><i>C</i> Type Conversion</h2>
<ol>
	<li>
		Type conversion is the process of converting from one data type to another. It is only performed to those data type where conversion is possible (such as from char to int, not from struct to int; mostly involving primitives). This process is done by the compiler.
	</li>
	<br>
	<li>
		There are two types of conversion in C:
	</li>
</ol>

<h3>1. Implicit Type Conversion</h3>
<ol>
	<li>
		Value of one type is automatically converted to the value of another type (also known as automatic type conversion).
	</li>
	<br>
	<li>
		Occurs in expressions involving more than one data type, such as initializing one data type to another (int to float) and addition of two data types (int + float).
	</li>
	<br>
	<li>
		It is possible for conversions to lose data (accuracy/precision etc.). For example, signs can be lost (when converting from a signed data type to unsigned), and overflow can occur (when converting from a long to int).
	</li>
	<br>
	<li>
		Converting from a smaller data type to a larger one is called <code>promotion</code>, whereas for the opposite it is called as <code>demotion</code>.
	</li>
	<br>
	<li>
		In any expressions other than (explicit) assignment, demotion will never occur.
	</li>
	<br>
	<li>
		Conversion of two different data types in an expression (except assignment) follows a conversion hierarchy:
	</li>
</ol>

<img src="images/Implicit-Type-Conversion-in-c.png">
<p>The conversion rank from the smallest to largest data type goes from bool to long double. (All data types may not be listed here)</p>

<h3>2. Explicit Type Conversion</h3>
<ol>
	<li>
		This process is also called as type casting (or rather, it <i>involves</i> type casting).
	</li>
</ol>

```c
/*	SYNTAX	*/
(type)expression

/*	EXAMPLE	*/
double	x = 1.2;

int		sum = (int)x + 1; // Explicit conversion from double to int
```

<ol start="2">
	<li>
		Explicit type conversion is mainly used in type demotions to improve readability. As type demotions are usually undesired due to the (potential) loss of data, if we intend to perform a type demotion, we use the explicit conversion to express so.
	</li>
	<br>
	<li>
		In some cases the compiler will ask us to perform explicit conversions, as it does not know which data type is should be using. For example in a comparison of two values of different type, if we do not type cast one of the values the compiler will not know which data type we are trying to compare the two values in. This especially occurs in data types of different signedness (such as between size_t (long unsigned int) and long).  However, the compiler will only tell us if we add the <code>-Wextra</code> flag during compilation. Otherwise it would not mention anything.
	</li>
</ol>

```c
size_t	a = 3;
long	b = 4;
if (a == b) // Compiler does not know whether to use size_t or long to compare
	...;
// The compiler we promote the smaller data type most of the time as mentioned above
```