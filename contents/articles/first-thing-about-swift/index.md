---
title: "Swift Language Changes, Part 1"
author: dwaite
date: 2015-01-25
template: article.jade
tags: swift, language
---

I have been learning the Swift Language so far this year, and it has been
somewhat enjoyable. However, the language is very young and has a few behaviors
which _may_ become wrinkles as it gets older, or may smooth themselves out.
So, I decided to post about a dozen things with Swift that you may or may not
know about.

I'll start with #1, split into two parts

## Swift 1.1 is different than what was shown at WWDC

Swift 1.0 was released alongside iOS 9, with 1.1 being released alongside
Yosemite. There were a lot of features added and syntax changed between the
WWDC beta and 1.0. Apple publishes all the videos at WWDC for developers to
view, which is a great way to get familiar with Swift - but it helps to know
what has changed in the language so you can follow along.

I'll cover the changes which I know affect WWDC content in this part, while
other changes (including new features) will be in part 2.

1. Array and Dictionary syntax changed

   In the code samples from WWDC, you'll see arrays written as follows:

   ```swift
   var array:Int[] = [1,2,3]
   ```

   This is now:

   ```swift
   var array:[Int] = [1,2,3]
   ```

   Dictionaries are also written as:
   ```swift
   var dict:[String:String] = ["foo":"bar"]
   ```

2. Arrays are now value types (like String). 

   If you do the following:

   ```swift
   var array = [1,2,3]
   var secondArray = array
   array.append(4)
   
   print(secondArray) // [1, 2, 3]
   ```
   
   You'll observe the assignment has value/copy semantics, not reference
   semantics. The `secondArray` behaves like a new copy of the array, and does
   not reflect changes that happen after the fact.
   
   Likewise, if you had typed `let array = [1,2,3]`, the append would generate
   a compile error since that reference is considered immutable.

3. Syntax for ranges changed

   Swift supports two types of ranges, declared via the "Closed Range Operator"
   and the "Half-Open Range Operator".

   For WWDC:
   ```swift
   var closed   = 1...5 # Represents 1, 2, 3, 4, 5
   var halfopen = 1..5  # Represents 1, 2, 3, 4 
   ```

   to clarify the syntax (likely both for developers and the compiler), the 
   half-open operator changed for 1.0:

   ```swift
   var halfopen = 1..<5 # Represents 1, 2, 3, 4
   ```

4. Optionals (and ErrorPointer) are no longer conditions

   In several swift videos, you'll have statements like the following:

   ```swift
   var a:String?
   ...
   if a {
     ...
   }
   ```

   To prevent a whole class of confusion (possibly due to Bool? and Bool!
   results), you must now compare explicitly against the `nil` literal
   [^nested-optional-note]

   ```swift
   var a:String?
   ...
   if a != nil {
     ...
   }
   ```

5. Several @attributes became keywords

   In particular, the @required, @optional, @lazy, @final, @prefix and @postfix
   all dropped the @ and became [Declaration modifiers]. The @infix attribute
   went away completely - infix is the default for operator overloads.
   
[Declaration modifiers]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Declarations.html#//apple_ref/doc/uid/TP40014097-CH34-ID381
[^nested-optional-note]: Note that you can still have nested optionals in the
language, so you (at least presently) have to pay attention to your types to
make sure comparisons to `nil` mean what you expect.

