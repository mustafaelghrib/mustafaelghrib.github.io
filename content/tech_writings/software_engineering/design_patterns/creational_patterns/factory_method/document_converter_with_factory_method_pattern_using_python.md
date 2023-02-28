---
title: "Building a Document Converter with the Factory Method pattern using Python"
date: 2023-02-28T17:12:04+02:00
author: Mustafa Abdallah
description:
---

### Introduction
I am implementing real-world projects using the design patterns to see how we could benefit from them when building our own software, in this article I am building a Document Converter using the Factory Method design pattern, so let's get started!

### What is the Factory Method pattern?
Explaining the pattern is out of scope of this article, and you could take a look at the [references]({{< ref "document_converter_with_factory_method_pattern_using_python.md#references" >}}) to know more about this pattern, how we use it and the UML diagram for it. Please make sure you understand the pattern before moving on.

### What is the Document Converter?
A Document Converter is a software tool that allows converting documents from one format to another. in this we will build a Document Converter that can convert between various document formats (e.g. PDF, DOCX, HTML) using the Factory Method pattern. Each converter (e.g. PdfConverter, DocxConverter, HtmlConverter) could be implemented as a separate factory that takes in the input document and returns the corresponding output format.

### UML diagram for the Document Converter
According to the Factory Method UML diagram, the **Creator** will be the **Converter** and the **Concrete Creators** will be (**PDFConverter**, **DocxConverter**, **HTMLConverter**) and the **Product** will be the **Document** and the **Concrete Products** will be (**PDFDocument**, **DocxDocument**, **HTMLDocument**), so let's see how we implement that in code.

### Code implementation

#### - Step 1: Create the Document class
```python
# document.py

class Document:

    def __init__(self):
        self._file = ""
        self._name = ""

    def read_file(self):
        print(f"Reading {self._file}")

    def process_file(self):
        print(f"Processing {self._file}")

    def convert_file(self):
        print(f"Converting {self._file} to {self._name}")
```
#### - Step 2: Create the PDFDocument, DocxDocument and HTMLDocument
```python
# document.py

class PDFDocument(Document):

    def __init__(self, file):
        super().__init__()
        self._file = file
        self._name = "pdf document"
        
class DocxDocument(Document):

    def __init__(self, file):
        super().__init__()
        self._file = file
        self._name = "docx document"
        
class HTMLDocument(Document):

    def __init__(self, file):
        super().__init__()
        self._file = file
        self._name = "html document"
```
#### - Step 3: Create the Converter class
```python
# converter.py

from document import Document
from abc import ABC, abstractmethod

class Converter(ABC):

    def convert(self, file, to):
        document: Document = self._create_document(file, to)
        document.read_file()
        document.process_file()
        document.convert_file()
        return document

    @abstractmethod
    def _create_document(self, file, to):
        pass
```
#### - Step 4: Create the PDFConverter
```python
# converter.py

from document import PDFDocument, HTMLDocument, DocxDocument

class PDFConverter(Converter):

    def _create_document(self, file, to):
        if to == "pdf":
            return PDFDocument(file)
        elif to == "html":
            return HTMLDocument(file)
        elif to == "docx":
            return DocxDocument(file)
        return None
        
# The DocxConverter and HTMLConverter will be the same
```

### Testing the Document Converter
We could test the PDF Converter as follows:
```python
pdf_converter = PDFConverter()
document = pdf_converter.convert(file="file.pdf", to="docx")
```
And here is the result:
```text
Reading file.pdf
Processing file.pdf
Converting file.pdf to docx document
```

### Conclusion
So this is just a simple Document Converter that show us how to use the Factory Method design pattern in a real-world application, I hope this article was helpful for you and [follow me](https://linkedin.com/in/mustafaabdulluh) for more articles like this.

### References
- [Factory Method Pattern – Design Patterns (ep 4) by Christopher Okhravi](https://www.youtube.com/watch?v=EcFVTgRHJLM)
- [Factory Method by RefactoringGuru](https://refactoring.guru/design-patterns/factory-method)
- [Head First Design Patterns: A Brain-Friendly Guide](https://www.amazon.com/Head-First-Design-Patterns-Brain-Friendly/dp/0596007124)
