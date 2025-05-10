# PDF-Text-Extractor

A Python library for extracting text from PDF documents with flexible configuration options and robust error handling.

![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)
![Python versions](https://img.shields.io/badge/python-3.7%20%7C%203.8%20%7C%203.9%20%7C%203.10%20%7C%203.11-blue)

## Table of Contents
- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Usage Examples](#usage-examples)
  - [Basic Text Extraction](#basic-text-extraction)
  - [Extracting Specific Pages](#extracting-specific-pages)
  - [Extracting Text with Layout Preservation](#extracting-text-with-layout-preservation)
  - [Extracting Tables](#extracting-tables)
  - [Password Protected PDFs](#password-protected-pdfs)
- [API Reference](#api-reference)
  - [PDFExtractor Class](#pdfextractor-class)
  - [Configuration Options](#configuration-options)
- [Output Formats](#output-formats)
- [Performance Optimization](#performance-optimization)
- [Error Handling](#error-handling)
- [Command Line Interface](#command-line-interface)
- [Contributing](#contributing)
- [Troubleshooting](#troubleshooting)
- [License](#license)

## Features

- **Simple API**: Extract text from PDFs with just a few lines of code
- **Page Selection**: Process specific pages or page ranges
- **Layout Preservation**: Maintain original document formatting
- **Table Extraction**: Identify and extract tabular data
- **Multiple Output Formats**: Export as plain text, JSON, CSV, or XML
- **Password Support**: Process password-protected PDF files
- **Error Handling**: Robust handling of corrupt or malformed PDFs
- **Performance**: Optimized for speed and memory efficiency
- **Command Line Interface**: Use as a CLI tool
- **Multiple PDF Libraries**: Support for PyPDF2, pdfplumber, and pdfminer.six backends

## Installation

You can install PDF-Text-Extractor using pip:

```bash
pip install pdf-text-extractor
```

### Dependencies

The package has the following dependencies:
- PyPDF2>=3.0.0
- pdfplumber>=0.9.0
- pdfminer.six>=20221105
- numpy>=1.20.0
- pandas>=1.3.0 (for table output)

For development:
```bash
pip install pdf-text-extractor[dev]
```

## Quick Start

Extract all text from a PDF:

```python
from pdf_extractor import PDFExtractor

# Initialize the extractor
extractor = PDFExtractor("document.pdf")

# Extract all text
text = extractor.extract_text()
print(text)

# Save to a file
extractor.save_text("output.txt")
```

## Usage Examples

### Basic Text Extraction

```python
from pdf_extractor import PDFExtractor

# Initialize with default settings
extractor = PDFExtractor("document.pdf")

# Extract all text from the PDF
text = extractor.extract_text()

# Print the extracted text
print(text)
```

### Extracting Specific Pages

```python
from pdf_extractor import PDFExtractor

extractor = PDFExtractor("document.pdf")

# Extract text from pages 1, 3, and 5 (0-indexed)
text_selected = extractor.extract_text(pages=[0, 2, 4])

# Extract a range of pages (pages 10-15)
text_range = extractor.extract_text(pages=range(9, 15))

# Extract first 5 pages 
text_first_pages = extractor.extract_text(pages=slice(0, 5))
```

### Extracting Text with Layout Preservation

```python
from pdf_extractor import PDFExtractor

# Initialize with layout preservation
extractor = PDFExtractor(
    "document.pdf",
    preserve_layout=True,
    line_margin=0.3,  # Space between lines to be considered the same paragraph
    char_margin=1.0   # Space between characters to be considered the same line
)

# Extract text while preserving the original layout
text = extractor.extract_text()
```

### Extracting Tables

```python
from pdf_extractor import PDFExtractor

# Initialize the extractor
extractor = PDFExtractor("document_with_tables.pdf")

# Extract tables from page 5
tables = extractor.extract_tables(pages=[4])

# Convert the first table to a pandas DataFrame
import pandas as pd
df = pd.DataFrame(tables[0])

# Save all tables to CSV files
for i, table in enumerate(tables):
    df = pd.DataFrame(table)
    df.to_csv(f"table_{i}.csv", index=False)
```

### Password Protected PDFs

```python
from pdf_extractor import PDFExtractor

# Initialize with password
extractor = PDFExtractor(
    "encrypted_document.pdf",
    password="your_password"
)

# Extract text
text = extractor.extract_text()
```

## API Reference

### PDFExtractor Class

```python
PDFExtractor(
    pdf_path,
    backend="pypdf",
    preserve_layout=False,
    detect_tables=False,
    line_margin=0.5,
    char_margin=2.0,
    password=None,
    encoding="utf-8",
    cleanup=True
)
```

#### Parameters:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `pdf_path` | str | - | Path to the PDF file |
| `backend` | str | "pypdf" | PDF processing backend ("pypdf", "pdfplumber", or "pdfminer") |
| `preserve_layout` | bool | False | Whether to preserve the original layout |
| `detect_tables` | bool | False | Whether to automatically detect and process tables |
| `line_margin` | float | 0.5 | Maximum vertical distance between lines in the same paragraph |
| `char_margin` | float | 2.0 | Maximum horizontal distance between characters in the same line |
| `password` | str | None | Password for encrypted PDFs |
| `encoding` | str | "utf-8" | Text encoding |
| `cleanup` | bool | True | Whether to clean up temporary files after processing |

#### Methods:

| Method | Returns | Description |
|--------|---------|-------------|
| `extract_text(pages=None)` | str | Extract text from specified pages (None for all pages) |
| `extract_tables(pages=None)` | list | Extract tables from specified pages (None for all pages) |
| `get_page_count()` | int | Returns the total number of pages |
| `save_text(output_path, pages=None)` | None | Save extracted text to a file |
| `save_tables(output_dir, format="csv", pages=None)` | None | Save extracted tables to files |
| `extract_images(output_dir, pages=None)` | list | Extract and save images from the PDF |
| `to_json(output_path=None)` | dict or None | Convert extracted content to JSON |
| `to_xml(output_path=None)` | str or None | Convert extracted content to XML |

### Configuration Options

#### Backend Selection

The library supports multiple PDF processing backends:

- **PyPDF** (Default): Fast and memory-efficient, best for simple text extraction
- **PDFPlumber**: Better layout preservation and table detection
- **PDFMiner**: Most accurate text positioning and encoding support

```python
# Using PDFPlumber backend
extractor = PDFExtractor("document.pdf", backend="pdfplumber")

# Using PDFMiner backend
extractor = PDFExtractor("document.pdf", backend="pdfminer")
```

#### Layout Preservation Options

When `preserve_layout=True`, you can fine-tune how the layout is preserved:

```python
extractor = PDFExtractor(
    "document.pdf",
    preserve_layout=True,
    line_margin=0.3,      # Smaller values make new paragraphs more aggressively
    char_margin=1.0,      # Smaller values join characters into words more aggressively
    word_margin=0.1       # Space between words
)
```

## Output Formats

### Plain Text

```python
# Extract and save as plain text
extractor.save_text("output.txt")
```

### JSON

```python
# Get document content as JSON
json_data = extractor.to_json()

# Save JSON to file
extractor.to_json("output.json")
```

JSON output structure:
```json
{
  "metadata": {
    "title": "Document Title",
    "author": "Author Name",
    "creation_date": "2023-05-10",
    "page_count": 5
  },
  "pages": [
    {
      "page_number": 1,
      "text": "Page 1 content...",
      "tables": [...],
      "images": [...]
    },
    ...
  ]
}
```

### XML

```python
# Get document content as XML
xml_data = extractor.to_xml()

# Save XML to file
extractor.to_xml("output.xml")
```

### CSV (for tables)

```python
# Save all detected tables to CSV files
extractor.save_tables("output_directory", format="csv")
```

## Performance Optimization

For large PDFs, you can optimize performance:

```python
from pdf_extractor import PDFExtractor

# Use PyPDF backend for best performance
extractor = PDFExtractor(
    "large_document.pdf",
    backend="pypdf",
    preserve_layout=False,  # Disable layout preservation for speed
    cleanup=True,           # Clean temporary files
    caching=True            # Enable caching
)

# Process in batches
batch_size = 10
total_pages = extractor.get_page_count()

for start_page in range(0, total_pages, batch_size):
    end_page = min(start_page + batch_size, total_pages)
    text = extractor.extract_text(pages=range(start_page, end_page))
    
    # Process text batch
    with open(f"output_{start_page}-{end_page}.txt", "w") as f:
        f.write(text)
```

## Error Handling

The library includes robust error handling:

```python
from pdf_extractor import PDFExtractor, PDFExtractionError

try:
    extractor = PDFExtractor("possibly_corrupt.pdf")
    text = extractor.extract_text()
except PDFExtractionError as e:
    print(f"Error extracting text: {e}")
    # Try alternative approach
    try:
        extractor = PDFExtractor("possibly_corrupt.pdf", backend="pdfminer", recovery_mode=True)
        text = extractor.extract_text()
        print("Extraction succeeded with recovery mode")
    except PDFExtractionError:
        print("Could not extract text even with recovery mode")
```

Common exceptions:

- `PDFExtractionError`: Base exception for all extraction errors
- `PDFPasswordError`: Incorrect or missing password
- `PDFCorruptedError`: Corrupted PDF file
- `PDFUnsupportedError`: Unsupported PDF feature

## Command Line Interface

The package includes a command-line interface:

```bash
# Basic usage
pdf-extract document.pdf -o output.txt

# Extract specific pages
pdf-extract document.pdf -p 1-5,8,11-13 -o output.txt

# Extract with layout preservation
pdf-extract document.pdf --preserve-layout -o output.txt

# Extract tables
pdf-extract document.pdf --tables -f csv -o tables_output/

# Process password-protected PDF
pdf-extract encrypted.pdf --password mypassword -o output.txt

# Get JSON output
pdf-extract document.pdf --format json -o output.json

# Use specific backend
pdf-extract document.pdf --backend pdfplumber -o output.txt

# Get help
pdf-extract --help
```

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Commit your changes: `git commit -m 'Add feature'`
4. Push to the branch: `git push origin feature-name`
5. Submit a pull request

Please make sure to update tests as appropriate.

### Development Setup

```bash
# Clone the repository
git clone https://github.com/username/pdf-text-extractor.git
cd pdf-text-extractor

# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install development dependencies
pip install -e ".[dev]"

# Run tests
pytest
```

## Troubleshooting

### Common Issues

#### 1. No Text Extracted

If no text is extracted, the PDF might be:
- Scanned images without OCR
- Text stored as curves/paths
- Using custom or embedded fonts

Solution: Try using `backend="pdfminer"` which has better support for different PDF structures:

```python
extractor = PDFExtractor("problematic.pdf", backend="pdfminer")
```

#### 2. Incorrect Character Encoding

If you see garbled text or strange characters:

```python
extractor = PDFExtractor(
    "document.pdf",
    encoding="latin-1"  # Try different encodings
)
```

Common encodings to try: "utf-8", "latin-1", "cp1252", "iso-8859-1"

#### 3. Memory Errors with Large PDFs

For very large PDFs:

```python
# Process page by page
extractor = PDFExtractor("huge_document.pdf")
total_pages = extractor.get_page_count()

with open("output.txt", "w") as outfile:
    for page_num in range(total_pages):
        text = extractor.extract_text(pages=[page_num])
        outfile.write(f"--- Page {page_num+1} ---\n{text}\n\n")
        # Clear memory
        import gc
        gc.collect()
```

#### 4. Table Detection Issues

If tables aren't being detected correctly:

```python
extractor = PDFExtractor(
    "document_with_tables.pdf",
    backend="pdfplumber",  # Better table detection
    detect_tables=True,
    table_settings={
        "vertical_strategy": "text",
        "horizontal_strategy": "text",
        "intersection_tolerance": 5
    }
)
```

## License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## Example Implementation

Below is a simplified implementation of the core functionality to help you understand how it works:

```python
import PyPDF2
import pdfplumber
from pdfminer.high_level import extract_text as pdfminer_extract_text

class PDFExtractor:
    def __init__(self, pdf_path, backend="pypdf", **kwargs):
        self.pdf_path = pdf_path
        self.backend = backend
        self.options = kwargs
        
        # Validate the PDF file
        self._validate_pdf()
        
    def _validate_pdf(self):
        try:
            if self.backend == "pypdf":
                with open(self.pdf_path, "rb") as f:
                    PyPDF2.PdfReader(f)
            elif self.backend == "pdfplumber":
                pdfplumber.open(self.pdf_path)
            elif self.backend == "pdfminer":
                pdfminer_extract_text(self.pdf_path)
            else:
                raise ValueError(f"Unsupported backend: {self.backend}")
        except Exception as e:
            raise PDFExtractionError(f"Invalid or corrupted PDF: {str(e)}")
    
    def extract_text(self, pages=None):
        """Extract text from the PDF document."""
        if self.backend == "pypdf":
            return self._extract_with_pypdf(pages)
        elif self.backend == "pdfplumber":
            return self._extract_with_pdfplumber(pages)
        elif self.backend == "pdfminer":
            return self._extract_with_pdfminer(pages)
    
    def _extract_with_pypdf(self, pages=None):
        """Extract text using PyPDF2."""
        with open(self.pdf_path, "rb") as f:
            reader = PyPDF2.PdfReader(f)
            
            if self.options.get("password"):
                reader.decrypt(self.options["password"])
            
            if pages is None:
                pages = range(len(reader.pages))
            elif isinstance(pages, int):
                pages = [pages]
            
            text = ""
            for page_num in pages:
                if 0 <= page_num < len(reader.pages):
                    page = reader.pages[page_num]
                    text += page.extract_text() + "\n\n"
            
            return text
    
    def _extract_with_pdfplumber(self, pages=None):
        """Extract text using pdfplumber."""
        with pdfplumber.open(self.pdf_path) as pdf:
            if pages is None:
                pages = range(len(pdf.pages))
            elif isinstance(pages, int):
                pages = [pages]
            
            text = ""
            for page_num in pages:
                if 0 <= page_num < len(pdf.pages):
                    page = pdf.pages[page_num]
                    text += page.extract_text(
                        x_tolerance=self.options.get("char_margin", 3),
                        y_tolerance=self.options.get("line_margin",.5)
                    ) or ""
                    text += "\n\n"
            
            return text
    
    def _extract_with_pdfminer(self, pages=None):
        """Extract text using pdfminer.six."""
        from pdfminer.high_level import extract_text
        from pdfminer.layout import LAParams
        
        laparams = LAParams(
            line_margin=self.options.get("line_margin", 0.5),
            char_margin=self.options.get("char_margin", 2.0),
            word_margin=self.options.get("word_margin", 0.1),
            detect_vertical=self.options.get("detect_vertical", False)
        )
        
        if pages is not None:
            if isinstance(pages, int):
                page_numbers = [pages + 1]  # pdfminer uses 1-indexed pages
            else:
                page_numbers = [p + 1 for p in pages]  # Convert to 1-indexed
            page_range = ','.join(str(p) for p in page_numbers)
        else:
            page_range = None
            
        return extract_text(
            self.pdf_path,
            password=self.options.get("password"),
            page_numbers=page_range,
            laparams=laparams
        )
    
    def extract_tables(self, pages=None):
        """Extract tables from the PDF document."""
        if self.backend != "pdfplumber":
            raise NotImplementedError(
                "Table extraction is only supported with the 'pdfplumber' backend"
            )
        
        with pdfplumber.open(self.pdf_path) as pdf:
            if pages is None:
                pages = range(len(pdf.pages))
            elif isinstance(pages, int):
                pages = [pages]
            
            all_tables = []
            for page_num in pages:
                if 0 <= page_num < len(pdf.pages):
                    page = pdf.pages[page_num]
                    tables = page.extract_tables()
                    all_tables.extend(tables)
            
            return all_tables
    
    def get_page_count(self):
        """Get the total number of pages in the PDF."""
        if self.backend == "pypdf":
            with open(self.pdf_path, "rb") as f:
                reader = PyPDF2.PdfReader(f)
                return len(reader.pages)
        elif self.backend == "pdfplumber":
            with pdfplumber.open(self.pdf_path) as pdf:
                return len(pdf.pages)
        elif self.backend == "pdfminer":
            from pdfminer.pdfparser import PDFParser
            from pdfminer.pdfdocument import PDFDocument
            
            with open(self.pdf_path, "rb") as f:
                parser = PDFParser(f)
                document = PDFDocument(parser)
                return len(document.catalog['Pages'].resolve()['Kids'])
    
    def save_text(self, output_path, pages=None):
        """Save extracted text to a file."""
        text = self.extract_text(pages)
        with open(output_path, "w", encoding=self.options.get("encoding", "utf-8")) as f:
            f.write(text)
    
    def save_tables(self, output_dir, format="csv", pages=None):
        """Save extracted tables to files."""
        import os
        import csv
        import json
        
        if not os.path.exists(output_dir):
            os.makedirs(output_dir)
        
        tables = self.extract_tables(pages)
        
        for i, table in enumerate(tables):
            if format.lower() == "csv":
                output_path = os.path.join(output_dir, f"table_{i+1}.csv")
                with open(output_path, "w", newline="", encoding=self.options.get("encoding", "utf-8")) as f:
                    writer = csv.writer(f)
                    writer.writerows(table)
            elif format.lower() == "json":
                output_path = os.path.join(output_dir, f"table_{i+1}.json")
                with open(output_path, "w", encoding=self.options.get("encoding", "utf-8")) as f:
                    # Convert table to list of dictionaries
                    headers = table[0]
                    rows = table[1:]
                    table_dicts = [dict(zip(headers, row)) for row in rows]
                    json.dump(table_dicts, f, indent=2)

class PDFExtractionError(Exception):
    """Base exception for PDF extraction errors."""
    pass

class PDFPasswordError(PDFExtractionError):
    """Exception raised when PDF password is incorrect or missing."""
    pass

class PDFCorruptedError(PDFExtractionError):
    """Exception raised when PDF file is corrupted."""
    pass

class PDFUnsupportedError(PDFExtractionError):
    """Exception raised when PDF contains unsupported features."""
    pass
```

---

Created with ❤️ by [Your GitHub Username]
