# Are Explicit Design Patterns Dead?

## Introduction

Consider the following quote for justification for explicit complex design patterns...

*"You can create a solution a million times over without ever doing it the same way twice"* - Design Patterns: Elements of Reusable Object-Oriented Software (1994) (Statement origin 1977 1 year after smalltalk 76)

This book was written at the infancy of a lot of modern languages that we use today such as Java which was released in 1995 and Visual Basic the predecessor to C#.

So does the beginning statement hold true anymore for modern C#? Or have we moved past the need for explicit usage of design patterns...

## Examples

Format a book signature that contains a title with an unknown amount of authors returning a string.

### Example Usage

```cs
Main() {
    // No Authors
    Console.WriteLine(Format("One Thousand and One Nights", Enumerable.Empty<string>()));

    // One Author
    Console.WriteLine(Format("Domain-Driven Design", new[] { "Eric Evans" }));

    // Multiple Authors
    Console.WriteLine(Format("Design Patterns", new[] { "Erich Gamma", "Richard Helm", "Ralph Johnson" }));
}
```

#### Output

> One Thousand and One Nights
>
> Domain-Driven Design by Eric Evans
>
> Design Patterns by Erich Gamma, Richard Helm and Ralph Johnson

### Explict Pattern Implementation Example

The code for explicit usage would look something like this:

```cs
string Format(string title, IEnumerable<string> authors)
{
    // Explict consumer of the interator
    IEnumerator<string> author = authors.GetEnumerator();

    // Has no authors
    if (!author.MoveNext()) return title;

    // Has one author "by Bob"
    StringBuilder builder = new StringBuilder(title).Append(" by ").Append(author.Current);
    if (!author.MoveNext()) return builder.ToString();

    // Has multiple authors "by Bob, Charlie and Sarah"
    string latest = author.Current;
    while (author.MoveNext())
    {
        builder.Append(", ").Append(latest);
        latest = author.Current;
    }

    return builder.Append(" and ").Append(latest).ToString();
}
```

This code is complex, likely difficult to extend or change and has three paths to test.

## Implicit Pattern Implementation Example

Luckily there is an alternative... List Patterns:

```cs
record Book(string Title, string[] Authors)

string Format(Book book) => book.Authors switch
{
    // Has no authors
    [] => book.Title,

    // Has one author "by Bob"
    [string single] => $"{book.Title} by {single}",
    
    // Has multiple authors "by Bob, Charlie and Sarah"
    [.. string[] fore, string last] => $"{book.Title} by {string.Join(", ", fore)} and {last}"
};
```

The simple list pattern simply won't compile for a missing case and is also simpler in not consuming the iterator explicitly whilst providing the same output for a complex case that was using a state machine and "move next".

## Extending The Examples

What if the requirements change so that book titles need to be sorted acsending and authors decending.

### Extended Implicit Pattern Implementation Example

```cs
Main()
{
    var sortedBooks = books.Concat(new Book[]
    {
        new ("Domain-Driven Design", new[] {"Vaughn Vernson"})
        // Collision of title with different authors
        new ("Domain-Driven Design", new[] {"Eric Evans", "Nobody Else"})
    })
    .OrderBy(book => book.Title) // Chain of responsiblity (inside LINQ)
    .ThenByDescending(book => book.Authors, // Inverting Decorator for the comparer
    Sorter.LexicographicalComparer<string>()) // Adapter

    foreach(var book in sortedBooks) Console.WriteLine(Format(book)); // Iterator
}

class Sorter
{
    // Generic comparer class has a method called "create" that wraps a comparison delegate into an IComparable<T> object
    // This is the adapter design pattern
    IComparer<T[]> LexicographicalComparer<T>(this T[] a, T[] b) where T : IComparable<T>.Create(a, b) => a.CompareLexicographically(b);

    // Dictionary order A, B, C etc
    IComparer<T[]> CompareLexicographically<T>(this T[] a, T[] b) where T : IComparable<T>
    {
        int length = Math.Min(a.Length, b.Length);
        for(int i = 0; i < length; i++) 
        {
            int comparison = a[i].CompareTo(b[i]);
            if (comparison != 0)
            {
                return comparison;
            }
        }

        return a.Length.CompareTo(b.Length);
    }
}
```

The books are sorted by title (ascending) and the authors list (decending) in this extended implict example.

The 4 patterns used however have all been implemented implicitly.

> Chain of responsibility - LINQ
>
> Decorator - LINQ
>
> Adapter - Base Class Library Framework
>
> Iterator - C# Syntax

## Summary

*"You can create a solution a million times over without ever doing it the same way twice"* - Design Patterns: Elements of Reusable Object-Oriented Software (1994) (Statement origin 1977 1 year after smalltalk 76)

Returning to this statement. With modern C# I am calling 🐂$&!" on this one. You can create a solution a million times the same way twice if you use implict patterns that already exist within C#. This is to say that whilst explict patterns are dead implicit ones within the language framework, libraries and syntax live on.

That said some explicit creational patterns live on such as factory methods and builder patterns and we can still benefit from learning how to implement these patterns ourselves for our own education as developers.

## Takeaways

- Less lines of code whilst still being readable make for fewer mistakes.
- Reduced complexity in the code we write for the same output leads to faster development (KISS principle).
- We should take advantage of the cakeism  offered by patterns inbuilt into languages like C#. Which is to say have your cake and eat it (simpler, readable/ understandable, fewer lines, consistently written, same output).

## Source

<https://www.youtube.com/watch?v=4rQ0jFmKSgk>
