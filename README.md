# DB-Import Text-Sanitizer

A lightweight, standalone web-based utility designed to inspect, detect, and sanitize problematic characters in text and data files before importing them into databases or ETL pipelines.

It runs **100% locally in your web browser** with zero installation, no backend server, and no external dependencies.

---

## 🎯 What Problem Does It Solve?

When importing datasets (CSV, TSV, SQL dumps, JSON, TXT) into relational databases (such as PostgreSQL, MySQL, SQL Server, or Oracle), imports frequently fail, crash, or introduce silent data corruption due to invisible, malformed, or unexpected characters:

- **Invisible & Zero-Width Characters:** Hidden zero-width spaces or Byte-Order-Marks (BOM) make string comparisons fail, break unique constraints, or cause primary key lookups to return no results.
- **Typographic "Smart" Quotes and Dashes:** Pasting from Microsoft Word, Outlook, or websites introduces typographic quotes (`“`, `”`, `„`) and em/en dashes (`–`, `—`) that break CSV column delimiters or SQL string literals.
- **Mojibake & Encoding Mismatches:** Files saved as UTF-8 but read as ANSI/Windows-1252 result in broken sequences such as `JÃ¼rgen` instead of `Jürgen` or `â‚¬` instead of `€`.
- **Homoglyphs (Lookalike Characters):** Greek or Cyrillic characters that look visually identical to Latin letters (e.g., Greek `Μ` vs. Latin `M`, or Cyrillic `С` vs. Latin `C`) lead to duplicated accounts, search indexing bugs, or security vulnerabilities.
- **Non-Breaking Spaces (NBSP):** Web-copied text often contains `U+00A0` spaces that standard regex or string trims do not remove.
- **Escape & Control Characters:** Unparsed escape sequences like literal `\n` or invisible binary control codes (e.g., `BEL`, `NUL`) corrupt row structures.

**TextSanitizer** highlights every anomaly line by line with clear color-coded badges, explains the exact codepoints and issues, and allows you to auto-clean or manually edit them before exporting a clean, UTF-8 encoded file.

---

## ✨ Key Features

