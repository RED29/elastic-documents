# Welcome to Elastic Programming Language! #

## Overview ##

The Elastic programming language allows Elastic job authors to express complex algorithms to be solved for bounties, paid for with XEL. The language is loosely based on the C programming language incorporating many of the basic operators and functions. There are no FOR, WHILE, or DO loops in Elastic PL in order to ensure that programs will not run indefinitely. Instead, job authors will use the Elastic PL REPEAT function.

## PROGRAM LAYOUT ##

An ElasticPL program consists of:

	- 1 or more Global Variable arrays

	- "main" Function

	- "verify" Function

	- User-Defined Functions (Optional)

## GLOBAL VARIABLES ##

The ElasticPL VM performs all storage using Global Variables based on the standard 32bit and 64bit data types (int32_t, uint32_t, int64_t, uint64_t, float, double). Local storage is not supported.



Named variables are not allowed, instead data is stored in an array which represents the data type.  The arrays are declared as follows:



	array_int    XXXX

	array_uint   XXXX

	array_long   XXXX

	array_ulong  XXXX

	array_float  XXXX

	array_double XXXX



	Note 1: XXXX represents the number of elements in the array.

	Note 2: Only 1 instance of each data type can be declared.

	Note 3: The maximim combined storage size of all Global Variables can

	        not exceed (TBD - need to determine max memory)




All global arrays need to be declared as the first lines in the program, prior to any functions.




Array variables are accessed using the prefix i, u, l, ul, f, or d followed by square brackets that include the zero based array index.  For example:



	i[0] = 100;

	u[5] = u[3] * 10;

	d[6] = u[5] / i[5];



	Note 1: XXXX represents the number of elements in the array.

	Note 2: Only 1 instance of each data type can be declared.

	Note 3: The maximim combined storage size of all Global Variables can

	        not exceed (TBD - need to determine max memory)

			

Although it is discouraged to mix data types, there are times when this may be necessary.  Because ElasticPL does not have an explicit 'cast' operation, when an array variable is combined with a variable or constant of a different data type, the values being combined will be cast based on the following precedence:



	int32_t -> uint32_t -> int64_t -> uint64_t -> float -> double

	

	For example, using d[6] = u[5] / i[5];

	

		u[5] / i[5] evaluates to u[5] / (uint)(i[5]) 

		

		no cast is needed for (u[5] / (uint)(i[5])) as the d[6] storage

		can handle the result of the operation.




If a value assigned to an array variable has a different data type, the value will be cast to the same type as the array variable.


Variables that have a calculated index value can accept an Unsigned Int / Long as thier index; however, a boundary check will need to be performed which may impact performance.  Therefore, you may want to minimize the use of these.  For example:



	u[0] = u[u[3]]; evaluates to: u[0] = ((u[3]<MAX_ALLOWED) ? u[u[3]] : 0)

	u[u[3]] = u[0]; evaluates to: if (u[3]<MAX_ALLOWED) { u[u[3]] = u[0]; }

## VM INITIALIZED VARIABLES ##

The ElasticPL VM inializes 12 Unsigned Int variables each run/iteration.

These variables are stored in m[0] through m[11] and defined as follows:



	m[0]	Thread ID

	m[1]	Run Number

	m[2]	Iteration Number

	m[3]	Not Used (Default to 0xFFFFFFFF)

	m[4]	Random 32 bit Unsigned Int

	.

	.

	.

	m[11]	Random 32 bit Unsigned Int




The variables allow the job authors to randomize thier algorithm inputs as well as trigger functions to run at specific intervals (i.e. run an 'init' function only on run/iteration 0)

## FUNCTIONS ##

All ElasticPL statements (except declaring global variable arrays) must be contained within functions.

No functions within ElasticPL are allowed to call "main" or "verify".

Recursive function calls are not allowed.

Function names can be made up of numbers (0-9), letters (a-z), and underscores (_).  However, names cannot begin with any reserved word in the ElasticPL language.


Functions are declared as follows:



	function name_of_function {

		<statement1>;

		<statement2>;

		etc...

	}

	

	Note: Statements must be enclosed in {} brackets.



To call a function, use the function name and parenthesis as follows:



	name_of_function();



Functions can appear in any order within the program.


All programs must have a "main" and "verify" function; however, these functions can only be called by external programs.


The "main" function is called by the Elastic miner to search for solutions to the algorithm.



The "verify" function is called by both the Elastic miner as well as SuperNodes to validate that the solution meets the criteria established by the job author.


The last statement in a "verify" function must be the "result" operators which evalutes a statement as True or False.  For example:



	function verify {

		<statement1>;

		<statement2>;

		etc...

		

		result (i[0] == 0)

	}

	

	The result of this function is True if i[0] == 0; otherwise, it's false

