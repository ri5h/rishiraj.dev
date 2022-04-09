---
title: "Stop Using in_array when possible"
date: 2021-02-10T09:09:37+01:00
draft: false
---
<br/>

## What is `in_array()`
[in_array() is a standard function in php](https://www.php.net/manual/en/function.in-array.php) which can be used to check if a value exists in array, duh! but sometimes, when the array size is relativly large this function can take a long time `O(n)`.

## Example
```php
$os = array("Mac", "NT", "Irix", "Linux");
if (in_array("Irix", $os)) {
    echo "Got Irix";
}
if (in_array("mac", $os)) {
    echo "Got mac";
}
?>
```

## The Argument

While it is quite easy to understand and use it as its intended but the problem comes when the size of the array is huge and use it for something as menial as finding if something exists in array. For a relatively smaller array will not affect the performance and thus you will never face the problem untill the system grows very big and you cannot figure why suddenly the response from server is very slow.

It is very important to understand the pitfalls and then make an educated decision as to weather you should use `in_array()` or not. For example, if the array contains status of a github pull request then you can confidently say that the number of status in array will almost never be so big that it becomes a problem. In that case, it is okay to use `in_array()`, just be sure not to be stupid enough to check for inverse unless necessary.
```php
// The catch is that this array will probably never be big enough to cause a problem 
CONST POSSIBLE_STATES = ['draft', 'open', 'merged', 'closed'];

$pr_state = $this->getStateFromRequest();

//Bad Example : checks against all states, guranteed to take O(n) time
if (!in_array($pr_state, self::POSSIBLE_STATES)) {
    throw new InvalidArgumentException('Invalid state for a pr');
}

//Good Example : returns as soon as first match is found, worst case scenario is O(n) time
if(in_array($pr_state, self::POSSIBLE_STATES)) {
    // do some stuff with a valid state
}

//optionally throw exception here if you have to
throw new InvalidArgumentException('Please check your input and try again');

```

## The Alternative

One way to look at the problem is to see if you can do anything to the array itself so that you don't have to go through each element one by one (`in_array()`) and instead just directly try to check if an element is there. For a simple array, it is possible.

You can use another function called [`array_flip()`](https://www.php.net/manual/en/function.array-flip.php) which makes the array keys into values and values into keys. Then use [`array_key_exists()`](https://www.php.net/manual/en/function.array-key-exists.php) or [`isset()`](https://www.php.net/manual/en/function.isset.php) for a lookup that is almost `O(1)`.

If you are worries that a single dimensional array doesn't have keys to begin with so how will it work ?
- Internally php array always have keys and values even a one dimensional array has keys from `0-n` generally called as index.
- Flip will use those numbers or indices as values.


**Considerations:**
- The flip happens in place (original array gets updated) so you do not use extra space.
- Be aware that the resulting array will remove duplicate values because using them as keys mean they must be unique.
- `isset()` will evaluate the value of the key so it will return false if that value is null.
- `array_key_exists()` will only check if the key exists in the array.

**Example**
```php

$states = ['draft', 'open', 'merged', 'closed'];
$pr_state = $this->getStateFromRequest();
if (array_key_exists($pr_state , array_flip($states))) {
    // do some stuff with valid state
}

//bonus, the negative check also takes constant time
if (!array_key_exists($pr_state , array_flip($copystates))) {
    throw new InvalidArgumentException('Invalid state for a pr');
}
```


## Notes:
1. While this is a useful method be sure that you can actually face the problem before trying to over-engineer simple situations. For example, this logic is better used against a list of usernames than a list of user_roles.
2. When the array is fairly complex, it serves better to first make the array simple and then checking what the result of `array_flip()` will be.