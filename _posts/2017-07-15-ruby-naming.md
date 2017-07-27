---
layout: post
title:  "Ruby naming convention"
date:   2017-07-15 19:45:31 -0300
categories: [recipes]
tags: [ruby]
author: Marcelo Foss
---
Ruby uses a unique convention for naming objects: it uses the first character of an identifier to determine its purpose.
	- Name of a method: starts with an underscore or lowercase letter
	- Parameter: starts with an underscore or lowercase letter
	- Local variable: starts with an underscore or lowercase letter
	- Global variable: starts with $
	- Instance variable: starts with @
	- Class variable: starts with @@
	- Class: starts with uppercase letter
	- Module: starts with uppercase letter
	- Constant: starts with uppercase letter
	- Symbols: starts with :

After the first character, you may combine numbers, letters, and underscores to create a unique name. However you can't place a number right after the @ symbol.
You need to divide multiword variables by placing an underscore between each word pair. Additionally you need to capitalize the first letter of each word. The name of a method may end with =, !, or ?
	
Symbols
A symbol is a constant name that doesn't require pre-declaration. Additionally, a symbol is unique 100% of the time. Symbol literals bigin with ":"