## ELASTICPL STATEMENTS ##


Similare to C, all ElasticPL statements are terminated by a ';'



	IF / ELSE Statements

	--------------------



	ElasticPL supports IF / ELSE statements.  Their behavior is identical

	to the C programming language.




	

	REPEAT Statement
	
	----------------



	ElasticPL does not allow DO, WHILE, or FOR loops.  Instead, ElasticPL

	uses a 'repeat' statement which ensures all loops terminate and cannot

	run indefinitely.

	

	The format of the 'repeat' statment is as follows:

	

		repeat( <variable1>, <variable2>, <constant> ) { }




		repeat( u[100], u[200], 1000 ) {

			statement1;

			statement2;

			.

			.

			.			

		}

	

	variable1 = Unsigned Int / Long variable to store the loop counter

	variable2 = Unsigned Int / Long variable to store number of iterations

	constant  = Unsinged Int / Long constant for Max number of iterations allowed.

		



	ELASTICPL 'RESULT'
	
	------------------




	All ElasticPL programs must have a 'result' statement that has a right

	operand that can be evaluated to True / False.



	The 'result' is used to indicate whether or not a given solution

	satisfies the Bounty requirements.  For Example:



		result (u[1000] == 0)



	The above statement indicates that a bounty will be rewarded for solutions

	where there value stored in u[1000] equals zero.  Otherwise, the solution

	does not qualify for a bounty.



	Only one 'result' statement per program is allowed, and it must be the

	last statement in the 'verify' function.

## ELASTICPL OPERATORS ##

The following operators are supported by ElasticPL.  These operators behave the same as C operators based on the C99 standard:



	Precedence  Operator  Description            Order
	
	------------------------------------------------------------

    0         a++     Postfix Increment      (Left to Right)

    0         a--     Postfix Decrement      (Left to Right)

    1         ++a     Prefix Increment       (Right to Left)

    1         --a     Prefix Decrement       (Right to Left)

    1         -a      Unary Minus            (Right to Left)

    1         !       Logical NOT            (Right to Left)

    1         ~       Bitwise NOT            (Right to Left)

    2         *       Multiplication         (Left to Right)

    2         /       Division               (Left to Right)

    2         %       Remainder              (Left to Right)

    3         +       Addition               (Left to Right)

    3         -       Subtraction            (Left to Right)

    4         <<      Bitwise Left Shift     (Left to Right)

    4         <<<     Bitwise Left Rotation  (Left to Right)

    4         >>      Bitwise Right Shift    (Left to Right)

    4         >>>     Bitwise Right Rotation (Left to Right)

    5         <       Less Than              (Left to Right)

    5         <=      Less Than Or Equal     (Left to Right)

    5         >       Greater Than           (Left to Right)

    5         >=      Greater Than Or Equal  (Left to Right)

    6         >=      Equal                  (Left to Right)

    6         >=      Not Equal              (Left to Right)

    7         &       Bitwise AND            (Left to Right)

    8         ^       Bitwise XOR            (Left to Right)

    9         |       Bitwise OR             (Left to Right)

    10        &&      Logical AND            (Left to Right)

    11        ||      Logical OR             (Left to Right)

    12        a?b:c   Ternary Conditional    (Right to Left)

    13        =       Assignment             (Right to Left)

    13        +=      Add and Assignment     (Right to Left)

    13        -=      Sub and Assignment     (Right to Left)

    13        *=      Mul and Assignment     (Right to Left)

    13        /=      Div and Assignment     (Right to Left)

    13        %=      Mod and Assignment     (Right to Left)

    13        <<=     L Shift and Assignment (Right to Left)

    13        >>=     R Shift and Assignment (Right to Left)

    13        &=      Bit AND and Assignment (Right to Left)

    13        ^=      Bit XOR and Assignment (Right to Left)

    13        |=      Bit OR and Assignment  (Right to Left)

## ELASTICPL BUILT_IN FUNCTIONS ##

ElasticPL includes several functions from the C math library.  The built-in functions behave the same as in C.


	
	sinh( double d )             Computes hyperbolic sine
	
	sin( double d )              Computes sine
	
	cosh( double d )             Computes hyperbolic cosine
	
	cos( double d )              Computes cosine
	
	tanh( double d )             Computes hyperbolic tangent
	
	tan( double d )              Computes tangent
	
	asin( double d )             Computes arc sine
	
	acos( double d )             Computes arc cosine
	
	atan2( double d )            Computes arc tangent, using signs to determine quadrants
	
	atan( double d )             Computes arc tangent
