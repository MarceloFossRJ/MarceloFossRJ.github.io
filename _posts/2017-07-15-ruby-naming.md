---
layout: post
title:  "Ruby naming convention"
date:   2017-07-15 19:45:31 -0300
categories: [guide]
tags: [ruby]
author: Marcelo Foss
---
Ruby uses a unique convention for naming objects:
it uses the first character of an identifier to determine its purpose.  
Certain names, are reserved words and should not be used as variable, method, class, or module name. See the section 'Reserved words' below.   
Lowercase letter means the characters "a" through "z".  
Uppercase letter means the characters "A" through "Z".
A digit means "0" through "9".  

A name is an uppercase letter, lowercase letter, or an underscore ("_"), followed by Name characters (this is any combination of upper- and lowercase letters, underscore and digits).

The Ruby convention is to use underscores to separate words in a multiword method or variable name.  
By convention, most constants are written in all uppercase with underscores to separate words, LIKE_THIS.  
Ruby class and module names are also constants, but they are conventionally written using initial capital letters and camel case, LikeThis.

After the first character, you may combine numbers, letters, and underscores to create a unique name.  
However you can't place a number right after the @ symbol.  
You need to divide multiword variables by placing an underscore between each word pair.   
Additionally you need to capitalize the first letter of each word.  
The name of a method may end with =, !, or ?

**Symbols**  
A symbol is a constant name that doesn't require pre-declaration. Additionally, a symbol is unique 100% of the time. Symbol literals begin with ":"


### Variables
**Local variable:**  
  starts with an underscore or lowercase letter  

**Global variable:**  
  starts with $  

**Instance variable:**  
  starts with @  

**Class variable:**  
  starts with @@  

### Methods
**Name of a method:**  
  starts with an underscore or lowercase letter  

**Parameter:**  
  starts with an underscore or lowercase letter  

**Class:**  
  starts with uppercase letter, by convention they are named using MixedCase  

**Module:**  
  starts with uppercase letter, by convention they are named using MixedCase  

### Constants
**Constant:**  
  starts with uppercase letter  

**Symbols:**  
  starts with :  


### Reserved words

| Reserved Word     | Description |  
| :---------------- | :---------- |
| BEGIN             | Code, enclosed in { and }, to run before the program runs. |  
| END               | Code, enclosed in { and }, to run when the program ends. |  
| alias             | Creates an alias for an existing method, operator, or global variable. |  
| and               | Logical operator; same as && except and has lower precedence. |  
| begin             | Begins a code block or group of statements; closes with end. |  
| break             | Terminates a while or until loop or a method inside a block. |  
| \case             | Compares an expression with a matching when clause; closes with end. |  
| class             | Defines a class; closes with end. |  
| def               | Defines a method; closes with end. |  
| defined?          | Determines if a variable, method, super method, or block exists. |  
| do                | Begins a block and executes code in that block; closes with end. |  
| else              | Executes if previous conditional, in if, elsif, unless, or when, is not true. |  
| elsif             | Executes if previous conditional, in if or elsif, is not true. |  
| end               | Ends a code block (group of statements) starting with begin, def, do, if, etc. |  
| ensure            | Always executes at block termination; use after last rescue. |  
| false             | Logical or Boolean false, instance of FalseClass. (See true.) |  
| for               | Begins a for loop; used with in. |  
| if                | Executes code block if true. Closes with end.  |  
| module            | Defines a module; closes with end. |  
| next              | Jumps before a loop's conditional. |  
| nil               | Empty, uninitialized variable, or invalid, but not the same as zero; object of NilClass. |  
| not               | Logical operator; same as !. |  
| or                | Logical operator; |  
| redo              | Jumps after a loop's conditional. |  
| rescue            | Evaluates an expression after an exception is raised; used before ensure. |  
| retry             | Repeats a method call outside of rescue; jumps to top of block (begin) if inside rescue. |  
| return            | Returns a value from a method or block. May be omitted. |  
| self              | Current object (invoked by a method). |  
| super             | Calls method of the same name in the superclass. The superclass is the parent of this class. |  
| then              | A continuation for if, unless, and when. May be omitted. |  
| true              | Logical or Boolean true, instance of TrueClass. |  
| undef             | Makes a method in current class undefined. |  
| unless            | Executes code block if conditional statement is false. |  
| until             | Executes code block while conditional statement is false. |  
| when              | Starts a clause (one or more) under case. |  
| while             | Executes code while the conditional statement is true. |  
| yield             | Executes the block passed to the method. |  
| _ _FILE_ _        | Name of current source file. |  
| _ _LINE_ _        | Number of current line in the current source file. |  
