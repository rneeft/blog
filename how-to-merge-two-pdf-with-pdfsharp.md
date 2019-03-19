# How to merge two PDF file with using PDFSharp

Nuget: PDFSharp (by empira Software GmbH)

```csharp
using PdfSharp.Pdf;
using PdfSharp.Pdf.IO;

namespace ConsoleApp3
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var pdf1 = @"C:\temp\pdf1.pdf";
            var pdf2 = @"C:\temp\pdf2.pdf";

            using (var doc1 = PdfReader.Open(pdf1, PdfDocumentOpenMode.Import))
            using (var doc2 = PdfReader.Open(pdf2, PdfDocumentOpenMode.Import))
            using (var merged = new PdfDocument())
            {
                foreach (var page in doc1.Pages)
                {
                    merged.AddPage(page);
                }

                foreach (var page in doc2.Pages)
                {
                    merged.AddPage(page);
                }

                merged.Save(@"C:\temp\merge.pdf");
            }
        }
    }
}

```