### First part
```csharp
var input = await File.ReadAllTextAsync("Advent of Code/2015/Day 1/input.txt");

int count = 0;
foreach (var element in input)
{
    if (element == '(')
    {
        count++;
    }
    else
    {
        count--;
    }
}

Console.WriteLine(count);
```