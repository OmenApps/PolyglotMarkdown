# PolyglotMarkdown
A versatile Markdown extension designed to manage and display multilingual content within a single document, offering powerful features like language-specific content inclusion, multi-language metadata support, and version control for a global audience.

This project is a *Work in Progress*

## Overview

This document introduces the *PolyglotMarkdown* format and the `PolyglotParser` Python class, which can parse and process *PolyglotMarkdown* documents. A goal of this project is to provide parsers in other programming languages in the near future.

The *PolyglotMarkdown* format is an extension of Markdown designed to allow authors to create documents that can be read in multiple languages. This format is designed to be simple to use, easy to read and write, and compatible with existing Markdown tools. It is also flexible enough to handle complex scenarios such as dynamic content inclusion, versioning, transclusion, and language-aware internal links.

The *PolyglotMarkdown* format is ideal for creating documents that need to be accessible in multiple languages, such as documentation, knowledge bases, and other multilingual content.

## Why Would I Use This Instead of Separate Files for Each Language?

Imagine you have 100 articles in your knowledge base, and right now you’re managing them in two languages. With separate Markdown files per language, that’s already 200 files to manage! As your business grows and you start adding more languages — 5 or even 10 — those 200 files will balloon into 500 or 1000. Painful!

But PolyglotMarkdown might be just what you're looking for. Instead of trying to keep every language version of your articles consistently worded and consistently formatted across a bunch of files, PolyglotMarkdown lets you manage everything in one tidy package. Want to add a new section to an article? You can do it in one place, and it’s there for every language. Need to delete something? One and done. No need to open a dozen files just to make the same small change for all the languages you support.

