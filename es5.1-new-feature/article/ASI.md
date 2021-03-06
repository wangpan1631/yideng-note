# Automatic Semicolon Insertion (ASI)

[source](https://www.ecma-international.org/ecma-262/5.1/#sec-7.9)

Certain ECMAScript statements (empty statement, variable statement, expression statement, **do-while** statement, **continue** statement, **break** statement, **return** statement, and **throw** statement) must be terminated with semicolons. Such semicolons may always appear explicitly in the source text. For convenience, however, such semicolons may be omitted from the source text in certain situations. These situations are described by saying that semicolons are automatically inserted into the source code token stream in those situations.

## 1. Rules of Automatic Semicolon Insertion

There are three basic rules of semicolon insertion:

1. When, as the program is parsed from left to right, a token (called the offending token) is encountered that is not allowed by any production of the grammar, then a semicolon is automatically inserted before the offending token if one or more of the following conditions is true:
   * The offending token is separated from the previous token by at least one LineTerminator.
   * The offending token is }.
2. When, as the program is parsed from left to right, the end of the input stream of tokens is encountered and the parser is unable to parse the input token stream as a single complete ECMAScript Program, then a semicolon is automatically inserted at the end of the input stream.
3. When, as the program is parsed from left to right, a token is encountered that is allowed by some production of the grammar, but the production is a restricted production and the token would be the first token for a terminal or nonterminal immediately following the annotation “[no LineTerminator here]” within the restricted production (and therefore such a token is called a restricted token), and the restricted token is separated from the previous token by at least one LineTerminator, then a semicolon is automatically inserted before the restricted token.

However, there is an additional overriding condition on the preceding rules: a semicolon is never inserted automatically if the semicolon would then be parsed as an empty statement or if that semicolon would become one of the two semicolons in the header of a for statement ([see 12.6.3](https://www.ecma-international.org/ecma-262/5.1/#sec-12.6.3)).

**NOTE** The following are the only restricted productions in the grammar:

> *PostfixExpression* :  
> &emsp;LeftHandSideExpression [no LineTerminator here] ++  
> &emsp;LeftHandSideExpression [no LineTerminator here] --  
> 
> *ContinueStatement* :  
> &emsp;**continue** [no LineTerminator here] Identifier ;
> 
> *BreakStatement* :  
> &emsp;**break** [no LineTerminator here] Identifier ;
>
> *ReturnStatement* :  
> &emsp;**return** [no LineTerminator here] Expression ;
>
> *ThrowStatement* :  
> &emsp;**throw** [no LineTerminator here] Expression ;

The practical effect of these restricted productions is as follows:

When a ++ or -- token is encountered where the parser would treat it as a postfix operator, and at least one LineTerminator occurred between the preceding token and the ++ or -- token, then a semicolon is automatically inserted before the ++ or -- token.

When a continue, break, return, or throw token is encountered and a LineTerminator is encountered before the next token, a semicolon is automatically inserted after the continue, break, return, or throw token.

The resulting practical advice to ECMAScript programmers is:

A postfix ++ or -- operator should appear on the same line as its operand.

An Expression in a return or throw statement should start on the same line as the return or throw token.

An Identifier in a break or continue statement should be on the same line as the break or continue token.

## 2. Examples of Automatic Semicolon Insertion

The source

`{ 1 2 } 3`

is not a valid sentence in the ECMAScript grammar, even with the automatic semicolon insertion rules. In contrast, the source

```js
{ 1
2 } 3
```

is also not a valid ECMAScript sentence, but is transformed by automatic semicolon insertion into the following:

```js
{ 1
;2 ;} 3;
```

which is a valid ECMAScript sentence.

The source

```js
for (a; b
)
```

is not a valid ECMAScript sentence and is not altered by automatic semicolon insertion because the semicolon is needed for the header of a for statement. Automatic semicolon insertion never inserts one of the two semicolons in the header of a for statement.

The source

```js
return
a + b
```

is transformed by automatic semicolon insertion into the following:

```js
return;
a + b;
```

NOTE The expression a + b is not treated as a value to be returned by the return statement, because a LineTerminator separates it from the token return.

The source

```js
a = b
++c
```

is transformed by automatic semicolon insertion into the following:

```js
a = b;
++c;
```

NOTE The token ++ is not treated as a postfix operator applying to the variable b, because a LineTerminator occurs between b and ++.

The source

```js
if (a > b)
else c = d
```

is not a valid ECMAScript sentence and is not altered by automatic semicolon insertion before the else token, even though no production of the grammar applies at that point, because an automatically inserted semicolon would then be parsed as an empty statement.

The source

```js
a = b + c
(d + e).print()
```

is not transformed by automatic semicolon insertion, because the parenthesised expression that begins the second line can be interpreted as an argument list for a function call:

`a = b + c(d + e).print()`

In the circumstance that an assignment statement must begin with a left parenthesis, it is a good idea for the programmer to provide an explicit semicolon at the end of the preceding statement rather than to rely on automatic semicolon insertion.