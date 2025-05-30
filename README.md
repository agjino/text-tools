# Breaklines

A Python utility that inserts line breaks in text files to ensure no line exceeds a specified
character limit.

## Description

Breaklines is a Python script that processes text files by adding line breaks to any line that
exceeds a maximum character length. The line break is intelligently placed at the last space before
the maximum length is reached, preserving word boundaries.

## Installation

Make the script executable:

```bash
chmod +x breaklines
```

Ensure the script is in your system path to run it from anywhere.

## Usage

```bash
breaklines <filename> [max_chars] [--inplace]
```

### Parameters

- **filename**: Path to the file to process (required)
- **max_chars**: Maximum number of characters per line (optional, default: 100)
- **inplace**: Whether to create a backup of the original file (optional, if not specified, a backup
will be created)

### Example

```bash
breaklines myfile.txt 100
```

This will process `myfile.txt` ensuring no line exceeds 100 characters.
