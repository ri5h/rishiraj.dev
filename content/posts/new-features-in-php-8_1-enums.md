---
title: "New Features in Php 8.1 - Enums"
date: 2021-02-10T09:09:37+01:00
draft: true
---
<br/>

# Description
An Enumerated type or enum is a data type that is used to demote a collection of specific type of data. It can contain a group of named values (also called enumerators of type).

We can assign any of the allowed legal values to the variables in enum and any function can also type check against the enumerated type.

## - rfc Details
- *Link:* [https://wiki.php.net/rfc/enumerations](https://wiki.php.net/rfc/enumerations)
- *Authors:* Larry Garfield, Ilija Tovilo
- *Status:* In Voting(date of writing)
- *Target Version:* PHP 8.1
- *Implementation:* [https://github.com/php/php-src/pull/6489](https://github.com/php/php-src/pull/6489)

## - enum syntax:
```php
enum Color {
    case Red;
    case Green;
    case Blue;
}
```
<br/>

# Diving deep
Enums can be created by using the `enum` keyword and name of the Enum after it. It can declare `string` or `int` and can implement interfaces or extend a class. During the parsing, a new token named `T_NUM` is assigned a value of `369`.

Enumerations are built on top of objects and classes and thus will pass an *object* type check. when it comes to namespace they share it with other classes, traits and interfaces.


## - using types with `enum`
```php
enum Color: string {
    case Red = 'red';
    case Green = 'green';
    case Blue = 'blue';
}
```
## - implementing a interface
```php
enum Color: string implements ColorInterface {
    case Red = 'red';
    case Green = 'green';
    case Blue = 'blue';
}
```

## - new `enum_exists` function
Along with enum we also will have a new function to check weather the enum being specifies exists or not.
```php
function  enum_exists(string $enum, bool $autoload = true): bool {}
```

## - new interface `UnitEnum`
```php
// Implicitly implemented by engine
// Cannot be implemented by user
interface UnitEnum
{
    // returns an array of all poossible cases
    // Cannot be declared inside an Enum
    public static function cases(): array;
}
```

## - new interface `ScalarEnum`
```php
// Implicitly implemented by engine when enum values are scalar
// Cannot be implemented by user
interface ScalarEnum extends UnitEnum
{
    // Cannot be declared inside an Enum
    public static function from(int|string $value): static;
    public static function tryFrom(int|string $value): ?static;
}
```

## Magic constants
Enums because they are subtype of classes support magic contants including `::class` , `__CLASS__` , `__FUNCTION__`, `__METHOD__` . They also behave as a standard php object and have functions like `get_class`, `is_a`, `is_object`.

<br/>

# When can we expect it?
While the RFC is currently in voting (as of date of writing). We already know that there is a strong support for it. The target for the RFC is php 8.1 which we can expect to be released around November 2021. Check out the current milestone here:  [php/php-src Milestones](https://github.com/php/php-src/milestone/3) which says its `58%` complete at the time of writing.

<br/>

# Current Situation
Enums are generally used to represt named values in a structured and typed ways instead of directly using string values.

Instead of native enum type a [SPL Implementation](https://www.php.net/manual/en/class.splenum.php) have existed for a long time. Along with a few packages that can be used to implement/use enum types in php.

1. [spatie/enum](https://github.com/spatie/enum) by [spatie](https://docs.spatie.be/enum/v3/introduction)
```php
// Creating
use \Spatie\Enum\Enum;

/**
 * @method static self red()
 * @method static self green()
 * @method static self blue()
 */
class ColorEnum extends Enum
{
}

// Using
public function setColor(ColorEnum $color): void
{
    $this->color = $color;
}

// ...

$class->setColor(ColorEnum::green());
```

2. [myclabs/php-enum](https://github.com/myclabs/php-enum) by [Matthieu Napoli](https://twitter.com/matthieunapoli)
```php
class ColorEnum extends Enum
{
    private const RED = 'red';
    private const GREEN = 'green';
    private const BLUE = 'blue';
}

class Backgroud
{
    public function setColor(string $color): void
    {
        $this->color = $color;
    }
}

// Using magic methods for typesafety and restricting allowed values
$background->setColor(ColorEnum::RED());
```
3. [And a whole lot of others](https://github.com/topics/php-enum)

## Before You Leave

Did you liked the article? is something missing or wrong? Send a DM on twitter where I regularly post about new articles and videos I make about tech news. [Twitter](https://twitter.com/ri5hirajp)
