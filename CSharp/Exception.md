# Exception

## Reference
[StackOverflow](http://stackoverflow.com/questions/730250/is-there-a-difference-between-throw-and-throw-ex)

## Comparison for `throw` and `throw ex` and `throw new Exception()`
```csharp
using System;

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                ThrowTest();
            }
            catch (Exception ex)
            {
                Console.WriteLine("-- Outer Catch --");
                Console.WriteLine("Stack trace:");
                Console.WriteLine(ex.StackTrace);
                Console.WriteLine();
                Console.WriteLine("Message:");
                Console.WriteLine(ex.Message);
                Console.WriteLine();

                if (ex.InnerException == null)
                {
                    Console.WriteLine("No Inner exception.");
                }
                else
                {
                    var innerEx = ex.InnerException;
                    Console.WriteLine("Inner Stack trace:");
                    Console.WriteLine(innerEx.StackTrace);
                    Console.WriteLine();
                    Console.WriteLine("Inner Message:");
                    Console.WriteLine(innerEx.Message);
                    Console.WriteLine();
                }
            }
        }

        static void ThrowTest()
        {
            decimal a = 1m;
            decimal b = 0m;
            Div(a, b);

            try
            {
                Div(a, b);
            }
            catch (ArithmeticException ex)
            {
                Console.WriteLine("-- Inner Catch --");
                Console.WriteLine($"Handling a {ex.GetType().Name}.");
                Console.WriteLine();

                throw;
                throw ex;
                throw new Exception();
                throw new Exception("Throw new exception with message");
                throw new Exception("Throw new exception with message and inner exception", ex);
            }
        }

        static void Div(decimal x, decimal y)
        {
            decimal.Divide(x, y);
        }
    }
}
```

### no inner handling
```csharp
static void ThrowTest()
{
    Div(1m, 0m);  // Line 44
}
```
```
// Output:
-- Outer Catch --
Stack trace:
   at System.Decimal.FCallDivide(Decimal& d1, Decimal& d2)
   at System.Decimal.Divide(Decimal d1, Decimal d2)
   at ConsoleApplication.Program.Div(Decimal x, Decimal y) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 54
   at ConsoleApplication.Program.ThrowTest() in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 44
   at ConsoleApplication.Program.Main(String[] args) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 11

Message:
Attempted to divide by zero.

No Inner exception.
```

### catch and `throw`
```csharp
try
{
    Div(1m, 0m);  // Line 44
}
catch (ArithmeticException)
{
    throw;  // Line 48
}
```
```
// Output:
-- Outer Catch --
Stack trace:
   at System.Decimal.FCallDivide(Decimal& d1, Decimal& d2)
   at System.Decimal.Divide(Decimal d1, Decimal d2)
   at ConsoleApplication.Program.Div(Decimal x, Decimal y) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 54
   at ConsoleApplication.Program.ThrowTest() in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 48
   at ConsoleApplication.Program.Main(String[] args) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 11

Message:
Attempted to divide by zero.

No Inner exception.
```

### catch and `throw ex`
```csharp
try
{
    Div(1m, 0m);  // Line 44
}
catch (ArithmeticException ex)
{
    throw ex;  // Line 48
}
```
```
// Output:
-- Outer Catch --
Stack trace:
   at ConsoleApplication.Program.ThrowTest() in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 48
   at ConsoleApplication.Program.Main(String[] args) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 11

Message:
Attempted to divide by zero.

No Inner exception.
```

### catch and `throw new Exception()`
```csharp
try
{
    Div(1m, 0m);  // Line 44
}
catch (ArithmeticException)
{
    throw new Exception();  // Line 48
}
```
```
// Output:
-- Outer Catch --
Stack trace:
   at ConsoleApplication.Program.ThrowTest() in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 48
   at ConsoleApplication.Program.Main(String[] args) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 11

Message:
Exception of type 'System.Exception' was thrown.

No Inner exception.
```

### catch and `throw new Exception()` with `Message` and `InnerException`
```csharp
try
{
    Div(1m, 0m);  // Line 44
}
catch (ArithmeticException ex)
{
    throw new Exception("Throw new exception with message and inner exception", ex);  // Line 48
}
```
```
// Output:
-- Outer Catch --
Stack trace:
   at ConsoleApplication.Program.ThrowTest() in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 48
   at ConsoleApplication.Program.Main(String[] args) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 11

Message:
Throw new exception with message and inner exception

Inner Stack trace:
   at System.Decimal.FCallDivide(Decimal& d1, Decimal& d2)
   at System.Decimal.Divide(Decimal d1, Decimal d2)
   at ConsoleApplication.Program.Div(Decimal x, Decimal y) in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 54
   at ConsoleApplication.Program.ThrowTest() in C:\Workspace\VSCode\Consoles\Prac\Program.cs:line 44

Inner Message:
Attempted to divide by zero.
```

## Unused Exception Variable
```csharp
catch (ArithmeticException ex)
{
    Console.WriteLine("I have an ArithmeticException");
}
```
**The variable `ex` is declared but never used**
If you do not use the exception variable, just remove it as the following:
```csharp
catch (ArithmeticException)  // do not use the exception variable
{
    Console.WriteLine("I have an ArithmeticException");
}
```

## Conclusion
- Make sure what you are doing when dealing with exceptions and throwing exceptions
- Consider to throw new exceptions with `InnerException` to preserve information
- Do not catch exceptions that you can not handle
- Do not leave unused exception variables
