---
title: "Swift Changes since WWDC, Part 2"
author: dwaite
date: 2015-02-03
template: article.jade
tags: swift, language
---
This is a continuation of [part 1], where I'll continue to speak to changes to
the Swift language between the WWDC announcement of Swift, and the Swift 1.1
release alongside Yosemite.

<hr />

> Note: I may be wrong about some of these features not being in the WWDC
> release; it is not possible for me to go back and double check-this list
> against earlier Swift/XCode releases. This list was created partially from
> the [Swift Programming Langauge Revision History].
>
> If you know of any corrections or additions to this list, please reach out
> to me!

1. Access control was added to the language

   At WWDC, Swift didn't have any concept of public or private classes or
   methods. The system put in place the 1.0 release is perhaps simpler than
   other languages, but still reasonable:

   | Keyword   | Behavior                                                                                               |
   |-----------|---------------------------------------------------------------
   | `private` | Visibility limited to the same file only                      
   | `internal`| Visibility is limited to the same module (app/framework)      
   | `public`  | Visibility for entities dependant on the module (framework)

   The default is `internal` if no access level is declared.

   The [Official Swift Blog] had both an article [about the addition of access
   control][Access Control Article] and developer inquiries about the [lack of
   a `protected` access control level][Protected Mode Article]. While
   `protected` in many languages allows you to have a policy of restricting
   access to certain implementation details only to subclasses, the Swift
   creators have decided that `protected` was not worth the complexity of
   adding it to the language. [^protected-note]

2. A nil coalescing operator was added

   Possibly because optionals no longer conditions, a new operator was added -
   a nil coalescing operator. An expression using the nil coalescing operator
   will give the forced value on the left unless that value is equivalent to 
   `nil`; in that case, it will give the value on the right:

   ```swift

   var foo:String? = possiblyStringOrNil()

   println( foo != nil ? foo! : "default" ) // "default" if foo doesn't have a value
   println( foo ?? "default" )              // same, using nil coalescing operator
   ```

3. Optional chaining now supports setting properties and assigning to indexes

   As many readers will already know, Swift has an optional chaining operator 
   (`?.`) to call a series of methods without checking that the results are 
   `nil`. Instead, each stage wraps its result in an optional, and a call 
   against `nil` will short circuit and result in the appropriate optional `nil`
   value for the next method in the chain.

   ```swift
   class Player {
     var location:Point
   }

   var enemy:Player? = currentEnemy()

   let oldLocation = enemy?.location
   println("Old location: \(oldLocation ?? "none")")
   enemy?.speed = 4
   ```

   The `enemy?.location` expression is analagous to the following:

   ```swift
   let oldLocation:Point?
   if let e = enemy {
     oldLocation = e.location
   }
   else {
     oldLocation = nil
   }
   ```

   If you wanted just the `x` position, you might do `enemy?.location?.x`,
   chaining from one call to the next, only evaluating if the result is nil at
   the very end.

   Since calls that wind up going against `nil` are short-circuited without any
   effect, the `enemy?.speed = 4` will do nothing unless an enemy has been set.

9. You can only add an Array to an Array

   The following code:
   
   ```swift
   var array = [1,2,3]
   array += 4
   ```
   
   Is no longer legal. You now need to do either:

   ```swift
   array.append(4)
   // or
   array += [4]
   ```
   
   Likewise, you can no longer append Characters to a String with `+=`, instead
   needing to use the `append` method. However, since there is no literal syntax
   for Character, this is less of a problem in practice.
   
10. Class-only protocols

   A protocol can be restricted such that the only implementations are classes
   (not value types like `struct`s and `enum`s). There are no examples of this
   that I know of of this in use, but you would use this when the protocol
   dictates that implementing objects should have reference semantics. 
   
   Oddly, I would have thought that Swift Core would use this for `AnyObject`.

11. Swift 1.1 rawValue on Enum

   Simple change; rather than a toRaw() method, there is now a property called
   rawValue to retrieve the raw value on enums which have such values declared.
   
12. Swift 1.1 Failable initializers

   The biggest change between Swift 1.0 (released with iOS 9) and 1.1 (released
   with Mac OS 10.10 Yosemite). This allows two new forms of initializers, both
   of which can fail.
   
   ```swift
   class MyConnection {
     func init?(address:String)    // MyConnection?, nil if address is of the wrong format
     func init!(address:MyAddress) // same returning an implicit optional (MyConnection!)
   }
   ```
   
   Along with this, significantly more Objective C Frameworks had factory and
   initializion methods exposed as standard Swift initializers.
   
   Note that blindly failing may not be the best idea, so for now you may want
   to instead do:
   
   ```swift
   func init?(address:String, error:NSErrorPointer = nil)
   ```
   
13. Swift 1.1 Function instances now fall under the Any protocol

   AnyClass will match any object which is of reference type, including all
   Objective C classes. Any matches any type, including structs and enums and
   now functions.

   ```swift
   func foo() -> (Void)->Void{
     return {}
   }
   var a1:(Void)->Void = foo()
   // same value can be assigned to an Any
   var a2:Any = foo()
   ```
   
   Note, while Any can match any type, it doesn't have any behavior, including
   equality. You'll need to cast an 'Any' type back down before it is really
   useful.

[part 1]: ../swift-changes-since-wwdc/index.md
[Official Swift Blog]: https://developer.apple.com/swift/blog/
[Access Control Article]: https://developer.apple.com/swift/blog/?id=5
[Protected Mode Article]: https://developer.apple.com/swift/blog/?id=11

[Swift Programming Langauge Revision History]: https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html#//apple_ref/doc/uid/TP40014097-CH3-ID0

[^protected-note]: One additional point that the Swift Blog only alluded to -
since you can subclass Swift classes in Objective C, they would have to define
`protected` semantics from Objective C as well - that a message is visible to a
subclass, but not to a normal Objective C caller.
