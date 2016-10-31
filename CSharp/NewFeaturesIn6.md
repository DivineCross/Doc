# New Features in C#6
[Reference](https://blogs.msdn.microsoft.com/csharpfaq/2014/11/20/new-features-in-c-6/)

## `Using Static`
```csharp
using static System.Console;

    // in your code...
    Console.WriteLine("Hello World!");  // without 'using static'
    WriteLine("Hello World!");  // with 'using static'
```

## String Interpolation
A new way to format your string. The problem of `string.Format` is the use of numbered placeholders, which is not so readable and the parameters must be arranged with that order. The syntax of **interpolated string** can do the similar work with a more readable and often shroter code.

```csharp
public class Dog
{
    public string Name { get; set; }
    public int Age { get; set; }

    public override string ToString() => $"Name: {Name}, Age: {Age}";
}

public static void Main(string[] args)
{
    double x = 3.1415926;
    int y = 6;
    Dog myDog = new Dog { Name = "White", Age = 3 };

    // Use string.Format
    WriteLine(string.Format(@"
            x     = {0, -10:F10}
            y     = {1, -10:D}
            myDog = {2}
        ",
        x, y, myDog
    ));

    // Use interpolated string
    WriteLine($@"
            x     = {x, -10:F10}
            y     = {y, -10:D}
            myDog = {myDog}
    ");
}
```
```
// Output:

            x     = 3.1415926000
            y     = 6
            myDog = Name: White, Age: 3


            x     = 3.1415926000
            y     = 6
            myDog = Name: White, Age: 3

```

## Null-conditional Operator
```csharp
public static void Main(string[] args)
{
    string str = null;
    int? length1 = str != null ? str.Length : (int?)null;  // without '?.'
    int? length2 = str?.Length;  // with '?.'

    List<int> scores1 = null;
    List<int> scores2 = new List<int> { 3, 6, 9 };
    WriteLine($"{nameof(scores1)} count = {scores1?.Count ?? 0}");
    WriteLine($"{nameof(scores2)} count = {scores2?.Count ?? 0}");
}
```

## Index Initializers
```csharp
Dictionary<int, Dog> dict = new Dictionary<int, Dog>() {
    [5566] = new Dog { Name = "White", Age = 2 },
    [5487] = new Dog { Name = "Black", Age = 4 }
};
```

## `nameof` Operator
Convert **symbol** to **string** at **compile time**. This is useful when you want to show information of parameters in exceptions or events; in addition, this prevent developers from mistyping, and the refactoring of renaming can be done simply by tools in your IDE.

```csharp
public static void Main(string[] args)
{
    DemoNameof();
}

public class Dog
{
    public string Name { get; set; }
    public int Age { get; set; }

    public void Bark(int count)
    {
        if (count < 0)
            throw new ArgumentException($"'{nameof(count)}' can not be negative!");

        for (int i = 0; i < count; ++i)
            WriteLine("5566 never die!!!");
    }
}

public static void DemoNameof()
{
    Dog myDog = new Dog { Name = "White", Age = 3 };

    WriteLine(nameof(Dog));
    WriteLine(nameof(myDog));
    WriteLine(nameof(myDog.Name));

    try {
        myDog.Bark(-1);
    }
    catch (ArgumentException e) {
        WriteLine(e.Message);
    }
}
```
```
// Output
Dog
myDog
Name
'count' can not be negative!
```

## Expression Bodied Functions and Properties
Just like lambda!

```csharp
public class Dog
{
    public string Name { get; set; }
    public int Age { get; set; }
    public int DoubleAge => Age * 2;

    public override string ToString() => $"Name: {Name}, Age: {Age}";
}
```

