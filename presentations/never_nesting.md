# Never Nesting

## Introduction

Despite the name, the Never Nesting approach does not mean that we shouldn't ever write nested code, but that we should limit the levels of nesting as much as possible.

Heavily nested code can be very difficult to read and often leads to complications in maintenance and refactoring and potentially even introduction of bugs.

There are two main concepts that help us to declutter our code and minimize nesting.

> * Extraction - Take parts of heavily nested methods and extract them into standalone methods
>
> * Inversion - Flip conditions and use the early exit method

There are several lines of thought into the number of levels that are acceptable, many would say beyond level 4 is where code becomes truly unreadable, others would argue that even level 3 is too much.

But the general premise is that whenever code seems unreadable or complex with lots of nesting it should be refactored. It also promotes other positive software engineering practices in which methods should have, where possible, single responsibility.

## Example

Here is an example that has 4 levels of nesting if we consider each open brace a new level;

```cs
int Calculate( int bottom, int top )
{ // 1
    if( top > bottom )
    { // 2
        int sum = 0;


        for( int i = bottom; i <= top; i++ )
        { // 3
            if( i % 2 == 0 )
            { // 4
                sum += i;
            }
        }

        return sum;
    }
    else
    {
        return 0;
    }
}
```

## Extraction

Firstly, we can extract the inner part of the loop into it's own method. This also allows us to separate out code that could also be duplicated in other areas making it available to the other methods in our class.

```cs
int FilterNumber( int number )
{
 if( number % 2 == 0 )
 {
  return number;
 }

 return 0;
}

int Calculate( int bottom, int top )
{
    if( top > bottom )
    {
        int sum = 0;

        for( int i = bottom; i <= top; i++ )
        {
            sum += FilterNumber( i );
        }

        return sum;
    }
    else
    {
        return 0;
    }
}
```

Note:

This is a basic example and arguably we could also reduce the nesting in this case by using ternary operators without the need for extraction. e.g.

```cs
...
    for( int i = bottom; i <= top; i++ )
    {
        sum += i % 2 == 0 ? i : 0;
    }
...
```

However, it could also be argued that this is not always the most immediately readable and understandable code. We have to bear in mind that simplifying code to one line is not always more readable.

In addition, the inner loop code may be more complex removing this particular shorthand as an option to begin with.

## Inversion

Secondly, we can move the "unhappy" path to the top of the method. Leading with the "happy" path, i.e. "if this then that" can generally lead to further nesting.

We flip our if..else by inverting the condition:

```cs
int FilterNumber( int number )
{
    if( number % 2 == 0 )
    {
        return number;
    }


    return 0;
}

int Calculate( int bottom, int top )
{
    if( top <= bottom )
    {
        return 0;
    }
    else
    {        
        int sum = 0;

        for( int i = bottom; i <= top; i++ )
        {
            sum += FilterNumber( i );
        }

        return sum;
    }
}
```

And now seeing as we just return straight away if our new condition is matched, we can remove the else statement.

This is called "Early Exit" or "Return Early" and the idea is that we want to escape a method as quickly as possible if something is likely to go "wrong", e.g. our inputs do match a given "happy" condition.

```cs
int FilterNumber( int number )
{
    if( number % 2 == 0 )
    {
        return number;
    }


    return 0;
}

int Calculate( int bottom, int top )
{ // 1
    if( top <= bottom )
    { // 2
        return 0;
    }

    int sum = 0;

    for( int i = bottom; i <= top; i++ )
    { // 2
        sum += FilterNumber( i );
    }

    return sum;
}
```

Now we've flattened our method to 2 levels. The "unhappy" path of our code acts as validation or guarding for the input arguments of our method and we return early if those conditions are not met. Then our "happy" path executes if we get past this early validation.

## Notes

Further examples and more information are provided in the "Sources" section of this page.

These are not hard and fast rules and it is not always possible to perform this refactoring easily. It depends on the problem you are solving.

Coding style is subjective and will vary from developer to developer, patterns are constantly shifting and some drift in and out of fashion. It is always best to work with your team to decide what works best for you as a whole.

In general this is a way to get you thinking about how to make your code more readable to other people you work with and even your future self. Simpler, more readable code is worth 100x a comment explaining what your code is supposed to be doing.

Using extraction to separate code into individual methods can make it easier to test and find single points of failure. Though this would depend heavily on the testing methodology you are following.

## Sources

These links are valid as of June 2023

> * Why You Shouldn't Nest Your Code | <https://www.youtube.com/watch?v=CFRhGnuXG-4> - Contains a lot more in depth examples with much more complex code
>
> * Why You Shouldn't Nest Your Code | <https://medium.com/codex/why-you-shouldnt-nest-your-code-185cf2e2cde3>
>
> * Minimize Nesting | <https://en.wikibooks.org/wiki/Computer_Programming/Coding_Style/Minimize_nesting>
>
> * Return Early Pattern | <https://medium.com/swlh/return-early-pattern-3d18a41bba8> - Contains some downsides to the pattern which are very valid
