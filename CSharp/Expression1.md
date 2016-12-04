# Expression Practice 1

## Reference
[aspnet Mvc `ExpressionHelper`](https://github.com/aspnet/Mvc/blob/4cca6b09f0044660d64469e3278b584a17c356e9/src/Microsoft.AspNetCore.Mvc.ViewFeatures/Internal/ExpressionHelper.cs)  

## Mimic `NameFor` in `aspnet Mvc`

```csharp
using System;
using System.Linq.Expressions;
using System.Text;

namespace ConsoleApplication
{
    public static class NameExtensions
    {
        // This method only supports MemberAccess and ArrayIndex with constant integer index.
        public static string NameFor<TModel, TProperty>(
            this MyHelper<TModel> helper,
            Expression<Func<TModel, TProperty>> expression)
        {
            var builder = new StringBuilder();
            var part = expression.Body;
            while (part != null)
            {
                switch (part.NodeType)
                {
                    case ExpressionType.ArrayIndex:
                        // Supports only constant indice.
                        var binaryExpression = part as BinaryExpression;
                        var index = binaryExpression.Right as ConstantExpression;
                        builder.Insert(0, $"[{index}]");

                        Console.WriteLine($"BinaryExpression: {binaryExpression}");
                        Console.WriteLine($"    Right: {binaryExpression.Right}");
                        Console.WriteLine($"    Left: {binaryExpression.Left}");

                        part = binaryExpression.Left;
                        break;

                    case ExpressionType.MemberAccess:
                        var memberExpression = part as MemberExpression;
                        var name = memberExpression.Member.Name;
                        builder.Insert(0, $".{name}");

                        Console.WriteLine($"MemberExpression: {memberExpression}");
                        Console.WriteLine($"    Expression: {memberExpression.Expression}");

                        part = memberExpression.Expression;
                        break;

                    default:
                        // Unsupported.
                        Console.WriteLine($"Unsupported: {part}");
                        part = null;
                        break;
                }
                Console.WriteLine();
            }
            if (builder.Length > 0 && builder[0] == '.')
                builder.Remove(0, 1);

            return builder.ToString();
        }
    }

    public class MyHelper<TModel>
    {
    }

    public static class Program
    {
        public class Company
        {
            public Project Project { get; set; }
            public Project[] Projects { get; set; }
        }

        public class Project
        {
            public Programmer Programmer { get; set; }
            public Programmer[] Programmers { get; set; }
        }

        public class Programmer
        {
            public string Name { get; set; }
            public int Age { get; set; }
        }

        public static void Main(string[] args)
        {
            var my = new MyHelper<Company>();

            Console.WriteLine(my.NameFor(m => m.Project.Programmer.Name));
            Console.WriteLine("\n------------------------------------------------------------\n");
            Console.WriteLine(my.NameFor(m => m.Projects[467].Programmers[979].Name));
        }
    }
}
```
```
// Output:
MemberExpression: m.Project.Programmer.Name
    Expression: m.Project.Programmer

MemberExpression: m.Project.Programmer
    Expression: m.Project

MemberExpression: m.Project
    Expression: m

Unsupported: m

Project.Programmer.Name

------------------------------------------------------------

MemberExpression: m.Projects[467].Programmers[979].Name
    Expression: m.Projects[467].Programmers[979]

BinaryExpression: m.Projects[467].Programmers[979]
    Right: 979
    Left: m.Projects[467].Programmers

MemberExpression: m.Projects[467].Programmers
    Expression: m.Projects[467]

BinaryExpression: m.Projects[467]
    Right: 467
    Left: m.Projects

MemberExpression: m.Projects
    Expression: m

Unsupported: m

Projects[467].Programmers[979].Name
```
