Download Link: https://assignmentchef.com/product/solved-cs305-hw3-semanticanalyzer
<br>
In this homework you will implement a small scale semantic analyzer and a type checker for BMO language. Your tool will check if a given BMO program has problems with respect to some type compatibility rules, which will be explained below. In this homework, we will try only the inputs that are grammatically correct with respect to the grammar given in the bison file attached to this homework. However, these BMO examples may or may not have the type compatibility problems that you are supposed to check in this homework. In the case that there is a type compatibility issue, you can assume that there will be only one such problem in the input. Therefore, when you detect an error, you can simply terminate after producing the required error message.

int vector digits, digits2 = [1,2,3,4,5,6,7,8,9,0]; int matrixM = 3; int matrix result = [1,2;3,4;5,6;7,8;9,0]; real realNum = 4.0E-12; int matrix resultT = transpose([1,2;3,4;5,6;7,8;9,0]); if(digits == digits2) realNum = 5.0E-12;

endif

Below is the detailed syntactic features of the BMO language. <strong>The grammar of BMO in HW3 is slightly different than HW2 (in HW3 we only allow constants to be used in expressions). </strong>Note that, a scanner and a parser for BMO are provided as an attachment to this homework. You can use these scanner/parser, or you can implement a scanner/parser pair of your own.

<ol>

 <li>A BMO program consists of a non–empty sequence of statements.</li>

 <li>Each statement in our BMO can be a declaration, or an assignment statement or an if</li>

 <li>A declaration is given by the type, followed by a comma separated list of variable names, followed by an assignment sign, followed by an expression, and followed by a semicolon.</li>

 <li>The type can be one of the following: int, int vector, int matrix, real, real vector, real matrix.</li>

 <li>An assignment statement given by a variable name, and an equality sign (i.e. the character =), and then an expression, finally followed by a semicolon. E.g.</li>

</ol>

a = 1+2;

<ol start="6">

 <li>An if statement starts with keyword if. After the keyword if, a boolean expression resides betweeen ( and ). This is followed by the body of the if statement, which is a non–empty sequence of statements. Finally, the keyword endif is given.</li>

 <li>An expression can be a number (integer or real), or a vector literal, or a matrix literal, or two expressions being applied to one of the operators ∗,+,−,<em>/</em>,<em>.</em>∗ or a transpose expression.</li>

 <li>A vector literal is given by a [, followed by a value row, followed by a ].</li>

 <li>A value row is a comma separated list of values, where a value is either a number (integer or real), or a variable name.</li>

 <li>A matrix literal is given by a [, followed by a semicolon separated list of value rows, followed by a ]. There has to be at least two rows in a matrix literal.</li>

 <li>A transpose expression is given by the keyword transpose, followed by a (, followed by an expression, followed by a ).</li>

 <li>A boolean expression is either a simple comparison or two boolean expressions connected with a boolean operator, where we only have &amp;&amp; and || as the boolean operators.</li>

 <li>A simple comparison must have exactly two variables separated with one of the comparison operators &gt;,&lt;,&gt;=,&gt;=,!=,==.</li>

</ol>

<h1>1             Errors</h1>

There are two different type of errors that your tool will check. The errors are related to the suitability of the dimensions of the matrix/vector/scalar values used in the expressions. They are quite intuitive as these rules are the usual rules that we know from matrix operations. We explain these two errors and the error messages that you need to produce below.

<ol>

 <li>A matrix literal consists of a sequence of rows, rows are separated by semicolons. In a matrix literal, each row must have the same number of columns. If a matrix literal has rows with different number of columns, then you must produce an error message. The format of the error message is as follows:</li>

</ol>

ERROR 1: <em>lineNo </em>inconsistent matrix size

In this error message, “<em>lineNo</em>” is the line number on which this matrix literal is seen. Note that, the line number of the opening left bracket of the matrix literal is considered to be the line number of a matrix literal. Please see Section 3 for a concrete example of this error message.

<ol start="2">

 <li>In an expression, we may have scalar values, vector values, and matrix values as the operands. An expression is “ill formed” if the size of the operands are not appropriate for the operator of the expression. For example, a 2 × 3 matrix cannot be multiplied by a 5 × 4 matrix, or a 4 × 1 vector cannot be added with a scalar value, etc. Here, we consider the usual matrix, vector, scalar operations with the usual size compatibility rules. Your parser will analyze the expressions on the right hand side of declaration and assignment statements. For each binary expression of the form</li>

</ol>

operand 1 operator operand 2

your parser will infer the dimension of operand 1 and operand 2, and will check if the dimensions of operand 1 and operand 2 are suitable for the operand in this expression.

A vector literal with <em>n </em>values is considered to have dimension 1 × <em>n</em>. A matrix literal with <em>m </em>rows where each row having <em>n </em>columns is considered to have dimension <em>m </em>× <em>n</em>.

Note that, you will have to infer the dimensions of the subexpressions. For example consider the following expression consisting of three operands multiplied:

[1,2,3;4,5,6] * [7;8;9] * [10,11]

The first operand has dimension 2 × 3, the second operand has dimension 3 × 1, and the last operand has dimension 1 × 2.

Since the multiplication is left associative, first the following subexpression will be considered:

[1,2,3;4,5,6] * [7;8;9]

The operands have appropriate dimensions for this multiplication. Therefore no error will be produced. Furthermore, the dimension of the resulting expression will be inferred as 2×1. And this dimension is suitable for multiplying from left with the last operand which has dimension 1 × 2. The dimension of the overall expression will be inferred as 2 × 2.

When the parser sees that there is a dimension mismatch for a binary expression, the following error message must be produced:

ERROR 2: <em>lineNo </em>dimension mismatch

In this error message, “<em>lineNo</em>” is the line number of the operator of the binary expression.

<h1>2             Example Outputs</h1>

<ol>

 <li>INPUT:</li>

</ol>

int vector digits = [1,2,3,4,5,6,7,8,9,0]; int matrixm = 3; int a = 5;

int matrix result = [1,2;3,4;5,6;7,8;9,0]; int matrix resultT = transpose([1,2;3,4;5,6;7,8;9,0]); int matrix myMat = [1,2,3;3,4;6,1;7,8;9,0,1]; if(matrixm &gt;= a) matrixm = 4; endif

OUTPUT:

ERROR 1: 6 inconsistent matrix size

<ol start="2">

 <li>INPUT:</li>

</ol>

int a = 3; int b = 4;

real realNum = 4.0E-12; int matrix mult = [1,2;3,4;5,6;7,8;9,0] * [1,2,3;3,4,5;5,6,7;7,8,9;9,0,1]; if(a &gt;= b) a = 1;

endif

OUTPUT:

ERROR 2: 4 dimension mismatch

<ol start="3">

 <li>INPUT:</li>

</ol>

int abc = 6; int matrix result = [1,2;5,6;7,8;9,0]; int matrix resultT = transpose([1,2;2,3]); int matrix calc = [1,2;3,4] + [1,2,3;3,4,5;5,6,7]; abc = 8;

OUTPUT:

ERROR 2: 4 dimension mismatch

<ol start="4">

 <li>INPUT:</li>

</ol>

int vector digits, digits2 = [1,2,3,4,5,6,7,8,9,0]; int matrixM = 3; int matrix result = [1,2;3,4;5,6;7,8;9,0]; real realNum = 4.0E-12;

int matrix resultT = transpose([1,2;3,4;5,6;7,8]);

OUTPUT:

OK