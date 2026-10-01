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
* `print(...)`, `read(...)` and similar simple built-ins
* `'a'` character literals
* `"hello"` string literals
* `//` and `/* ... */` comments


## Grammar

### Tokens used

```
INTEGER    = [0-9]+
FLOAT      = [0-9]+\.[0-9]+([eE][+-]?[0-9]+)?
IDENTIFIER = [A-Za-z][A-Za-z0-9_]*
CHAR       = '([^'\\]|\\.)'
STRING     = "([^"\\]|\\.)*"
```
--- 
### Program structure

```ebnf
program ::= { declaration } ;

declaration ::= variable_declaration
              | function_definition ;
```

### Types

```ebnf
type ::= "int"
       | "double"
       | "char"
       | "bool" ;
```

### Declarations

```ebnf
variable_declaration ::= variable_declaration_core ";" ;

variable_declaration_core ::= type IDENTIFIER
                              [ "=" expression ]
                            | type IDENTIFIER
                              "[" INTEGER "]"
                              [ "=" array_initializer ] ;

array_initializer ::= "{"
                        [ expression { "," expression } ]
                      "}" ;
```


### Functions

```ebnf
function_definition ::= return_type IDENTIFIER
                        "(" [ parameter_list ] ")"
                        block ;

return_type ::= type
              | "void" ;

parameter_list ::= parameter { "," parameter } ;

parameter ::= type IDENTIFIER ;
```


### Statements

```ebnf
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
```

```ebnf
block ::= "{"
            { statement }
          "}" ;
```

```ebnf
assignment ::= IDENTIFIER "=" expression
             | IDENTIFIER "[" expression "]" "=" expression ";" ;

if_statement ::= "if" "(" expression ")"
                 statement
                 [ "else" statement ] ;

while_statement ::= "while" "(" expression ")"
                    statement ;

for_statement ::= "for"
                  "("
                    [ for_initializer ]
                    ";"
                    [ expression ]
                    ";"
                    [ expression ]
                  ")"
                  statement ;

for_initializer ::= variable_declaration_core
                  | assignment ;

return_statement ::= "return" [ expression ] ";" ;

print_statement ::= "print" "(" expression ")" ";" ;

read_statement ::= "read" "(" IDENTIFIER ")" ";" ;

```


### Expressions

```ebnf
expression ::= logical_and
               { "||" logical_and } ;

logical_and ::= equality
                { "&&" equality } ;

equality ::= comparison
             { ("==" | "!=") comparison } ;

comparison ::= additive
               { ("<" | "<=" | ">" | ">=") additive } ;

additive ::= multiplicative
             { ("+" | "-") multiplicative } ;

multiplicative ::= unary
                   { ("*" | "/" | "%") unary } ;

unary ::= [ "+" | "-" | "!" ] postfix ;

postfix ::= primary
          | primary "[" expression "]"
          | function_call ;

function_call ::= primary
                  "(" [ argument_list ] ")" ;

argument_list ::= expression { "," expression } ;

primary ::= INTEGER
          | FLOAT
          | CHAR
          | STRING
          | "true"
          | "false"
          | IDENTIFIER
          | "(" expression ")"
          | "(" type ")" expression ;
```

## Example programs

The defined example programs can be found in the [examples folder](examples/).

## Additional notes and considerations

### 1. Expressions and precedence

From **lowest to highest precedence**:

| Level | Operators                   | Associativity |
| ----: | --------------------------- | ------------- |
|     1 | `||`                        | left          |
|     2 | `&&`                        | left          |
|     3 | `==`, `!=`                  | left          |
|     4 | `<`, `<=`, `>`, `>=`        | left          |
|     5 | `+`, `-`                    | left          |
|     6 | `*`, `/`, `%`               | left          |
|     7 | unary `+`, `-`, `!`         | right         |
|     8 | function call, indexing     | -----         |

### 2. Lexical considerations

Whitespace and comments are ignored:

WHITESPACE    = [ \t\r\n]+
LINE_COMMENT  = "//" ... "\n"
BLOCK_COMMENT = "/*" ... "*/"

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
