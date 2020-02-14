LargeXlsx - Minimalistic .net library to write large XLSX files
===============================================================

This is a minimalistic library, written in C# targeting .net standard 2.0, providing a tiny layer above Microsoft's [Office Open XML library](https://github.com/OfficeDev/Open-XML-SDK) to facilitate creation of very large Excel files in XLSX format.

This library provides simple primitives to write data in a streamed manner, so that potentially huge files can be created while consuming a low, constant amount of memory.


Supported features
------------------

Currently the library supports:

* cells containing inline strings
* cells containing numeric (double) values
* multiple sheets
* merged cells (_warning:_ their reference is kept in memory until a sheet is complete)
* split panes, a.k.a. frozen rows and columns
* basic styling: font face, size, and color; solid background color; top-right-bottom-left cell borders; numeric formatting


Example
-------

To create a simple single-sheet Excel document:

```csharp
using (var stream = new FileStream("Simple.xlsx", FileMode.Create))
using (var largeXlsxWriter = new LargeXlsxWriter(stream))
{
    var whiteFont = largeXlsxWriter.Stylesheet.CreateFont("Calibri", 11, "ffffff", bold: true);
    var blueFill = largeXlsxWriter.Stylesheet.CreateSolidFill("004586");
    var headerStyle = largeXlsxWriter.Stylesheet.CreateStyle(whiteFont, blueFill, LargeXlsxStylesheet.GeneralNumberFormat, LargeXlsxStylesheet.NoBorder);

    largeXlsxWriter.BeginSheet("Sheet1")
        .BeginRow().Write("Col1", headerStyle).Write("Col2", headerStyle).Write("Col3", headerStyle)
        .BeginRow().Write("Row2").Write(42).Write(-1)
        .BeginRow().Write("Row3").SkipColumns(1).Write(1234)
        .SkipRows(2)
        .BeginRow().Write("Row6").AddMergedCell(1, 2).SkipColumns(1).Write(3.14159265359);
}
```

The output is like:

![Single sheet Excel document with 6 rows and 3 columns](https://github.com/salvois/LargeXlsx/raw/master/example.png "Single sheet Excel document with 6 rows and 3 columns")

Known issues
------------

On .net core there is an [issue on System.IO.Packaging](https://github.com/dotnet/corefx/issues/24457) (used by the Open XML SDK to write XLSX's zip packages) that causes memory consumption to be proportional to the amount of data written, instead of being low and constant. Unfortunately, this kind of defeats the purpose of this library when targeting .net core. The issue is not present on .net framework.


License
-------

Permissive, [2-clause BSD style](https://opensource.org/licenses/BSD-2-Clause)

LargeXlsx - Minimalistic .net library to write large XLSX files

Copyright 2020  Salvatore ISAJA

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
