# Audiobook Organizer

CLI tool to organize audiobooks based on metadata.json files.

## Features

- Organizes audiobooks by author/series/title structure
- Handles multiple authors
- Preserves spaces by default
- Optional space replacement with custom character
- Dry-run mode to preview changes
- Interactive prompt mode for reviewing moves
- Undo functionality
- Colored output
- Operation logs for recovery
- Separate input/output directory support

## Installation

### Ubuntu/Debian

```bash
# Add the repository GPG key
curl -fsSL https://github.com/yourusername/audiobook-organizer/raw/main/key.gpg | sudo gpg --dearmor -o /usr/local/share/keyrings/audiobook-organizer.gpg

# Add repository
echo "deb [signed-by=/usr/local/share/keyrings/audiobook-organizer.gpg] https://github.com/yourusername/audiobook-organizer/releases/latest/download/ /" | sudo tee /etc/apt/sources.list.d/audiobook-organizer.list

# Update package list
sudo apt update

# Install the package
sudo apt install audiobook-organizer
```

### Go Install

```bash
go install github.com/yourusername/audiobook-organizer@latest
```

### Docker
```bash
docker pull jeffsui/audiobook-organizer:latest
```

## Usage

Basic organization:
```bash
# Organize in place
audiobook-organizer --dir=/path/to/audiobooks

# Organize to separate output directory
audiobook-organizer --dir=/path/to/source/audiobooks --out=/path/to/organized/audiobooks
```

Options:
- `--dir`: Base directory to scan (required)
- `--out`: Output directory for organized files (optional, defaults to --dir if not specified)
- `--replace_space`: Character to replace spaces (optional)
- `--dry-run`: Preview changes without moving files
- `--verbose`: Show detailed progress
- `--undo`: Restore files to original locations
- `--prompt`: Review and confirm each book move interactively

### Docker Usage Examples

Basic usage with single directory:
```bash
# Process current directory
docker run -v $(pwd):/books \
  jeffsui/audiobook-organizer --dir=/books

# Process specific directory
docker run -v /path/to/audiobooks:/books \
  jeffsui/audiobook-organizer --dir=/books
```

Separate input and output directories:
```bash
# Mount source and destination directories
docker run \
  -v /path/to/source:/input:ro \
  -v /path/to/destination:/output \
  jeffsui/audiobook-organizer --dir=/input --out=/output

# Use current directory as source, output to specific directory
docker run \
  -v $(pwd):/input:ro \
  -v /path/to/organized:/output \
  jeffsui/audiobook-organizer --dir=/input --out=/output
```

Interactive mode with input/output:
```bash
# Interactive prompt mode with separate directories
docker run -it \
  -v /path/to/source:/input:ro \
  -v /path/to/destination:/output \
  jeffsui/audiobook-organizer --dir=/input --out=/output --prompt
```

Dry run with verbose output:
```bash
# Preview changes without moving files
docker run \
  -v /path/to/source:/input:ro \
  -v /path/to/destination:/output \
  jeffsui/audiobook-organizer --dir=/input --out=/output --dry-run --verbose
```

### Docker Volume Mounting Notes

- Use `:ro` for read-only access to source directories
- The container paths must match the `--dir` and `--out` parameters
- Use `-it` flag when running with `--prompt` for interactive mode
- Multiple directories can be mounted for source/destination separation
- Source and destination can be the same directory if desired
- Log files will be written to the output directory

### Interactive Mode

Using the `--prompt` flag will show each book's details and proposed move location:

```
Book found:
  Title: The Book Title
  Authors: Author One, Author Two
  Series: Amazing Series #1

Proposed move:
  From: /input/original/path/book
  To: /output/Author One,Author Two/Amazing Series #1/The Book Title

Proceed with move? [y/N]
```

## Metadata Format

Expects metadata.json files with structure:
```json
{
  "authors": ["Author Name"],
  "title": "Book Title",
  "series": ["Series Name #1"]
}
```

## Directory Structure

Without series:
```
/output/Author Name/Book Title/
```

With series:
```
/output/Author Name/Series Name #1/Book Title/
```

With multiple authors:
```
/output/Author One,Author Two/Book Title/
```

With space replacement (--replace_space="."):
```
/output/Author.Name/Series.Name.#1/Book.Title/
```

## Recovery

Operations are logged to `.abook-org.log` in the output directory. Use `--undo` to restore files to their original locations:

```bash
# Undo with same input/output configuration
docker run \
  -v /path/to/source:/input \
  -v /path/to/destination:/output \
  jeffsui/audiobook-organizer --dir=/input --out=/output --undo
```


