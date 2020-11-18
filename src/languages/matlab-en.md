# Matlab (EN)

Matlab is a scientific computing language, actually not a "programing" language.
Matlab is implemented by Java language and speed up by the kernel written in C++ / Cuda.

Although Matlab implements the object-oriented concept, it is rarely used.
Therefore, most of them are operated by pure functions.

This section will teach you familiar with Matlab development environment and programming methods.

## Environment

### Working Directory

The current path is set as working directory by default.
In the working directory, any function names can be called from separate matlab scripts.
Whether it is calling from a script file or console.

Matlab has no import syntax, so if a function is in the working directory,
it can be called directly.
The built-in functions and names in Matlab are included in working directory at begining.

### Working Space

The working space will store and update local variables during execution.
After run once, the reference expression may use previous values.

## Syntax

+ Continuous blank is same as one blank.
+ Indents (leading blanks) will not affect the commands.
+ Line breaks (new line symbols) will be considered in grammar.
    + Use "`...`" symbol to break a line manually at breakable position (like blank).
    + You can break a line without "`...`" symbol if there has "`;`" symbol.
    + In another words, you can join multiple lines into one line if there has "`;`" symbols.

Here is line break example:

```matlab
% Manually line break
a = 10 ... Comment
    * (20 + 70)... Another comment
    + 30

b = [1, 2, 3;  % Comment
     4, 5, 6;  % Another comment
     7, 8, 9]

clc; close all; clear  % Join three lines (not recommend)
```

### Comment

The comment syntax is a leading "`%`" symbol,
all after characters will be ignored until a new line is started.

```matlab
% Comment!
% ignored = 'help!';
worked_function
```

### Assignment

A value can be assigned to a name, which is called "variable".
There is no any constraint mechanism of "constants" in Matlab,
so every names are mutable (editable) and shadowable (coverable).

```matlab
a = 10
```

The basic syntax is same as Mathematical syntax with a "`=`" operator.
The left side is variable name and the right side is an expression.
"Expression" means a calculable or derivable value,
including literal value, name reference, function calling and operator using.

Each assignment syntax will be printed out in console.
To depress the output, add a "`;`" symbol after it.

```matlab
a = sin(deg2rad(60));
```

### Array

"Array" is the base value type in Matlab.
Even a single number is an 1x1 array.

1D and 2D array are supported by built-in literal value.
The 1D array value can be splited by comma "`,`" symbols or space.
And 2D rows can be splited by semicolon "`;`" symbols.
If there are any operation expressions here, strongly not recommend use space as 1D spliter,
because it will mess up the operators.

```matlab
10 + 30  % 1x1 array
[5, 5]  % 2x1 array
[5 5]  % 2x1 array, splited by space
[1, 2; 3, 4]  % 2x2 array
```

Array will flat embed into another array automatically.

```matlab
a = [1, 2, 3];
b = [a; a]  % [1, 2, 3; 1, 2, 3]
```

Array has most operator in Matlab.
Some of the operator is element-wise, the others are matrix-wise.
There is also has transpose operator for the 2D array.

```matlab
[1, 1; 2, 2] * 2  % Element-wise [2, 2; 4, 4]
[1, 1; 2, 2] * [2, 2; 3, 3]  % Matrix-wise [5, 5; 10, 10]
[1, 1; 2, 2] .* [2, 2; 3, 3]  % Element-wise operator [2, 2; 6, 6]
[1, 1; 2, 2] .* 2  % Element-wise operator is also suitable
[1, 1; 2, 2]'  % Trailing operator (transpose) [1, 2; 1, 2]
```

### Control Sequence

Basic control sequence like "if else", "for" loop or "while" loop are supported.
A "`end`" keyword is used to close the code block of the control sequence.

```matlab
if i > 20
    i = i + 1;
elseif i == 5
    i = i^2;
else
    i = 20;
end
```

```matlab
counter = 0;
for i = 1:30
    counter = counter + i
    if counter // 7
        break
    end
end
```

```matlab
stop = true;
while stop
    stop = ~stop;
end
```

### Function

Function is a set of commands that requires specific input variables and returns specific variables.
Unlike control sequence, indentation is usually not used in functions.

Matlab does not have a `return` syntax with values.
The return values need to define their names.
You can define them with the "`=`" symbol in the function definition.

```matlab
% Use function
get_3 = plus(1, 2)  % 3
[get_2, get_1] = return_two(true, 1, 2)  % 2 1

% Function definition
function c = plus(a, b)
c = a + b;
end

% Multiple return values
function [a, b] = return_two(condition, a, b)
if condition
    a = b;
    b = a;
end
end
```

A function without input value or return value:

```matlab
% Use function
hello  % Calling function without input value
@hello  % Function handle

% Function definition
function hello
fprintf('hello world!\n')
end
```