But PolyglotMarkdown isn’t all-or-nothing. If you already have separate files for each language and that works for you, great! You can combine them into a PolyglotMarkdown document using [transclusion](README.md#5-transclusion-including-external-content) and enjoy the benefits without tossing your existing workflow out the window. It’s flexible.

So, if you’re looking to keep your sanity as your content grows and the number of languages you support increases, PolyglotMarkdown is worth considering. It’s like having one file to rule them all, making your work smoother, faster, and less stressful. And who doesn’t want that?

## Key Features

### 1. Metadata Section

The document begins with a metadata block marked by `---` at the beginning and end. This section is used to define key information about the document, such as the title, author, date, and other relevant metadata. The metadata can be defined in multiple languages, allowing for language-specific metadata.

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

**Fallback Language Support**

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

### 2. Content Section

The content section of the document can be structured in two ways:

1. **Portion-marked (Per Paragraph or Sentence):**  
   You can mark individual paragraphs or sentences with a language code using the syntax `:[lang-code]:`. This allows for precise control over which language is displayed for each piece of content.

   **Example:**

   ```markdown
   :[en]: This is the first paragraph in English.
   :[es]: Este es el primer párrafo en español.
   ```

2. **Grouped by Language:**  
   Alternatively, you can group entire sections of content by language. Each block of content is marked with a specific language delimiter using the syntax `:[lang:code]:`.

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
<!-- Comment [en]: This section needs review -->
:[en]: This is the content in English.

<!-- Comment [es]: Esta sección necesita revisión -->
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

*PolyglotMarkdown* supports transclusion, which allows you to include content from external files. This is useful for large projects where content is maintained in different places. All relevant content for the specified language will be included in the final document, in the order it appears in the source file.

**Syntax:**  
Use `:[include "file.md" lang:code]:` to include external content.

**Example:**

```markdown
:[include "section1.md" lang:en]:
```

### 6. Inter-document Links with Language Awareness

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

## Using the PolyglotParser

The `PolyglotParser` class is designed to parse *PolyglotMarkdown* documents, allowing you to extract and manipulate the content based on language, version, and other parameters.

### Initialization

To use the parser, instantiate it with the content of a *PolyglotMarkdown* document.

```python
parser = PolyglotParser(content)
```

### Parsing Metadata

The `parse_metadata()` method extracts the metadata from the document.

```python
parser.parse_metadata()
```

### Parsing Content

The `parse_content()` method parses the content, separating it into language-specific sections.

```python
parser.parse_content()
```

### Handling Fallback Language

If content in the requested language is missing, the parser will automatically use the fallback language specified in the metadata.

```python
print(parser.convert_to_markdown(lang='es'))  # Will fallback to 'en' if 'es' content is missing
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

Let's consider a document that includes content in English, Spanish, and French. The document has different sections for each language, some versioned content, and includes an external file. We will walk through how to parse and work with this document using the `PolyglotParser`.

#### Example Document Content

```markdown
---
title: 
  en: "The Great Adventure"
  es: "La Gran Aventura"
  fr: "La Grande Aventure"
author: 
  en: "John Doe"
  es: "Juan Pérez"
  fr: "Jean Dupont"
date: "2024-08-25"
fallback_lang: "en"
versions:
  en: "1.0"
  es: "1.1"
  fr: "1.1"
---

# Introduction

:[lang:en]:
This is the introduction to the document in English.

:[lang:es]:
Esta es la introducción al documento en español.

:[lang:fr]:
Ceci est l'introduction du document en français.

# Content Section

:[lang:en]:
This is the first content section in English.  
:[include "external_file_en.md" lang:en]:  
Here is some more English content.

:[lang:es]:
Esta es la primera sección de contenido en español.  
:[include "external_file_es.md" lang:es]:  
Aquí hay más contenido en español.

:[lang:fr]:
Ceci est la première section de contenu en français.  
:[include "external_file_fr.md" lang:fr]:  
Voici un autre contenu en français.

# Versioned Content

:[version "1.1"]:
:[lang:en]: This content is only available in version 1.1 in English.
:[lang:es]: Este contenido solo está disponible en la versión 1.1 en español.
:[lang:fr]: Ce contenu n'est disponible que dans la version 1.1 en français.

# Conclusion

:[lang:en]:
This is the conclusion in English.

:[lang:es]:
Esta es la conclusión en español.

:[lang:fr]:
Ceci est la conclusion en français.
```

#### External File

The document includes an external file with content for each language (in any order):

**external_file.md:**

```markdown
:[lang:en]:
This content is included from an external English file.

:[lang:es]:
Este contenido se incluye desde un archivo externo en español.

:[lang:fr]:
Ce contenu est inclus à partir d'un fichier externe en français.

:[lang:es]:
Quiero una manzana.

:[lang:en]:
I want an apple.

:[lang:fr]:
Je veux une pomme.
```

#### Parsing and Handling the Document

Here’s how you can use the `PolyglotParser` to handle this document:

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
date: "2024-08-25"
fallback_lang: "en"
versions:
  en: "1.0"
  es: "1.1"
  fr: "1.1"
---

# Introduction

:[lang:en]:
This is the introduction to the document in English.

:[lang:es]:
Esta es la introducción al documento en español.

:[lang:fr]:
Ceci est l'introduction du document en français.

# Content Section

:[lang:en]:
This is the first content section in English.  
:[include "external_file.md" lang:en]:  
Here is some more English content.

:[lang:es]:
Esta es la primera sección de contenido en español.  
:[include "external_file.md" lang:es]:  
Aquí hay más contenido en español.

:[lang:fr]:
Ceci est la première section de contenu en français.  
:[include "external_file.md" lang:fr]:  
Voici un autre contenu en français.

# Versioned Content

:[version "1.1"]:
:[lang:en]: This content is only available in version 1.1 in English.
:[lang:es]: Este contenido solo está disponible en la versión 1.1 en español.
:[lang:fr]: Ce contenu n'est disponible que dans la version 1.1 en français.

# Conclusion

:[lang:en]:
This is the conclusion in English.

:[lang:es]:
Esta es la conclusión en español.

:[lang:fr]:
Ceci est la conclusion en français.
"""

# Create the parser instance
parser = PolyglotParser(content)

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
This is the introduction to the document in English.

This is the first content section in English.  
This content is included from an external English file.

I want an apple.  

Here is some more English content.

This content is only available in version 1.1 in English.

This is the conclusion in English.
```

**Spanish Output:**

```markdown
Esta es la introducción al documento en español.

Esta es la primera sección de contenido en español.  
Este contenido se incluye desde un archivo externo en español.

Quiero una manzana.  

Aquí hay más contenido en español.

Este contenido solo está disponible en la versión 1.1 en español.

Esta es la conclusión en español.
```

**French Output:**

```markdown
Ceci est l'introduction du document en français.

Ceci est la première section de contenu en français.  
Ce contenu est inclus à partir d'un fichier externe en français.

Je veux une pomme.  

Voici un autre contenu en français.

Ce contenu n'est disponible que dans la version 1.1 en français.

Ceci est la conclusion en français.
```

### Explanation of the Process

1. **Parsing Metadata:** The parser first extracts and processes the metadata section, including the fallback language and versions available for each language.
   
2. **Parsing Content:** The content is then parsed, and language-specific blocks are separated and stored in a dictionary within the parser.

3. **Handling Versioning:** The parser filters out content that is not applicable to the specified version (in this case, version 1.1).

4. **Handling Transclusion:** The parser processes any `:[include ...]:` tags, replacing them with the content from the corresponding external files.

5. **Generating Output:** Finally, the content is converted back to Markdown for each specified language, using the `convert_to_markdown` method. The fallback mechanism ensures that if any content is missing for a requested language, it defaults to the fallback language specified in the metadata.

## In Closing

- The *PolyglotMarkdown* format provides a way to manage multilingual content in a single document or a set of documents. This format ensures that your content is accessible, maintainable, and easy to work with across multiple languages.
- The `PolyglotParser` class offers a robust tool for parsing, processing, and converting *PolyglotMarkdown* documents, making it easy to integrate *PolyglotMarkdown* into your existing workflows and applications.