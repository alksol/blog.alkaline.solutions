---
title: "Swift Language Changes, Part 2"
author: dwaite
date: 2015-01-25
template: article.jade
tags: swift, language
---
In this part, I'll continue on changes between the WWDC release of Swift, 
and Swift 1.1 which was released alongside Yosemite.

1. Access control was added to the language

   At WWDC, Swift didn't have any concept of public or private classes or
   methods. The system put in place the 1.0 release is perhaps simpler than
   other languages:

   * `private`  only allows entity (method, class, etc) access if in the same 
                file
   * `internal` allows more than private, giving entity access to the module
                (framework/app) the entity is declared in
   * `public`   allows more than internal, giving access to everything outside
                the module the entity is declared in

   The default is `internal` if no access level is declared.

   The [Official Swift Blog] had both an article [about the addition of access
   control][Access Control Article] and developer inquiries about the [lack of
   a `protected` access control level][Protected Mode Article]. While
   `protected` in many languages allows you to have a policy of restricting
   access to certain implementation details only to subclasses, the Swift
   creators have decided that `protected` was not worth the complexity of
   adding it to the language. [^protected-note]

2. A nil coalescing operator was added

   Partly because optionals no longer conditions, a new operator was added:

   ```swift

   var foo:String? = possiblyStringOrNil()

   println( foo != nil ? foo! : "default" ) // print "default" if foo doesn't have a value
   println( foo ?? "default" )              // same, using nil coalescing operator
   ```

3. Optional chaining was expanded to support setting properties and assigning 
   to indexes

   Swift has an optional chaining operator (?.) to call a series of methods
   without checking that the results are nil. Instead, each stage wraps its
   result in an optional, and a call against 'nil' will short circuit and
   result in the appropriate optional nil value for the next method in the
   chain.

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

   If you wanted just the 'x' position, you might do `enemy?.location?.x`,
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
   
10. Initializers in protocols

   Protocols can now declare initializers. For example,
   
   ```swift
   protocol NSCoding { 
     func encodeWithCoder(aCoder: NSCoder)
     init(coder aDecoder: NSCoder)
   }
   ```
   
   On implementation, this initializer then becomes one of the required
   initializers for any subclasses.
   
11. Class-only protocols

   A protocol can be restricted such that the only implementations are classes
   (not value types like `struct`s and `enum`s). There are no examples of this
   that I know of in Swift core, but you would use this when the protocol
   dictates that implementing objects should have reference semantics.

12. Swift 1.1 rawValue on Enum

   Simple change; rather than a toRaw() method, there is now a property called
   rawValue to retrieve the raw value on enums which have such values declared.
   
13. Swift 1.1 Failable initializers

   The biggest change between Swift 1.0 (released with iOS 9) and 1.1 (released
   with Mac OS 10.10 Yosemite). This allows two new forms of initializers, both
   of which can fail.
   
   ```swift
   class MyConnection {
     func init?(address:String)    // returns MyConnection?, nil if address is of the wrong format
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

14. Swift 1.1 Function instances now fall under the Any protocol

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
   equality. You'll need to cast an 'Any' type back down before it is really useful.
    
[Official Swift Blog]: https://developer.apple.com/swift/blog/
[Access Control Article]: https://developer.apple.com/swift/blog/?id=5
[Protected Mode Article]: https://developer.apple.com/swift/blog/?id=11

[^protected-note]: One additional point that the Swift Blog only alluded to -
since you can subclass Swift classes in Objective C, they would have to define
`protected` semantics from Objective C as well - that a message is visible to a
subclass, but not to a normal Objective C caller.