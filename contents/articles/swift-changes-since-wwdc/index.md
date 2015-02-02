---
title: "Swift Changes Since WWDC, Part 1"
author: dwaite
date: 2015-02-01
template: article.jade
tags: swift, language
---

I have been enjoying learning the Swift Language so far this year. It is quite
interesting how Apple could create a language with such a strong emphasis on
compatibility (or rather, supporting compatibility) with existing Frameworks and
Objective C style of usage, yet be such a different language than Objective C
as you really investigate it.


However, the language is very young, and has quite a few odd behaviors. These
may be wrinkles in the language, or they may just be things that Apple has yet
to iron out. However, as I play around with creating new apps and frameworks
using this language, I decided I should document what I find in the case that it
is interesting or helpful for others. 

<hr />

> Note: This is part 1 of a two part series. You may also be interested in [part 2].

## Swift has changed since WWDC

Swift 1.0 was released alongside iOS 9, with 1.1 being released alongside
Yosemite. There were a lot of features added and syntax changed between the
WWDC beta and 1.0. 

My feeling is that Apple actually made more changes to the language between
WWDC and 1.0 than they originally planned to - the number of differences over
a period of a few months is quite striking, especially considering the tools
team needed to audit existing Apple frameworks and finish up XCode 6 as well.

If I had to guess, the team at Apple decided to try to prioritize changes that
affected syntax for 1.0, so that they would not need to make as many breaking
syntax changes before Swift 2.0 (which I'll talk about/speculate on in a future
article.)

Apple publishes all the videos at WWDC for developers to view, which is a great
way to get familiar with Swift - but it helps to know what has changed in the
language so you can follow along. I'll cover the changes which I know affect
WWDC content in this part, while other changes (including new features) will be
in [part 2].

1. Array and Dictionary syntax changed

   In the code samples from WWDC, you'll see arrays written as follows:

   ```swift
   var array:Int[] = [1,2,3]
   ```

   This syntax has changed, and is now:

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
   
   This is actually a very nice feature in Swift compared to Objective C. It
   can be painful to write robust code for reuse with value semantics, because
   you do not have guarantees that the caller will not change the object later,
   after you return from the call. Indeed, you do not have guarantees that the
   object isn't currently being modified on a separate thread!
   
   So in Objective C, you have the `copy` attribute on properties to instruct
   that a copy of the object is saved on set, rather than the object directly. 
   `NSCopying` also dictates that copies of types with immutable variants
   (`NSString` vs `NSMutableString`) will be immutable. This allows the copies
   to be returned from a property getter directly, rather than having to `copy`
   again.
   
   Instead, value semantics means that every assignment of a Swift string, array
   or dictionary is a new copy. The developer does not have to worry about
   explicitly copying to make more robust code.
   
   I'll likely talk in a future article about how Swift manages to make these
   value type copies nearly free.

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

   To prevent a whole class of confusing errors (including Bool? and Bool!
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
   
[Declaration modifiers]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Declarations.html#//apple_ref/doc/uid/TP40014097-CH34-ID381
[part 2]: ../swift-changes-since-wwdc-2/index.md

[^nested-optional-note]: Note that you can still have nested optionals in the
language, so you (at least presently) have to pay attention to your types to
make sure comparisons to `nil` mean what you expect.

