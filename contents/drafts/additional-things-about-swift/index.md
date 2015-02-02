---
title: "Additional Odd Things about Swift"
author: dwaite
date: 2015-01-25
template: article.jade
publish: false
---

= 1. Swift 2.0 will not necessarily be code compatibile with 1.0 - but you can upgrade at your leisure

= 2. Swift does not support implicit type casting, almost aggressively so

= 3. Swift enums are different beasts depending on whether you have associated values

= 4. Swift protocols are different beasts depending on whether you have typealiases

= 5. Protocols can only support optional property/method/subscript members if the protocol is @objc

= 6. AnyObject exposes every Objective C selector - and uses Optional to represent the selector isn't present

= 7. Swift functions can be overloaded by return type

= 8. Nil is a literal, but there is no single nil representation in the system. Types can instead have nil representations.

= 9. You can have nested optionals

= 10. No root object/required methods

= 11. The effects of importing Foundation

= 12. How String, Array and Dictionary cheaply support value semantics