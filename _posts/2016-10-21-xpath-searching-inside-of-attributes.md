---
layout: post
title: '[Xpath] - searching inside of attributes'
published: true
---



We want to search elements with a certain class, using this example:

```
<div class="class-1"></div>
<div class="class-1 class-2"></div>
<div class="class-1 class-2 class-3"></div>
<div class="class-2 class-11"></div>
```

We want to get the three div with the `class-1` class.


We have this options to do that:

# 1. Using a <attribute> = <value>

```
>> .//div[@class="class-1"]
<div class="class-1"></div>
```

With this we get the elements that only have `class-1`, so if an element have more classes, then this element wil be not considered, even if he have the class that we want. For this option we only get one element... we want three.


# 2. Using contains(<attribute>,<value>)

```
>> .//div[contains(@class, "class-1")]
<div class="class-1"></div>
<div class="class-1 class-2"></div>
<div class="class-1 class-2 class-3"></div>
<div class="class-2 class-11"></div>
```

Eh? We want only three elements, but now we have four and the last one doesn't have the `class-1` class.  Why? Because the `contains(...)` search inside the attribute trying to check if the `class-1` value is contained. And yes, it`s contained in the last element, `class-11` contains `class-1`.


# 3. Using contains(concat(<attribute>), <value>) 

```
>> .//div[contains(concat(" ",normalize-space(@class)," ")," class-1 ")]
<div class="class-1"></div>
<div class="class-1 class-2"></div>
<div class="class-1 class-2 class-3"></div>
```

With this, you can get the elements that have the class `class-1` (without knowing about other things).
The method [`normalize-space`](https://developer.mozilla.org/en-US/docs/Web/XPath/Functions/normalize-space), trim the string (left and right).

Just remember that you need to add spaces at the begin and the end of the string that you want to find.
