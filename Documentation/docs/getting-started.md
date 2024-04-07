# Getting Started

## AnsiStreamParser

[`AnsiStreamParser`](../api/Microlithic.Text.Ansi.AnsiStreamParser.yml) parses one character at a time from a stream of character data. It expects the characters to be encoded in UTF-16 format, which is the standard string encoding format for the .NET [`String`](xref:System.String) type. It should generally also work with 7 and 8-bit codes that conform to ECMA-35.

Here is a simple example of its usage:

```csharp
AnsiStreamParser parser = new(ParsedElementCallback);

foreach (ch in $"\x1b[34mHello\x1b[0m") {
    parser.Parse(ch);
}

void ParsedElementCallback(IAnsiElement element) {
    // Handle the parsed element.
    Debug.Print(element);
}
```

First, we create a new `AnsiStreamParser` instance and provide a callback function that will be executed once for every parsed element in the input stream. Then we simply call the parser's `Parse()` method for each character in the input stream. The example above will produce the following debug output:

```text
AnsiCsiElement { Parameters = [ 34 ], ControlFunction = m }
AnsiPrintElement { Text = H }
AnsiPrintElement { Text = e }
AnsiPrintElement { Text = l }
AnsiPrintElement { Text = l }
AnsiPrintElement { Text = o }
AnsiCsiElement { Parameters = [ 0 ], ControlFunction = m }
```

## AnsiStringParser

`AnsiStringParser` uses an internal instance of `AnsiStreamParser` in order to parse an entire string on each invocation of its `Parse()` method. Unlike `AnsiStreamParser`, `AnsiStringParser` returns a list of `IAnsiElement` instances rather than invoking a callback function for each instance. Furthermore, the printable characters and the characters in control strings are consolidated into strings of characters returned in single `IAnsiElement` instances, rather than being returned one character at a time.

Here is a simple example showing how `AnsiStringParser` can be used:

```csharp
AnsiStringParser parser = new();

List<IAnsiElement> elements = parser.Parse($"\x1b[34mHello\x1b[0m"));

foreach (element in elements) {
    Debug.Print(element);
}
```

First, we create a new AnsiStringParser instance and then we simply call the parser's Parse() method for each string that we wish to parse. The example above will produce the following debug output:

```text
AnsiCsiElement { Parameters = [ 34 ], ControlFunction = m }
AnsiPrintElement { Text = Hello }
AnsiCsiElement { Parameters = [ 0 ], ControlFunction = m }
```
