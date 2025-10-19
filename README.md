# WpfElementRenderer

![NuGet version](https://www.nuget.org/packages/WpfElementRenderer/)

A simple .NET library for WPF that renders any `FrameworkElement` into a series of high-quality, paginated PNG images.

This library solves the common problem of exporting complex or dynamically sized WPF UI controls (like receipts, reports, or long forms) into a format suitable for printing or PDF generation. It automatically handles scaling to a standard page width (A4) and splitting the content across multiple pages, all while maintaining the correct aspect ratio.

## Features

*   **Render Any Element**: Convert any `FrameworkElement` (e.g., `Grid`, `StackPanel`, `UserControl`) into images.
*   **Automatic Pagination**: Automatically calculates the number of pages needed based on the element's total height.
*   **High-Quality Output**: Renders images at a configurable DPI (defaulting to 300 DPI for print quality).
*   **Aspect-Ratio Correct Scaling**: Uses a `Viewbox` to scale content uniformly to an A4 page width, preventing any stretching or distortion.
*   **Simple API**: A single method call is all you need to get started.
*   **Framework Independent**: The output is a `List<byte[]>`, which can be easily used with any PDF library (like QuestPDF, iTextSharp, PdfSharp) or saved directly to disk.

## Installation

The library is available on NuGet. You can install it using the .NET CLI or the NuGet Package Manager Console.

#### .NET CLI

```shell
dotnet add package WpfElementRenderer
```

#### Package Manager Console

```powershell
Install-Package WpfElementRenderer
```

## Quick Start

Using the library is straightforward. Instantiate the `ElementToImageConverter` and pass it the WPF element you want to render.

```csharp
using System.IO;
using System.Windows;
using WpfElementRenderer; // 1. Add the using directive

public partial class MyReportView : Window
{
    private void ExportButton_Click(object sender, RoutedEventArgs e)
    {
        // 2. Create an instance of the converter.
        var converter = new ElementToImageConverter();

        // 3. Call the method, passing the UI element you want to render.
        //    (e.g., a Grid named "PrintableContent" in your XAML).
        var imagePages = converter.GeneratePagedImages(this.PrintableContent);

        // 4. Do something with the results (e.g., save to disk).
        string desktopPath = Environment.GetFolderPath(Environment.SpecialFolder.Desktop);
        for (int i = 0; i < imagePages.Count; i++)
        {
            string filePath = Path.Combine(desktopPath, $"Report_Page_{i + 1}.png");
            File.WriteAllBytes(filePath, imagePages[i]);
        }

        MessageBox.Show($"{imagePages.Count} page(s) were generated and saved to your desktop.");
    }
}
```

## API Reference

### `ElementToImageConverter`

The main class in the library.

#### Methods

`public List<byte[]> GeneratePagedImages(FrameworkElement elementToRender, int dpi = 300)`

Converts a `FrameworkElement` into a list of PNG byte arrays.

*   **`elementToRender`**: The WPF `FrameworkElement` to be rendered. This can be any visual element from your application.
*   **`dpi` (optional)**: The resolution in dots-per-inch for the output images. Defaults to `300`.
*   **Returns**: A `List<byte[]>`, where each byte array contains the data for one PNG image page.

## How It Works

The library uses a multi-step process to achieve clean, paginated output:
1.  **Scaling**: The provided `FrameworkElement` is placed inside a `Viewbox`, which is then constrained to the width of an A4 page (in device-independent units). This scales the entire visual down uniformly.
2.  **Measuring**: The framework measures the total height of the scaled visual.
3.  **Slicing**: A `VisualBrush` is created from the scaled visual. This brush is then "painted" onto separate render targets, one for each page, using a calculated offset to create the "slices" for pagination.
4.  **Encoding**: Each rendered page is encoded as a PNG and returned as a byte array.

## Common Use Cases

*   **Generating Printable Reports**: Create multi-page PNGs of a report and print them.
*   **Export to PDF**: Generate the PNGs and then embed them into a PDF document using a library like QuestPDF.
*   **Creating Image Previews**: Quickly generate a visual preview of a complex UI control.

## Contributing

Contributions are welcome! If you find a bug or have a feature request, please open an issue. If you'd like to contribute code, please fork the repository and submit a pull request.

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.
