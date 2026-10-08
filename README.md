# SDVM-lang

Repository for the specification of the language defined for the Software Maintenance and Evolution class by the SDVM Profile's students.

## Language features

* Basic types: `int`, `double`, `char`, `bool` and `void` as a return type for functions
* Static arrays: `int a[10];` (only accepting integer literals for the size specifier)
* Functions with typed parameters and return values
* Global and local variables
* Lexical block scope
* `if` / `else`
* `while`
* `for`
* `break`, `continue`
* `return`
* Arithmetic operators: `+`, `-`, `*`, `/`, `%`
* Boolean operators: `&&`, `||`, `!`
* Comparison operators: `<`, `<=`, `>`, `>=`, `==`, `!=`
* Explicit type casts: `(double)x`
* Built-in `print(...)` function that prints the value of an expression to the standard output
* Built-in `read(...)` function that reads a value from the standard input and assigns it to a variable
* `'a'` character literals
* `"hello"` string literals
* `//` and `/* ... */` comments


## Grammar

### Grammar tuple

The grammar is defined as the tuple:

```text
G = (T, N, S, P)
```

where:

```text
T = {
  INTEGER, FLOAT, IDENTIFIER, CHAR, STRING,
  "int", "double", "char", "bool", "void",
  "if", "else", "while", "for", "break", "continue", "return",
  "print", "read", "true", "false",
  "{", "}", "[", "]", "(", ")", ";", ",",
  "=", "||", "&&", "==", "!=", "<", "<=", ">", ">=",
  "+", "-", "*", "/", "%"
}

N = {
  program, declaration_list, declaration,
  type, variable_declaration, variable_declaration_core, variable_initializer,
  array_declaration_initializer, array_initializer, array_initializer_contents,
  array_initializer_tail,
  function_definition, return_type, parameter_list_optional, parameter_list,
  parameter_list_tail, parameter,
  statement, statement_list, block, assignment, if_statement, else_clause,
  while_statement, for_statement, for_initializer_optional, expression_optional,
  for_initializer, return_statement, return_expression_optional,
  print_statement, read_statement,
  expression, expression_tail, logical_and, logical_and_tail, equality,
  equality_tail, equality_operator, comparison, comparison_tail,
  comparison_operator, additive, additive_tail, additive_operator,
  multiplicative, multiplicative_tail, multiplicative_operator, unary,
  unary_operator, postfix, function_call, argument_list_optional,
  argument_list, argument_list_tail, primary
}

S = program
```

The `P` set consists of the following BNF production rules. 

### Program structure

```bnf
program ::= declaration_list

declaration_list ::= declaration declaration_list
                   | ε

declaration ::= variable_declaration
              | function_definition
```

### Types

```bnf
type ::= "int"
       | "double"
       | "char"
       | "bool"
```

### Declarations

```bnf
variable_declaration ::= variable_declaration_core

variable_declaration_core ::= type IDENTIFIER
                              variable_initializer
                            | type IDENTIFIER
                              "[" INTEGER "]"
                              array_declaration_initializer

variable_initializer ::= "=" expression
                       | ε

array_declaration_initializer ::= "=" array_initializer
                                | ε

array_initializer ::= "{" array_initializer_contents "}"

array_initializer_contents ::= expression array_initializer_tail
                             | ε

array_initializer_tail ::= "," expression array_initializer_tail
                         | ε
```


### Functions

```bnf
function_definition ::= return_type IDENTIFIER
                        "(" parameter_list_optional ")"
                        block

return_type ::= type
              | "void"

parameter_list_optional ::= parameter_list
                          | ε

parameter_list ::= parameter parameter_list_tail

parameter_list_tail ::= "," parameter parameter_list_tail
                      | ε

parameter ::= type IDENTIFIER
```


### Statements

