# PolyglotMarkdown

A versatile Markdown extension designed to manage and display multilingual content within a single document, offering powerful features like language-specific content inclusion, multi-language metadata support, and version control for a global audience.

This project is a *Work in Progress*

## Table of contents

- [PolyglotMarkdown](#polyglotmarkdown)
  - [Table of contents](#table-of-contents)
  - [Overview](#overview)
  - [Why Would I Use This Instead of Separate Files for Each Language?](#why-would-i-use-this-instead-of-separate-files-for-each-language)
  - [Key Features](#key-features)
    - [1. Metadata Section](#1-metadata-section)
      - [Fallback Language Support](#fallback-language-support)
    - [2. Portion Marking](#2-portion-marking)
    - [3. Comments and Annotations](#3-comments-and-annotations)
    - [4. Content Versioning](#4-content-versioning)
    - [5. Transclusion (Including External Content)](#5-transclusion-including-external-content)
    - [6. Inter-document Links with Language Awareness](#6-inter-document-links-with-language-awareness)
    - [7. Multi-language Indexing and Search](#7-multi-language-indexing-and-search)
  - [Using the PolyglotParser Python Class](#using-the-polyglotparser-python-class)
    - [Initialization](#initialization)
    - [Parsing Metadata](#parsing-metadata)
    - [Parsing Metadata by Language](#parsing-metadata-by-language)
    - [Parsing Content](#parsing-content)
    - [Handling Fallback Language](#handling-fallback-language)
    - [Handling Versioning](#handling-versioning)
    - [Handling Transclusion](#handling-transclusion)
    - [Example Usage](#example-usage)
      - [Example Document Content](#example-document-content)
      - [External Files](#external-files)
      - [Parsing and Handling the Document](#parsing-and-handling-the-document)
      - [Expected Output](#expected-output)
    - [Using the PolyglotParser Methods](#using-the-polyglotparser-methods)
      - [Example PolyglotMarkdown Document](#example-polyglotmarkdown-document)
      - [Initializing the Parser](#initializing-the-parser)
      - [Parsing Metadata](#parsing-metadata-1)
      - [Parsing Metadata by Language](#parsing-metadata-by-language-1)
      - [Listing Available Languages](#listing-available-languages)
      - [Parsing Content by Language](#parsing-content-by-language)
  - [Using the PolyglotBuilder Python Class](#using-the-polyglotbuilder-python-class)
    - [Initialization](#initialization-1)
  - [In Closing](#in-closing)

<!-- Created with https://luciopaiva.com/markdown-toc/ -->

## Overview

This document introduces the *PolyglotMarkdown* format and the `PolyglotParser` Python class, which can parse and process *PolyglotMarkdown* documents. A goal of this project is to provide parsers in other programming languages in the near future.

The *PolyglotMarkdown* format is an extension of Markdown designed to allow authors to create documents that can be read in multiple languages. This format is designed to be simple to use, easy to read and write, and compatible with existing Markdown tools. It is also flexible enough to handle complex scenarios such as dynamic content inclusion, versioning, transclusion, and language-aware internal links.

The *PolyglotMarkdown* format is ideal for creating documents that need to be accessible in multiple languages, such as documentation, knowledge bases, and other multilingual content.

## Why Would I Use This Instead of Separate Files for Each Language?

Imagine you have 100 articles in your knowledge base, and right now you’re managing them in two languages. With separate Markdown files per language, that’s already 200 files to manage! As your business grows and you start adding more languages — 5 or even 10 — those 200 files will balloon into 500 or 1000. Painful!

But PolyglotMarkdown might be just what you're looking for. Instead of trying to keep every language version of your articles consistently worded and consistently formatted across a bunch of files, PolyglotMarkdown lets you manage everything in one tidy package. Want to add a new section to an article? You can do it in one place, and it’s there for every language. Need to delete something? One and done. No need to open a dozen files just to make the same small change for all the languages you support.

One of the significant advantages of PolyglotMarkdown is the ability to manage partially translated content. On many multilingual websites, some content of lesser value or interest is often left untranslated "for later" and may or may not get translated in the end. If you use a multi-file approach, you would have to copy and paste these sections across different language files. However, with PolyglotMarkdown's [fallback mechanism](#handling-fallback-language), you can maintain such sections in the fallback language, ensuring that they are still available to readers even if they aren't translated.

This approach is not just beneficial for content that might be translated in the future. There are sections of documents that often do not require translation at all, such as code snippets or mathematical formulas. With PolyglotMarkdown, these can remain in the fallback language, or be included as-is, without needing redundant copies across multiple files.

But PolyglotMarkdown isn’t all-or-nothing. If you already have separate files for each language and that works for you, great! You can combine them into a PolyglotMarkdown document using [transclusion](#5-transclusion-including-external-content) and enjoy the benefits without tossing your existing workflow out the window. It’s flexible.

So, if you’re looking to keep your sanity as your content grows and the number of languages you support increases, PolyglotMarkdown is worth considering. It’s like having one file to rule them all, making your work smoother, faster, and less stressful. And who doesn’t want that?

## Key Features

The *PolyglotMarkdown* format is simply an extension of the standard Markdown format, with additional features to support multilingual content. Here are some key features of the *PolyglotMarkdown* format:

### 1. Metadata Section

The document begins with a metadata block marked by `---` at the beginning and end. This section is used to define key information about the document, such as the title, author, date, and other relevant metadata. The metadata can be defined in multiple languages, allowing for language-specific metadata. Currently, the only top-level metadata keys specified in `PolyglotMarkdown` are [`fallback_lang`](#fallback-language-support) and [`versions`](#4-content-versioning). Any other metadata can be defined as needed, with each language-specific value nested under the appropriate key.

**Example:**

```markdown
---
title:
  en: "The Great Adventure"
  es: "La Gran Aventura"
author:
  en: "John Doe"
  es: "Juan Pérez"
date:
  en: "08-25-2024"
  es: "25-08-2024"
fallback_lang: "en"
versions:
  en: "1.0"
  es: "1.1"
---
```

#### Fallback Language Support

If content in the requested language is not available, you can specify a fallback language using the `fallback_lang` field in the metadata. This ensures that the reader can still access the content, even if it's not available in their preferred language.

**Example:**

```markdown
---
title:
  en: "The Great Adventure"
  es: "La Gran Aventura"
fallback_lang: "en"
---
```

### 2. Portion Marking

The content section of the document can be portion-marked by language in two ways:

1. **Per Line:**
   You can mark individual lines with a language code using the syntax `:[lang-code]:`. This allows for precise control over which language is displayed for each piece of content.

   **Example:**

   ```markdown
   :[en]: This is the first paragraph in English.
   :[es]: Este es el primer párrafo en español.
   ```

2. **Per Paragraph or Section:**
   Alternatively, you can group entire sections of content by language. Each block of content is marked with a specific language delimiter using the syntax `:[lang:lang-code]:`.

   **Example:**

   ```markdown
   :[lang:en]:
   This is a section in English.

   Here is another paragraph in English.

   :[lang:es]:
   Esta es una sección en español.

   Aquí hay otro párrafo en español.
   ```

### 3. Comments and Annotations

The *PolyglotMarkdown* format allows for language-specific comments or annotations. These can be used by authors or translators to provide context or notes within the document.

**Syntax:**
Use HTML-style comments with a language tag.

**Example:**

```markdown
<!-- Comment :[en]: This section needs review -->
:[en]: This is the content in English.

<!-- Comment :[es]: Esta sección necesita revisión -->
:[es]: Este es el contenido en español.
```

### 4. Content Versioning

*PolyglotMarkdown* supports versioning within the document, allowing authors to maintain different versions of content for different languages or editions. Versioning is defined in the metadata and referenced within the content using the `:[version "x.x"]:` syntax.

**Example:**

```markdown
---
versions:
  en: "1.1"
  es: "1.0"
---

:[version "1.0"]:
:[lang:en]: This is version 1.0 in English. It will not be shown, as the version is 1.1.
:[lang:es]: Esta es la versión 1.0 en español.

:[version "1.1"]:
:[lang:en]: This is version 1.1 in English.
:[lang:es]: Esta es la versión 1.1 en español. No se mostrará porque la versión es 1.0.
```

### 5. Transclusion (Including External Content)

*PolyglotMarkdown* supports transclusion, which allows you to include content from external files. This is useful for large projects where content is maintained in different places. You can include an entire file, ignoring language-specific sections, or include content for a specific language.

To include all content from an external file:

**Syntax:**
`:[include "file.md"]:`

To include only relevant content for the specified language in the final document (in the order such content appears in the source file):

**Syntax:**
`:[include "file.md" lang:code]:`

**Example:**

```markdown
:[include "bike_article.md" lang:en]:
```

### 6. Inter-document Links with Language Awareness

> **Note:** This feature is experimental and may require additional processing by the consuming application.

Create links to other documents that automatically resolve to the appropriate language version if it exists. This ensures that internal links work correctly regardless of the language being viewed.

**Syntax:**
Use `:[link "document.md" lang:code]:` to create language-aware links.

**Example:**

```markdown
:[en]: See the [related document](:[link "related_doc.md" lang:en]:).
:[es]: Consulte el [documento relacionado](:[link "related_doc.md" lang:es]:).
```

### 7. Multi-language Indexing and Search

The *PolyglotMarkdown* format is designed to support multi-language indexing and search capabilities. This allows users to search within the document in their preferred language. While the file format itself supports this, the implementation would typically be handled by the application consuming the *PolyglotMarkdown* files.

## Using the PolyglotParser Python Class

The `PolyglotParser` class is designed to parse *PolyglotMarkdown* texts and files, allowing you to extract and manipulate the content based on language, version, and other parameters.

### Initialization

To use the parser, instantiate it with the text of a *PolyglotMarkdown* document.

```python
from polyglot_parser import PolyglotParser

parser = PolyglotParser(markdown_string=content)
```

Alternately, if you have a file, you can read the content and pass the file path as a string:

```python
from polyglot_parser import PolyglotParser

parser = PolyglotParser(markdown_file="document.md")
```

Or as a `Path` object:

```python
from pathlib import Path
from polyglot_parser import PolyglotParser

file_path = Path("document.md")
parser = PolyglotParser(markdown_file=file_path)
```

### Parsing Metadata

The `parse_metadata()` method extracts the metadata from the document and returns it as a dictionary with language-specific values nested under their respective keys.

```python
metadata = parser.parse_metadata()
```

Example output:

```json
{
  "title": {"en": "The Great Adventure", "es": "La Gran Aventura"},
  "author": {"en": "John Doe", "es": "Juan Pérez"},
  "date": "2024-08-25",
  "fallback_lang": "en",
  "versions": {"en": "1.0", "es": "1.1"}
}
```

### Parsing Metadata by Language

The `parse_metadata_by_language()` method restructures the metadata, returning a dictionary organized by language.

```python
metadata_by_language = parser.parse_metadata_by_language()
```

Example output:
```json
{
  "en": {"title": "The Great Adventure", "author": "John Doe", "date": "2024-08-25", "fallback_lang": "en", "versions": "1.0"},
  "es": {"title": "La Gran Aventura", "author": "Juan Pérez", "date": "2024-08-25", "fallback_lang": "en", "versions": "1.1"}
}
```

### Parsing Content

The `parse_content()` method parses the content, separating it into language-specific sections and returning a dictionary where the keys are language codes and the values are the respective content.

```python
content = parser.parse_content()
```

### Handling Fallback Language

If content in the requested language is missing, the parser will automatically use the fallback language specified in the metadata.

```python
output = parser.convert_to_markdown(lang='es')  # Will fallback to 'en' if 'es' content is missing
```

### Handling Versioning

The `handle_versioning(version="x.x")` method allows you to filter content based on a specific version.

```python
parser.handle_versioning(version="1.1")
```

### Handling Transclusion

The `handle_transclusion()` method processes `:[include ...]:` tags, replacing them with the content of the specified files.

```python
parser.handle_transclusion()
```

### Example Usage

Let's consider a more comprehensive example that includes content in English, Spanish, and French, multiple sections, metadata, inter-document links, and external file transclusions.

#### Example Document Content

```python
content = """
---
title:
  en: "The Great Adventure"
  es: "La Gran Aventura"
  fr: "La Grande Aventure"
author:
  en: "John Doe"
  es: "Juan Pérez"
  fr: "Jean Dupont"
region:
  en: "United States of America"
  es: "Centroamérica"
  fr: "Europe"
date: "2024-09-05"
fallback_lang: "en"
versions:
  en: "1.0"
  es: "1.1"
  fr: "1.1"
---

:[lang:en]:
# Introduction

Welcome to **The Great Adventure**. This document will guide you through the basics of using PolyglotMarkdown.

:[lang:es]:
# Introducción

Bienvenidos a **La Gran Aventura**. Este documento le guiará a través de los conceptos básicos del uso de PolyglotMarkdown.

:[lang:fr]:
# Introduction

Bienvenue dans **La Grande Aventure**. Ce document vous guidera à travers les bases de l'utilisation de PolyglotMarkdown.

:[lang:en]:
# First Section

Here is the first section with some sample text in English.

# Versioned Content

:[lang:es]:
# Primera Sección

Aquí está la primera sección con un texto de ejemplo en español.

# Contenido Versionado

:[lang:fr]:
# Première Section

Voici la première section avec un exemple de texte en français.

# Contenu Versionné

:[version "1.0"]:
:[en]: - This English content is for version 1.0.

:[version "1.1"]:
:[en]: - This English version 1.1 bullet-point won't be used!
:[es]: - Este contenido en español es para la versión 1.1.
:[fr]: - Ce contenu en français est pour la version 1.1.

:[lang:en]:
# Inter-document Links

For more details, check out the [Advanced Features](:[link "advanced_features.md" lang:en]:) document, specifically section [Advanced Feature 1](:[link "advanced_features.md#advanced-feature-1" lang:en]:).

:[lang:es]:
# Enlaces entre Documentos

Para más detalles, consulte el documento [Funciones avanzadas](:[link "advanced_features.md" lang:es]:), específicamente la sección [Función Avanzada 1](:[link "advanced_features.md#función-avanzada-1" lang:es]:).

:[lang:fr]:
# Liens Inter-documents

Pour plus de détails, consultez le document [Fonctionnalités avancées](:[link "advanced_features.md" lang:fr]:), en particulier la section [Fonctionnalité Avancée 1](:[link "advanced_features.md#fonctionnalité-avancée-1" lang:fr]:).

:[lang:en]:
# External Content

Here is some included English content from an external file with a single language:
:[include "extra_content_en.md"]:

This content is also from an external file, but the file includes content for multiple languages:
:[include "external_file.md" lang:en]:

:[lang:es]:
# Contenido Externo

A continuación se incluye contenido en español desde un archivo externo con un solo idioma:
:[include "extra_content_es.md"]:

Este contenido también proviene de un archivo externo, pero el archivo incluye contenido para varios idiomas:
:[include "external_file.md" lang:es]:

:[lang:fr]:
# Contenu Externe

Voici quelques contenus français inclus à partir d’un fichier externe avec une seule langue:
:[include "extra_content_fr.md"]:

Ce contenu provient également d'un fichier externe, mais le fichier inclut du contenu pour plusieurs langues:
:[include "external_file.md" lang:fr]:

:[lang:en]:
# Conclusion

Thank you for reading **The Great Adventure**. We hope this helps you understand PolyglotMarkdown.

:[lang:es]:
# Conclusión

Gracias por leer **La Gran Aventura**. Esperamos que esto le ayude a comprender PolyglotMarkdown.

:[lang:fr]:
# Conclusion

Merci d'avoir lu **La Grande Aventure**. Nous espérons que cela vous aidera à comprendre PolyglotMarkdown.
"""
```

#### External Files

**extra_content_en.md:**

```markdown
This is additional content in English, included from an external file.
```

**extra_content_es.md:**

```markdown
Este es contenido adicional en español, incluido desde un archivo externo.
```

**extra_content_fr.md:**

```markdown
Voici du contenu supplémentaire en français, inclus à partir d'un fichier externe.
```

**external_file.md:**

```markdown
:[lang:en]:
This content...

... is included from an external English file.

:[lang:es]:
Este contenido...

... se incluye desde un archivo externo en español.

:[lang:fr]:
Ce contenu...

... est inclus à partir d'un fichier externe en français.

:[es]: Quiero una manzana.

:[en]: I want an apple.

:[fr]: Je veux une pomme.
```

**advanced_features.md:**

```markdown
:[lang:en]:
This document contains advanced features.

# Advanced Feature 1

This is the first advanced feature.

:[lang:es]:
Este documento contiene funciones avanzadas.

# Función Avanzada 1

Esta es la primera función avanzada.

:[lang:fr]:
Ce document contient des fonctionnalités avancées.

# Fonctionnalité Avancée 1

Ceci est la première fonctionnalité avancée.
```

#### Parsing and Handling the Document

Here’s how you can use the `PolyglotParser` to handle this document:

```python
# Create the parser instance
parser = PolyglotParser(markdown_string=content)

# Parse the metadata
parser.parse_metadata()

# Parse the content
parser.parse_content()

# Handle versioning (we'll work with version 1.1 in this example)
parser.handle_versioning(version="1.1")

# Handle transclusion (including content from external files)
parser.handle_transclusion()

# Convert the content to markdown for a specific language
english_output = parser.convert_to_markdown(lang='en')
spanish_output = parser.convert_to_markdown(lang='es')
french_output = parser.convert_to_markdown(lang='fr')

# Print the outputs for each language
print("English Output:\n", english_output)
print("\nSpanish Output:\n", spanish_output)
print("\nFrench Output:\n", french_output)
```

#### Expected Output

Given the document content and the external files, the expected output for each language is as follows:

**English Output:**

```markdown
# Introduction

Welcome to **The Great Adventure**. This document will guide you through the basics of using PolyglotMarkdown.

# First Section

Here is the first section with some sample text in English.

# Versioned Content

- This English content is for version 1.0.

# Inter-document Links

For more details, check out the [Advanced Features](advanced_features.md) document, specifically section [Advanced Feature 1](advanced_features.md#advanced-feature-1).

# External Content

Here is some included English content from an external file with a single language:
This is additional content in English, included from an external file.

This content is also from an external file, but the file includes content for multiple languages:
This content...

... is included from an external English file.

I want an apple.

# Conclusion

Thank you for reading **The Great Adventure**. We hope this helps you understand PolyglotMarkdown.
```

**Spanish Output:**

```markdown
# Introducción

Bienvenidos a **La Gran Aventura**. Este documento le guiará a través de los conceptos básicos del uso de PolyglotMarkdown.

# Primera Sección

Aquí está la primera sección con un texto de ejemplo en español.

# Contenido Versionado

- Este contenido en español es para la versión 1.1.

# Enlaces entre Documentos

Para más detalles, consulte el documento [Funciones avanzadas](advanced_features.md), específicamente la sección [Función Avanzada 1](advanced_features.md#función-avanzada-1).

# Contenido Externo

A continuación se incluye contenido en español desde un archivo externo con un solo idioma:
Este es contenido adicional en español, incluido desde un archivo externo.

Este contenido también proviene de un archivo externo, pero el archivo incluye contenido para varios idiomas:
Este contenido...

... se incluye desde un archivo externo en español.

Quiero una manzana.

# Conclusión

Gracias por leer **La Gran Aventura**. Esperamos que esto le ayude a comprender PolyglotMarkdown.
```

**French Output:**

```markdown
# Introduction

Bienvenue dans **La Grande Aventure**. Ce document vous guidera à travers les bases de l'utilisation de PolyglotMarkdown.

# Première Section

Voici la première section avec un exemple de texte en français.

# Contenu Versionné

- Ce contenu en français est pour la version 1.1.

# Liens Inter-documents

Pour plus de détails, consultez le document [Fonctionnalités avancées](advanced_features.md), en particulier la section [Fonctionnalité Avancée 1](advanced_features.md#fonctionnalité-avancée-1).

# Contenu Externe

Voici quelques contenus français inclus à partir d’un fichier externe avec une seule langue:
Voici du contenu supplémentaire en français, inclus à partir d'un fichier externe.

Ce contenu provient également d'un fichier externe, mais le fichier inclut du contenu pour plusieurs langues:
Ce contenu...

... est inclus à partir d'un fichier externe en français.

Je veux une pomme.

# Conclusion

Merci d'avoir lu **La Grande Aventure**. Nous espérons que cela vous aidera à comprendre PolyglotMarkdown.
```

### Using the PolyglotParser Methods

Here, we demonstrate how to use various methods of the `PolyglotParser` Python class and the resulting output for each, using the example PolyglotMarkdown text provided below.

#### Example PolyglotMarkdown Document

```markdown
---
title:
  en: "The Great Adventure"
  es: "La Gran Aventura"
author:
  en: "John Doe"
  es: "Juan Pérez"
date: "2024-08-25"
fallback_lang: "en"
versions:
  en: "1.0"
  es: "1.1"
---

# Introduction

:[lang:en]:
This is the introduction in **English**.

:[lang:es]:
Esta es la introducción en **español**.

:[lang:en]:
This is the More English content.

:[lang:es]:
Este es el contenido Más español.
```

#### Initializing the Parser

To begin, initialize the `PolyglotParser` with the markdown content.

```python
parser = PolyglotParser(markdown_string=text)
```

#### Parsing Metadata

The `parse_metadata()` method extracts the metadata from the document, including the title, author, date, fallback language, and versions.

```python
metadata = parser.parse_metadata()
print(metadata)
```

**Expected Output:**

```json
{
  "title": {
    "en": "The Great Adventure",
    "es": "La Gran Aventura"
  },
  "author": {
    "en": "John Doe",
    "es": "Juan Pérez"
  },
  "date": "2024-08-25",
  "fallback_lang": "en",
  "versions": {
    "en": "1.0",
    "es": "1.1"
  }
}
```

#### Parsing Metadata by Language

The `parse_metadata_by_language()` method organizes the metadata by language, providing a view where each language has its corresponding metadata.

```python
metadata_by_language = parser.parse_metadata_by_language()
print(metadata_by_language)
```

**Expected Output:**

```json
{
  "en": {
    "title": "The Great Adventure",
    "author": "John Doe",
    "date": "2024-08-25",
    "fallback_lang": "en",
    "versions": "1.0"
  },
  "es": {
    "title": "La Gran Aventura",
    "author": "Juan Pérez",
    "date": "2024-08-25",
    "fallback_lang": "en",
    "versions": "1.1"
  }
}
```

#### Listing Available Languages

The `list_languages()` method identifies all the languages available in the document.

```python
languages = parser.list_languages()
print(languages)
```

**Expected Output:**

```python
['es', 'en']
```

#### Parsing Content by Language

The `parse_content()` method parses the content, returning a dictionary where the keys are the language codes, and the values are the content in those respective languages.

```python
content = parser.parse_content()
print(content)
```

**Expected Output:**

```json
{
  "es": "Esta es la introducción en **español**.\n\nEste es el contenido Más español.",
  "en": "This is the introduction in **English**.\n\nThis is the More English content."
}
```

To retrieve the content for only one language, falling back to `fallback_language` for any missing content, you can use the `parse_content()` method along with the `lang` parameter.

```python
english_content = parser.parse_content(lang='en')
print(english_content)
```

These methods allow you to extract and manage multilingual content and metadata in PolyglotMarkdown texts and documents.

## Using the PolyglotBuilder Python Class

> **Note:** This feature is planned for future development.

The `PolyglotBuilder` class is designed to create *PolyglotMarkdown* documents programmatically. This class provides methods to set metadata, add content in multiple languages, and handles versioning and transclusion.

### Initialization

To use the builder, instantiate it with the desired metadata and content.

```python
from polyglot_builder import PolyglotBuilder

builder = PolyglotBuilder()
```

## In Closing

- The *PolyglotMarkdown* format provides a way to manage multilingual content in a single document or a set of documents. This format ensures that your content is accessible, maintainable, and easy to work with across multiple languages.
- The `PolyglotParser` class offers a robust tool for parsing, processing, and converting *PolyglotMarkdown* documents, making it easy to integrate *PolyglotMarkdown* into your existing workflows and applications.
- The `PolyglotBuilder` class, planned for future development, will provide a way to create *PolyglotMarkdown* documents programmatically, enabling you to generate multilingual content dynamically.
