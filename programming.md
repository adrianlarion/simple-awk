### Programming intro
* Awk is a full blown programming language. It has all the operators & syntax you would expect from a "regular" programming language such as C or python. 
* This is taken directly from the manual
```
 Operators
       The operators in AWK, in order of decreasing precedence, are:

       (...)       Grouping

       $           Field reference.

       ++ --       Increment and decrement, both prefix and postfix.

       ^           Exponentiation (** may also be used, and **= for the assignment operator).

       + - !       Unary plus, unary minus, and logical negation.

       * / %       Multiplication, division, and modulus.

       + -         Addition and subtraction.

       space       String concatenation.

       |   |&      Piped I/O for getline, print, and printf.

       < > <= >= == !=
                   The regular relational operators.

       ~ !~        Regular expression match, negated match.  NOTE: Do not use a constant regular expression (/foo/) on the left-hand side of a ~ or !~.  Only use one on the right-hand side.  The expression
                   /foo/ ~ exp has the same meaning as (($0 ~ /foo/) ~ exp).  This is usually not what you want.

  &&          Logical AND.

       ||          Logical OR.

       ?:          The  C  conditional  expression.  This has the form expr1 ? expr2 : expr3.  If expr1 is true, the value of the expression is expr2, otherwise it is expr3.  Only one of expr2 and expr3 is
                   evaluated.

       = += -= *= /= %= ^=
                   Assignment.  Both absolute assignment (var = value) and operator-assignment (the other forms) are supported.

   Control Statements
       The control statements are as follows:

              if (condition) statement [ else statement ]
              while (condition) statement
              do statement while (condition)
              for (expr1; expr2; expr3) statement
              for (var in array) statement
              break
              continue
              delete array[index]
              delete array
              exit [ expression ]
              { statements }
              switch (expression) {
              case value|regex : statement
  ...
              [ default: statement ]
              }

```
