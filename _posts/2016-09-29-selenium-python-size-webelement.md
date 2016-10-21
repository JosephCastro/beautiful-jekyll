---
layout: post
title: Python - Selenium - How to get the size of a WebElement
---

For get the size from a WebElement ( element from now ), you just need to access through the variable class `size`:

```
>> element.size
{'width': 100, 'height': 200}
>> element.size['width']
100
>> element.size['height']
200
```

`element.size` returns a dict with the `width` and the `height` of the element, this value is updated if there's any modification in the element.

Another way that I found is using javascript:

```
>> browser_driver.execute_script("""
   var clientRect = arguments[0].getBoundingClientRect();
   return {width: clientRect.width, height: clientRect.height};
   """, element)
{'width': 100, 'height': 200}
```

Also you can use `element.get_attribute('width')`, to get the html attribute (but this isn't reliable source)

```
>> element.get_attribute('width')
100
>> element.get_attribute('height')
200
```

Also you can get the value through css, but stay sharp, the value comes in the css type-value (an string with a px, %, auto , etc), so be careful if you want to use this way (not recommended):

```
>> element.value_of_css_property('width')
'100px'
>> element.value_of_css_property('height')
'200px'
```

In general, you can use the firsts two options, but the problem comes when Selenium (or the browser) doesn't compute the value (or is wrong), sometimes the value is lost in time and space and come with a value like `{'width': 0,'height': 0}`. In that escenario you can calculate distances with others elements, like asking for a father element size, etc, but this is for another post.