```bnf
statement ::= block
            | variable_declaration
            | assignment
            | if_statement
            | while_statement
            | for_statement
            | return_statement
            | print_statement
            | read_statement
            | "break" ";"
            | "continue" ";"
            | function_call ";"

block ::= "{" statement_list "}"

statement_list ::= statement statement_list
                 | ε

assignment ::= IDENTIFIER "=" expression
             | IDENTIFIER "[" expression "]" "=" expression ";"

if_statement ::= "if" "(" expression ")"
                 statement
                 else_clause

else_clause ::= "else" statement
              | ε

while_statement ::= "while" "(" expression ")"
                    statement

for_statement ::= "for"
                  "("
                    for_initializer_optional
                    ";"
                    expression_optional
                    ";"
                    expression_optional
                  ")"
                  statement

for_initializer_optional ::= for_initializer
                           | ε

expression_optional ::= expression
                      | ε

for_initializer ::= variable_declaration_core
                  | assignment

return_statement ::= "return" expression ";"

print_statement ::= "print" "(" expression ")" ";"

read_statement ::= "read" "(" IDENTIFIER ")" ";"

```


### Expressions

```bnf
expression ::= logical_and expression_tail

expression_tail ::= "||" logical_and expression_tail
                  | ε

logical_and ::= equality logical_and_tail

logical_and_tail ::= "&&" equality logical_and_tail
                   | ε

equality ::= comparison equality_tail

equality_tail ::= equality_operator comparison equality_tail
                | ε

equality_operator ::= "=="
                    | "!="

comparison ::= additive comparison_tail

comparison_tail ::= comparison_operator additive comparison_tail
                  | ε

comparison_operator ::= "<"
                      | "<="
                      | ">"
                      | ">="

additive ::= multiplicative additive_tail

additive_tail ::= additive_operator multiplicative additive_tail
                | ε

additive_operator ::= "+"
                    | "-"

multiplicative ::= unary multiplicative_tail

multiplicative_tail ::= multiplicative_operator unary multiplicative_tail
                      | ε

multiplicative_operator ::= "*"
                          | "/"
                          | "%"

unary ::= unary_operator postfix
        | postfix

unary_operator ::= "+"
                 | "-"
                 | "!"

postfix ::= primary
          | primary "[" expression "]"
          | function_call

function_call ::= primary
                  "(" argument_list_optional ")"

argument_list_optional ::= argument_list
                         | ε

argument_list ::= expression argument_list_tail

argument_list_tail ::= "," expression argument_list_tail
                     | ε

primary ::= INTEGER
          | FLOAT
          | CHAR
          | STRING
          | "true"
          | "false"
          | IDENTIFIER
          | "(" expression ")"
          | "(" type ")" expression
```

## Example programs

The defined example programs can be found in the [examples folder](examples/).

## Additional notes and considerations

### 1. Expressions and precedence

From **lowest to highest precedence**:

| Level | Operators                   | Associativity |
| ----: | --------------------------- | ------------- |
|     1 | `\|\|`                      | left          |
|     2 | `&&`                        | left          |
|     3 | `==`, `!=`                  | left          |
|     4 | `<`, `<=`, `>`, `>=`        | left          |
|     5 | `+`, `-`                    | left          |
|     6 | `*`, `/`, `%`               | left          |
|     7 | unary `+`, `-`, `!`         | right         |
|     8 | function call, indexing     | -----         |

### 2. Lexical considerations

The lexical patterns of the tokens would be somewhat like the following (in regex notation):

```text
INTEGER    = [0-9]+
FLOAT      = [0-9]+\.[0-9]+([eE][+-]?[0-9]+)?
IDENTIFIER = [A-Za-z][A-Za-z0-9_]*
CHAR       = '([^'\\]|\\.)'
STRING     = "([^"\\]|\\.)*"
```

Whitespace and comments are ignored.
The literals included in the grammar (words in between quotes) must be recognized before the IDENTIFIER token.

### 3. Semantic rules

The grammar allows constructs that require semantic validation, such as:
* Array sizes equal to zero
* Empty blocks
* Using `break` or `continue` outside of a loop
* Returning an expression from a `void` function
* Omitting a return value from a non-void function
* Type-incompatible assignments and expressions
* ...

For these consider the same semantic rules of C.
