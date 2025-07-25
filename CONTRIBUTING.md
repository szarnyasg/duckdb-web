# Contributing

* [Contributing](#contributing)
  * [Code of Conduct](#code-of-conduct)
  * [Contributing to the DuckDB Documentation](#contributing-to-the-duckdb-documentation)
  * [Eligibility](#eligibility)
  * [Adding a New Page](#adding-a-new-page)
  * [Style Guide](#style-guide)
    * [Formatting](#formatting)
    * [Headers](#headers)
    * [SQL Style](#sql-style)
    * [Python Style](#python-style)
    * [Spelling](#spelling)
  * [Example Code Snippets](#example-code-snippets)
  * [Cross-References](#cross-references)
  * [Preview, Stable and Versioned Pages](#preview-stable-and-versioned-pages)
  * [Generated Pages](#generated-pages)
    * [Generated SQL Function Lists](#generated-sql-function-lists)
  * [Notice](#notice)

## Code of Conduct

This project and everyone participating in it is governed by a [Code of Conduct](code_of_conduct.md). By participating, you are expected to uphold this code. Please report unacceptable behavior to [quack@duckdb.org](mailto:quack@duckdb.org).

## Contributing to the DuckDB Documentation

Contributions to the [DuckDB Documentation](https://duckdb.org/) are welcome. To submit a contribution, please open a [pull request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) in the [`duckdb/duckdb-web`](https://github.com/duckdb/duckdb-web) repository.

## Eligibility

Before submitting a contribution, please check whether your contribution is eligible.

1. Before creating a new page, please search the existing documentation for similar pages.
2. In general, guides for third-party tools using DuckDB should not be included in the DuckDB documentation. Rather, these tools and their documentation should be collected in the [Awesome DuckDB community repository](https://github.com/davidgasquez/awesome-duckdb).

## Adding a New Page

Thank you for contributing to the DuckDB documentation!

Each new page requires at least 2 edits:

* Create new Markdown file (using the `snake_case` naming convention). Please follow the format of another `.md` file in the `docs/stable` folder.
* Add a link to the new page within `_data/menu_docs_stable.json`. This populates the side menu.

The addition of a new guide requires one additional edit:

* Add a link to the new page within the Guides landing page: `docs/guides/overview.md`

Before creating a pull request, please perform the following steps:

* Preview your changes in the browser using the [site build guide](BUILDING.md).
* Run the linters with `scripts/lint.sh` to show potential issues and run `scripts/lint.sh -f` to perform the fixes for markdownlint.

When creating a PR, please check the box to "Allow edits from maintainers".

## Style Guide

Please adhere the following style guide when submitting a pull request.

Some of this style guide is automated with GitHub Actions, but feel free to run [`scripts/lint.sh`](scripts/lint.sh) to run them locally.

### Formatting

* Use [GitHub's Markdown syntax](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) for formatting.
* Do not hard-wrap lines in blocks of text.
* Format code blocks with the appropriate language (e.g., \`\`\`sql CODE\`\`\`).
* For a SQL code block to start with DuckDB's `D` prompt, use the `plsql` language tag (e.g., \`\`\`plsql CODE\`\`\`). This uses the same syntax highlighter as SQL, the only difference is the presence of the `D` prompt.
* Code blocks using the `bash` language tag automatically receive `$` prompt when rendered. To typeset a Bash code block without a prompt, use the `batch` language tag (e.g., \`\`\`batch CODE\`\`\`). This uses the same syntax highlighter as Bash, the only difference is the absence of the prompt.
* To display blocks of text without a language (e.g., output of a script), use \`\`\`text OUTPUT\`\`\`.
* To display error messages, use \`\`\`console ERROR MESSAGE\`\`\`.
* Quoted blocks (lines starting with `>`) are rendered as [a colored box](https://duckdb.org/docs/data/insert). The following box types are available: `Note` (default), `Warning`, `Tip`, `Bestpractice`, `Deprecated`.
* Always format SQL code, variable names, function names, etc. as code. For example, when talking about the `CREATE TABLE` statement, the keywords should be formatted as code.
* When presenting SQL statements, do not include the DuckDB prompt (`D `).
* SQL statements should end with a semicolon (`;`) to allow readers to quickly paste them into a SQL console.
* Tables with predominantly code output (e.g., the result of a `DESCRIBE` statement) should be prepended with an empty div that has the `monospace_table` class: `<div class="monospace_table"></div>`.
* Tables where the headers should be center-aligned (opposed to the left-aligned default) should be prepended with an empty div that has the `center_aligned_header_table` class: `<div class="center_aligned_header_table"></div>`.
* Do not introduce hard line breaks if possible. Therefore, avoid using the `<br/>` HTML tag and avoid [double spaces at the end of a line in Markdown](https://spec.commonmark.org/0.28/#hard-line-breaks).
* Single and double quote characters (`'` and `"`) are not converted to smart quotation marks automatically. To insert these, use `“` `”` and `‘` `’`.
* When referencing other articles, put their titles in quotes, e.g., `see the [“Lightweight Compression in DuckDB” blog post]({% post_url 2022-10-28-lightweight-compression %})`.
* For unordered lists, use `*`. If the list has multiple levels, use **4 spaces** for indentation.

> [!TIP]
> In VS Code, you can configure the [Markdown All in One extension](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) to use asterisk as the default marker when generating a table of content for a page using the following setting in `settings.json`:
> `"markdown.extension.toc.unorderedList.marker": "*"`

### Headers

* The title of the page should be encoded in the front matter's `title` property. The body of the page should not start with a repetition of this title.
* In the body of the page, restrict the use of headers to the following levels: h2 (`##`), h3 (`###`), and h4 (`####`).
* Use headline capitalization as defined in the [Chicago Manual of Style](https://capitalizemytitle.com/style/Chicago/).

### SQL Style

* Use **4 spaces** for indentation.
* Use uppercase SQL keywords, e.g., `SELECT 42 AS x, 'hello world' AS y FROM ...;`.
* Use lowercase function names, e.g., `SELECT cos(pi()), date_part('year', DATE '1992-09-20');`.
* Use snake case (lowercase with underscore separators) for table and column names, e.g., `SELECT departure_time FROM train_services;`
* Add spaces around commas and operators, e.g., `SELECT FROM tbl WHERE x > 42;`.
* Add a semicolon to the end of each SQL statement, e.g., `SELECT 42 AS x;`.
* Commas should be placed at the end of each line.
* _Do not_ add clauses or expressions purely for aligning lines. For example, avoid adding `WHERE 1 = 1` and `WHERE true`.
* _Do not_ include the DuckDB prompt. For example, avoid the following: `D SELECT 42;`.
* Employing DuckDB's syntax extensions, e.g., the [`FROM-first` syntax](https://duckdb.org/docs/sql/query_syntax/from) and [`GROUP BY ALL`](https://duckdb.org/docs/sql/query_syntax/groupby#group-by-all), is allowed but use them sparingly when introducing new features.
* Larger returned tables should be formatted using the DuckDB CLI's markdown mode (`.mode markdown`) and NULL values rendered as `NULL` (`.nullvalue NULL`). For smaller tables, using the default `.mode duckbox` is acceptable.
* Output printed on the system console should be formatted as code with the `text` language tag. For example:
   ````
   ```text
   DuckDB v1.3.1 (Ossivalis) 2063dda3e6
   ```
   ````
* Error messages should be formatted as code with the `console` language tag. For example:
   ````
   ```console
   Error: Constraint Error: Duplicate key "i: 1" violates primary key constraint.
   ```
   ````
* To specify placeholders (or template-style code), use the left angle and right angle characters, `⟨` and `⟩`. These will be highlighted in red and typeset in monospace bold italic to draw the reader's attention to them.
     * For example, you could write: To create a table from a Parquet file, run: `CREATE TABLE ⟨your_table_name⟩ AS FROM '⟨your_filename⟩.parquet'`.
     * Copy the characters from here: `⟨⟩`.
     * These characters are known in LaTeX code as `\langle` and `\rangle`.
     * Inline code snippets containing placeholders should be highlighted as SQL code. This can be achieved by appending `{:.language-sql .highlight}` after the code snippet (no space is required before the curly brace).
     * *Avoid* using arithmetic comparison characters, `<` and `>`, brackets, `[` and `]`, braces, `{` and `}`, for this purpose.

### Python Style

* Use **4 spaces** for indentation.
* Use double quotes (`"`) by default for strings.

### Spelling

* Use [American English (en-US) spelling](https://en.wikipedia.org/wiki/Oxford_spelling#Language_tag_comparison).
* Avoid using the Oxford comma.

## Example Code Snippets

* Examples that illustrate the use of features are very welcome. Where applicable, consider starting the page with a few simple examples that demonstrate the most common uses of the feature described.
* If possible, examples should be self-contained and reproducible. For example, the tables used in the example must be created as a part of the example code snippet.

## Cross-References

* Where applicable, add cross-references to relevant other pages in the documentation.
* Use Jekyll's [link tags](https://jekyllrb.com/docs/liquid/tags/#link) to link to pages.
    * For example, to link to the Example section on the `SELECT` statement's page, use `{% link docs/stable/sql/statements/select.md %}#examples`.
    * Link tags ensure that the documentation is only compiled and deployed if links point to existing pages.
    * Note that the paths must contain the correct extension (most often `.md`) and they must be relative to the repository root.
    * :x: ```see [the `SELECT` statement](../../sql/statements/select)```
    * :white_check_mark: ```see [the `SELECT` statement]({% link docs/stable/sql/statements/select.md %})```
* Avoid using the term "here" for links. For the rationale, see a [detailed explanation on why your links should never say "click here"](https://uxmovement.com/content/why-your-links-should-never-say-click-here/).
    * :x: `see [here]({% link docs/stable/sql/statements/copy.md %}#copy-from)`
    * :white_check_mark: ```see the [`COPY ... FROM` statement]({% link docs/stable/sql/statements/copy.md %}#copy-from)```
* Reference a specific section when possible:
    * :x: ```see the [`COPY ... FROM` statement]({% link docs/stable/sql/statements/copy.md %})```
    * :white_check_mark: ```see the [`COPY ... FROM` statement]({% link docs/stable/sql/statements/copy.md %}#copy-from)```
* In most cases, linking related GitHub issues/discussions is discouraged. This allows the documentation to be self-contained.

## Preview, Stable and Versioned Pages

* The `preview` pages under <https://duckdb.org/docs/preview/> contains documentation for the latest preview (nightly) release of DuckDB. Most pull requests should target these pages. Pull requests documenting new features must target these pages.
* The `stable` pages under <https://duckdb.org/docs/stable/> contain documentation for the latest stable release of DuckDB (e.g., v1.2).
* The versioned pages (e.g., <https://duckdb.org/docs/v1.0/>) contain documentation for old stable versions of DuckDB. We generally only accept contributions to the latest stable version. Older pages are only maintained if they contain a critical error.

## Generated Pages

Many of the documentation's pages are auto-generated. Before editing, please check the [`scripts/generate_all_docs.sh`](scripts/generate_all_docs.sh) script. Avoid directly editing the generated content, instead, edit the source files (often found in the [`duckdb` repository](https://github.com/duckdb/duckdb)), and run the generator script.

### Generated SQL Function Lists

The documentation concerning SQL functions in directory `docs/stable/sql/functions/` is partly auto-generated by script [`scripts/generate_sql_function_docs.py`](scripts/generate_sql_function_docs.py).
Specific sections in the function documentation pages will list available functions together with their descriptions and standard examples. These sections will be generated by querying the DuckDB catalog directly. Updating the function descriptions and examples, therefore, should be done in the [`duckdb` repository](https://github.com/duckdb/duckdb) (search for files named `functions.json`).

To insert a function list on a doc page, add the following two lines:

```md
<!-- Start of section generated by scripts/generate_sql_function_docs.py; categories: [text_similarity] -->
<!-- End of section generated by scripts/generate_sql_function_docs.py -->
```

The function list will be added between these lines and will be synced with the DuckDB catalog every time the docs are generated.
Note that the function categories to include in the list need to be set at end of the first line, e.g.: `categories: [string,regex]`.
To check the category labels for the functions in the catalog, the following query can be used:

```sql
SELECT DISTINCT function_name, categories
FROM duckdb_functions()
WHERE categories != [];
```

All data (e.g., parameter names, descriptions, examples) comes from the output of `duckdb_functions()`. Any deviations (exclusion, additions or overrides), need to be hardcoded in the script [`generate_sql_function_docs.py`](scripts/generate_sql_function_docs.py) via variables `OVERRIDES` and `EXCLUDES`.

Example: `blob.md`

```md
---
layout: docu
title: Blob Functions
---

<!-- markdownlint-disable MD001 -->

This section describes functions and operators for examining and manipulating [`BLOB` values]({% link docs/preview/sql/data_types/blob.md %}).

<!-- Start of section generated by scripts/generate_sql_function_docs.py; categories: [blob] -->
<!-- End of section generated by scripts/generate_sql_function_docs.py -->
```

## Notice

We reserve full and final discretion over whether or not we will merge a pull request. Adhering to these guidelines is not a guarantee that your pull request will be merged.