- **🔒 100% Private & Client-Side:** All processing happens entirely within your web browser using HTML5 File APIs. Your confidential or personal data is never uploaded to any server or cloud.
- **⚡ Standalone & Zero-Install:** Just open [`src/TextSanitizer.html`](file:///c:/Users/virtu/Meine%20Ablage/Meine%20Projekte/Projects/TextSanitizer/src/TextSanitizer.html) in any modern browser (Chrome, Edge, Firefox, Safari). No Node.js, Python, or command line required.
- **📂 Broad File Format Support:** Handles `.csv`, `.tsv`, `.txt`, `.sql`, `.json`, `.xml`, and `.log` files.
- **🔤 Encoding Selection:** Choose your source encoding (`UTF-8`, `ANSI / Windows-1252`, or `ISO-8859-1 / Latin-1`) to properly decode older files before scanning.
- **🔍 7 Dedicated Detection Categories:** Quickly see how many issues exist across 7 distinct categories with real-time statistics.
- **🎯 Interactive Categorical Filtering:** Click on any category card to filter the view and focus solely on specific errors.
- **🛠️ Flexible Remediation Options:**
  - **Batch Auto-Clean ("Fix All"):** Fix all detected issues across the entire file with a single click.
  - **Single Line Auto-Clean:** Clean issues row by row.
  - **Inline Manual Editor:** Manually edit and re-evaluate any line directly inside the app.
- **🚀 Standardized UTF-8 Export:** Download your sanitized data with one click as a UTF-8 encoded file named `<filename>_sanitized.<ext>`, ready for database import.
- **🧪 Built-in Demo Dataset:** Click **"Demo-Daten laden"** to test and preview all 7 error categories immediately without needing your own test file.

---

## 🛡️ The 7 Detection Categories

TextSanitizer examines every line against seven common data quality pitfalls:

| Category | Description | Common Characters / Examples | Default Sanitization Action |
| :--- | :--- | :--- | :--- |
| **Invisible / BOM** | Zero-width characters and file markers that cannot be seen with the naked eye. | Zero-Width Space (`U+200B`), Zero-Width Joiner (`U+200D`), Byte-Order-Mark (`U+FEFF`), Soft Hyphen (`U+00AD`) | Removed completely |
| **Non-Breaking Spaces (NBSP)** | Special space characters commonly copied from word processors or websites. | Non-Breaking Space (`U+00A0`), Narrow NBSP (`U+202F`), Figure Space (`U+2007`), Typographic Spaces | Replaced with standard ASCII space (` `) |
| **Homoglyphs** | Greek or Cyrillic letters that appear identical to standard Latin alphabet characters. | Greek `Α`, `Β`, `Ε`, `Μ`, `Ο`, `Τ` or Cyrillic `А`, `В`, `С`, `Е`, `О`, `Р`, `а`, `е`, `о`, `с` | Replaced with corresponding Latin letter (e.g., `A`, `B`, `C`, etc.) |
| **Mojibake & Encoding** | Broken character sequences caused by incorrect encoding interpretations, or Unicode replacement marks. | `Ã¤` (for `ä`), `Ã¼` (for `ü`), `Ã¶` (for `ö`), `ÃŸ` (for `ß`), `â‚¬` (for `€`), Replacement Char `U+FFFD` (``) | Reconstructed into intended character (e.g., `ä`, `ü`, `ö`, `€`, `-`) |
| **Dashes & Smart Quotes** | Typographic punctuation from office software that breaks SQL strings or CSV quotes. | Curly quotes (`“`, `”`, `„`, `‘`, `’`, `«`, `»`), En/Em dashes (`–`, `—`, `−`) | Converted to standard ASCII quotes (`"`, `'`) and standard hyphen (`-`) |
| **Escape & Control Chars** | Raw binary control codes or literal unescaped characters. | Literal strings like `\n`, `\r`, `\t` and non-printable control bytes (`U+0000`–`U+001F`, `U+007F`–`U+009F` such as BEL `\u0007`) | Literal escapes replaced with space; control codes stripped |
| **Non-Unicode / Exotics** | Exotic symbols, unpaired surrogates, or non-standard Unicode ranges. | Unpaired surrogates (`0xD800`–`0xDFFF`), Unicode Non-Characters (`U+FFFF`, `U+FFFE`), Private Use Area (`U+E000`–`U+F8FF`) | Stripped or flagged for manual review |

---

## 📖 How to Use

### 1. Open the Tool
Double-click or open [`src/TextSanitizer.html`](file:///c:/Users/virtu/Meine%20Ablage/Meine%20Projekte/Projects/TextSanitizer/src/TextSanitizer.html) in your browser.

### 2. Load Your File
- **Drag & Drop:** Drag any supported file (`.csv`, `.tsv`, `.txt`, `.sql`, `.json`, `.xml`, `.log`) into the designated drop zone.
- **File Browser:** Click on the drop zone to open the system file picker.
- **Demo Mode:** Click **"Demo-Daten laden" (Load Demo Data)** in the top right to test the engine with sample corrupted records.

### 3. Verify Source Encoding
If your input file was saved in an older Windows legacy format (e.g., German/Western European ANSI), select **"ANSI / Windows-1252"** or **"ISO-8859-1"** in the **Quell-Encoding** dropdown. The file is instantly re-decoded and re-analyzed.

### 4. Review Detected Issues
- Check the **header summary** for total lines and count of problematic lines.
- Click any of the **category cards** in the grid to filter rows and focus exclusively on specific issues.
- Leave **"Nur auffällige Zeilen anzeigen" (Show only problematic lines)** checked to hide clean rows, or uncheck it to view every line.
- Hover over any colored badge in the code preview to see a tooltip with the exact position, character name, Unicode hex code, and recommended fix.

### 5. Sanitize Your Data
You can fix issues using three methods depending on your needs:
- **Clean All Automatically:** Click the yellow **"Alle Fehler automatisch bereinigen" (Auto-clean all errors)** button in the top navigation to repair all issues throughout the entire file.
- **Clean Line by Line:** Click **"Zeile auto-bereinigen" (Auto-clean line)** on any specific row.
- **Manual Editing:** Click **"Manuell bearbeiten" (Edit manually)** to expand an editable text field, modify the line directly, and click **"Übernehmen & Prüfen" (Apply & Re-check)**.

### 6. Export the Sanitized File
Click the green **"Als UTF-8 Datei exportieren" (Export as UTF-8 file)** button. The clean content will be saved to your downloads folder with `_sanitized` appended to the original filename (e.g., `customers_sanitized.csv`), encoded cleanly in standard UTF-8.

---

## 🔒 Security & Privacy

- **No Remote Calls:** The application makes zero HTTP/HTTPS requests to external servers, APIs, or CDNs.
- **In-Memory Only:** File parsing and sanitization take place entirely in your computer's RAM using JavaScript's native `TextDecoder` and `Blob` APIs.
- **Compliance:** Suitable for handling sensitive business data, credentials, personally identifiable information (PII), or GDPR-restricted records.

---

## 📁 Repository Structure

```text
TextSanitizer/
├── README.md               # User guide and documentation (this file)
└── src/
    └── TextSanitizer.html  # Standalone HTML/CSS/JS application
```