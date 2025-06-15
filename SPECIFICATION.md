# PseudoScript Utility (PSU) Language Specification

## Introduction
This document defines the syntax and semantics of the PseudoScript Utility (PSU) language, specifically tailored for generating static websites. PSU aims to provide a high-level, semantic, and concise way to describe web pages, abstracting away raw HTML.

## Core Directives

### 1. `psload`
* **Purpose:** The absolute first directive in any PSU script. It signals the start of a valid PSU script and prepares the interpreter environment.
* **Syntax:**
    ```psu
    psload
    ```
* **Rules:** Must be the **very first line**. No preceding characters or comments. Interpreter will exit if not found.

### 2. `psstart`
* **Purpose:** The second directive, marking the beginning of the executable instructions in a PSU script.
* **Syntax:**
    ```psu
    psstart
    ```
* **Rules:** Must be the **very second line**, directly following `psload`. No preceding characters or comments. Interpreter will exit if not found.

## Comments

* **Syntax:**
    ```psu
    .. This is a single-line comment.
    ```
* **Rules:** Comments start with `..` (two dots). All text from `..` to the end of the line is ignored by the interpreter.

## Whitespace and Indentation

* **Rules:** Indentation is crucial for defining nested content blocks (e.g., children within a `section` or `container`). Each level of indentation must use **4 spaces**. Mixing spaces and tabs, or inconsistent indentation, will result in parsing errors.

## Data Types

* **Strings:** Enclosed in double quotes (e.g., `"Hello World!"`).
* **Numbers:** Integer or decimal values (e.g., `10`, `3.14`).
* **Booleans:** `true`, `false` (for conditional logic).

## Variables

* **Declaration/Assignment:** Use the `set` command.
    * **Syntax:** `set variableName = value;`
    * **Example:** `set user_name = "Alice";`
* **Naming:** Variable names must start with a letter and can contain letters, numbers, and underscores.
* **Scope:** All variables are currently global to the script.
* **Embedding:** Variables can be embedded within string literals using `${variableName}` syntax.
    * **Example:** `paragraph "Welcome, ${user_name}!" :`

## Commands for Website Generation

All block-level commands (those containing nested content) end with a colon (`:`).

### 1. `output_html "filename.html"`
* **Purpose:** Specifies the name of the HTML file that the interpreter will generate. This should typically be defined once near the beginning of the script.
* **Syntax:** `output_html "index.html"`
* **Rules:** The argument must be a string literal representing a valid filename.

### 2. `page "Page Title" (stylesheet="path.css", script="path.js", favicon="path.ico") :`
* **Purpose:** Defines the entire HTML document structure. It wraps all subsequent content. Automatically generates `<!DOCTYPE html>`, `<html>`, `<head>`, `<body>` and places the provided title and links within the `<head>`.
* **Syntax:**
    ```psu
    page "My Awesome Site" (stylesheet="css/style.css", script="js/main.js", favicon="images/favicon.ico"):
        .. Content for the <head> section (e.g., meta_info)
        .. Content for the <body> section (e.g., sections, containers)
    ```
* **Parameters:**
    * `"Page Title"` (required): String literal for the `<title>` tag.
    * `stylesheet` (optional): Path to a CSS stylesheet. Generates `<link rel="stylesheet" href="...">`.
    * `script` (optional): Path to a JavaScript file. Generates `<script src="..."></script>`.
    * `favicon` (optional): Path to a favicon. Generates `<link rel="icon" href="...">`.
* **Rules:** Must be the top-level content block after `psstart` (excluding `output_html` and `set` commands).

### 3. `meta_info name="property" content="value"`
* **Purpose:** Inserts HTML `<meta>` tags within the `<head>` section of the page.
* **Syntax:**
    ```psu
    meta_info name="description" content="A great website.":
    meta_info property="og:image" content="[https://example.com/image.jpg](https://example.com/image.jpg)":
    ```
* **Parameters:**
    * `name="value"`: Specifies the `name` attribute of the meta tag.
    * `property="value"`: Specifies the `property` attribute for Open Graph/Twitter Cards.
    * `content="value"`: Specifies the `content` attribute.
* **Rules:** Must be placed within the `page` command's scope.

### 4. `section "Section ID" (attributes) :`
* **Purpose:** Defines a distinct logical or structural section of your page. Translates to an HTML `<section>` tag.
* **Syntax:**
    ```psu
    section "hero-area" (class="bg-blue", data-animation="fade-in"):
        heading level=1 "Welcome!" :
        paragraph "This is the main hero section." :
    ```
* **Parameters:**
    * `"Section ID"` (required): A unique string used as the HTML `id` attribute.
    * `attributes` (optional): Key-value pairs (e.g., `class="my-class"`) that are passed directly as HTML attributes.

### 5. `container (attributes) :`
* **Purpose:** A generic block for grouping content, often used for layout and applying CSS classes. Translates to an HTML `<div>` tag.
* **Syntax:**
    ```psu
    container (class="grid-cols-2 text-center"):
        image "pic.jpg" (alt="Grid Image"):
        paragraph "Some text next to an image." :
    ```
* **Parameters:**
    * `attributes` (optional): Key-value pairs passed directly as HTML attributes.

### 6. `heading level=N "Text Content" (attributes) :`
* **Purpose:** Generates an `<h1>` through `<h6>` tag.
* **Syntax:**
    ```psu
    heading level=1 "Main Page Title" :
    heading level=2 "Subsection Header" (class="subtitle", id="section-title") :
    ```
* **Parameters:**
    * `level=N` (required): An integer from 1 to 6.
    * `"Text Content"` (required): String literal for the heading text. Variable interpolation `"${variable}"` is supported.
    * `attributes` (optional): Key-value pairs passed directly as HTML attributes.

### 7. `paragraph "Text Content" (attributes) :`
* **Purpose:** Generates a `<p>` tag.
* **Syntax:**
    ```psu
    paragraph "This is a standard paragraph with ${variable_name}." :
    ```
* **Parameters:**
    * `"Text Content"` (required): String literal for the paragraph text. Variable interpolation supported.
    * `attributes` (optional): Key-value pairs passed directly as HTML attributes.

### 8. `image "path/to/image.jpg" (attributes) :`
* **Purpose:** Generates an `<img>` tag.
* **Syntax:**
    ```psu
    image "assets/logo.png" (alt="Company Logo", width="150", class="responsive-img") :
    ```
* **Parameters:**
    * `"path/to/image.jpg"` (required): String literal for the `src` attribute.
    * `attributes` (optional): Key-value pairs (e.g., `alt`, `width`, `height`, `class`) passed directly as HTML attributes.

### 9. `button "Button Text" (attributes) :`
* **Purpose:** Generates a `<button>` tag.
* **Syntax:**
    ```psu
    button "Click Me!" (id="myButton", onclick="doSomething()", class="primary-btn") :
    ```
* **Parameters:**
    * `"Button Text"` (required): String literal for the button's display text.
    * `attributes` (optional): Key-value pairs (e.g., `onclick`, `class`, `id`, `type`) passed directly as HTML attributes.

### 10. `link "Display Text" "url/path" (attributes) :`
* **Purpose:** Generates an `<a>` (anchor) tag.
* **Syntax:**
    ```psu
    link "Visit Google" "[https://www.google.com](https://www.google.com)" (target="_blank", class="external") :
    link "Go to About Page" "/about.html" :
    ```
* **Parameters:**
    * `"Display Text"` (required): String literal for the link's display text.
    * `"url/path"` (required): String literal for the `href` attribute.
    * `attributes` (optional): Key-value pairs (e.g., `target`, `class`, `id`) passed directly as HTML attributes.

### 11. `list type="ordered|unordered" (attributes) :`
* **Purpose:** Generates an `<ol>` (ordered) or `<ul>` (unordered) list.
* **Syntax:**
    ```psu
    list type="unordered" (class="menu"):
        item "Home" :
        item "About" :
    ```
* **Parameters:**
    * `type="ordered"` or `type="unordered"` (required): Specifies the list type.
    * `attributes` (optional): Key-value pairs passed directly as HTML attributes.
* **Child Command:** `item "List Item Text" (attributes) :`
    * **Purpose:** Generates an `<li>` tag within a `list`.
    * **Syntax:** `item "My Item" (id="item-1") :`
    * **Parameters:** `"List Item Text"` (required), `attributes` (optional).

### 12. `card title="Card Title" (attributes) :`
* **Purpose:** Generates a common UI card component. This will typically translate into a `<div>` with specific classes and internal structure for title, body, and footer.
* **Syntax:**
    ```psu
    card title="Product One" (class="product-card"):
        card_body:
            image "products/product1.jpg" (alt="Product 1"):
            paragraph "Description of product one." :
        card_footer:
            button "Add to Cart" :
    ```
* **Parameters:**
    * `title="Card Title"` (required): String literal for the card's main heading (e.g., an `<h2>` inside the card).
    * `attributes` (optional): Key-value pairs for the main card container.
* **Child Commands:**
    * **`card_body :`**: Container for the main content of the card.
    * **`card_footer :`**: Container for actions or secondary info at the bottom of the card.

## Control Flow

### 1. `if condition: ... else: ...`
* **Purpose:** Executes a block of PSU code only if a condition is true. An optional `else` block executes if the condition is false.
* **Syntax:**
    ```psu
    if user_is_admin == true:
        paragraph "Welcome, Admin!" :
    else:
        paragraph "Welcome, Guest!" :
    ```
* **Conditions:** Supports equality (`==`), inequality (`!=`), greater than (`>`), less than (`<`), etc., between variables, numbers, and strings.

### 2. `loop count from start to end: ...`
* **Purpose:** Repeats a block of PSU code a specified number of times.
* **Syntax:**
    ```psu
    loop i from 1 to 3:
        paragraph "Item number ${i}" :
    ```
* **Parameters:** `i` (loop variable name), `start` (inclusive start number), `end` (inclusive end number).

---

## Part 2: The `interpreter/main.py` Code

This is the Python code that will read your `.psu` script, understand the commands defined above, and generate the corresponding HTML.

**Important Considerations for the Interpreter:**

* **Complexity:** A full-fledged interpreter for all these features (especially robust attribute parsing, indentation handling, and complex component generation) is a significant project.
* **Simplification:** The code below will provide a *functional starting point* for many of these commands. It will be simplified in terms of error handling and the parsing of complex attribute lists or deeply nested structures.
* **Indentation Management:** This is critical for PSU's block structure. The interpreter will need to track the current indentation level to know when blocks start and end.

---

**File: `interpreter/main.py`**

```python
import sys
import os
import re

# --- GLOBAL STATE FOR INTERPRETER ---
variables = {}  # Stores PSU variables (e.g., set user_name = "Alice";)
html_output_buffer = []  # Accumulates generated HTML lines
output_filename = "index.html"  # Default output filename

# A simple stack to keep track of the current HTML parent for nesting.
# This helps us know where to append generated HTML elements.
# E.g., ['html', 'head', 'body', 'div']
html_tag_stack = []

# --- HELPER FUNCTIONS FOR PARSING ---

def parse_attributes(attr_string):
    """
    Parses a string like 'attr1="value1", attr2="value2"' into a dictionary.
    Assumes attributes are simple key="value" pairs.
    """
    attributes = {}
    if not attr_string:
        return attributes

    # Regex to find key="value" or key=value (for numbers/booleans)
    # It's simplified and assumes no commas *within* values.
    # More robust: re.findall(r'(\w+)=(?:"([^"]*)"|(\S+))', attr_string)
    matches = re.findall(r'(\w+)=(?:"([^"]*)"|(\S+))', attr_string)
    for key, val1, val2 in matches:
        attributes[key] = val1 if val1 else val2 # Prioritize quoted string, otherwise non-quoted
    return attributes

def interpolate_variables(text):
    """Replaces <span class="math-inline">\{variableName\} in a string with actual variable values\."""
def replace\_var\(match\)\:
var\_name \= match\.group\(1\)
return str\(variables\.get\(var\_name, f"UNDEFINED\_VAR\_\{var\_name\}"\)\)
return re\.sub\(r'\\</span>\{(\w+)\}', replace_var, text)

# --- HTML GENERATION HELPERS ---

def get_html_indent(level_offset=0):
    """Calculates HTML indentation based on current tag stack depth."""
    # Assuming each PSU indentation level is 4 spaces, and one HTML indent is 4 spaces
    # This might need adjustment based on how 'page', 'section', 'container' are handled.
    return "    " * (len(html_tag_stack) + level_offset)

def append_html_line(html_content, current_psu_indent_level=0):
    """Appends an HTML line to the buffer with appropriate indentation."""
    # This is a simplification. A real parser would build a tree of HTML elements
    # and then render them at the end to manage indentation perfectly.
    # For now, we'll try to estimate HTML indent based on PSU indent or tag stack.
    html_output_buffer.append(f"{get_html_indent()}{html_content}")


# --- MAIN INTERPRETER LOGIC ---

def execute_psu_script(filepath):
    global output_filename, variables, html_output_buffer, html_tag_stack

    print(f"--- Starting PSU Interpreter for: {filepath} ---")

    # Reset global state for each run
    variables = {}
    html_output_buffer = []
    output_filename = "index.html" # Reset to default
    html_tag_stack = []

    try:
        with open(filepath, 'r', encoding='utf-8') as f:
            lines = f.readlines()
    except FileNotFoundError:
        print(f"Error: PSU script file not found at '{filepath}'")
        sys.exit(1)
    except Exception as e:
        print(f"Error reading PSU script file: {e}")
        sys.exit(1)

    # --- CORE `psload` AND `psstart` CHECK ---
    if not lines or lines[0].strip() != 'psload':
        print(f"Error: .psu script '{filepath}' must start with 'psload' on the first line.")
        sys.exit(1)
    if len(lines) < 2 or lines[1].strip() != 'psstart':
        print(f"Error: .psu script '{filepath}' must have 'psstart' on the second line.")
        sys.exit(1)
    print("`psload` and `psstart` directives validated.")
    # --- END CORE `psload` AND `psstart` CHECK ---

    # --- Line-by-Line Processing with Indentation Tracking ---
    # This uses a simple stack to manage active blocks.
    # Each item in the stack is a tuple: (indent_level, 'command_type', {command_data})
    block_stack = []

    current_line_index = 2 # Start from the third line after psload/psstart
    while current_line_index < len(lines):
        line = lines[current_line_index]
        trimmed_line = line.strip()
        line_indent_spaces = len(line) - len(line.lstrip())

        # Skip comments and empty lines
        if trimmed_line.startswith('..') or not trimmed_line:
            current_line_index += 1
            continue

        # Adjust block stack based on indentation
        while block_stack and line_indent_spaces <= block_stack[-1][0]:
            closed_indent, closed_cmd_type, closed_cmd_data = block_stack.pop()
            # Close corresponding HTML tags
            if closed_cmd_type == 'page':
                if 'body_open' in closed_cmd_data and closed_cmd_data['body_open']:
                    append_html_line("</body>", closed_indent // 4)
                append_html_line("</html>", closed_indent // 4)
            elif closed_cmd_type in ['section', 'container', 'list', 'card']:
                 append_html_line(f"</{closed_cmd_data['html_tag']}>", closed_indent // 4)
            elif closed_cmd_type in ['card_body', 'card_footer']:
                append_html_line(f"</div>", closed_indent // 4)
            # No closing tags needed for single-line commands like heading, paragraph, image, button, link, item, meta_info, set, output_html, loop, if

        # --- Parse and Interpret Command ---
        parts = trimmed_line.split(' ', 1) # Split into command and rest of line
        command = parts[0]
        arg_string = parts[1] if len(parts) > 1 else ""

        try:
            # Command: output_html
            if command == 'output_html':
                # Expects a single quoted string for filename
                match = re.match(r'"([^"]+)"', arg_string.strip())
                if match:
                    output_filename = match.group(1)
                else:
                    raise ValueError("`output_html` expects a quoted filename.")

            # Command: set
            elif command == 'set':
                match = re.match(r'(\w+)\s*=\s*(.+);', arg_string.strip())
                if match:
                    var_name = match.group(1)
                    value_expr = match.group(2).strip()
                    if value_expr.startswith('"') and value_expr.endswith('"'):
                        variables[var_name] = value_expr.strip('"')
                    elif value_expr.lower() in ['true', 'false']:
                        variables[var_name] = (value_expr.lower() == 'true')
                    elif re.match(r'^-?\d+(\.\d+)?$', value_expr): # Number
                        variables[var_name] = float(value_expr) if '.' in value_expr else int(value_expr)
                    elif var_name in variables: # Simple var assignment
                         variables[var_name] = variables[value_expr]
                    else: # Attempt to evaluate as expression (careful with security!)
                        try:
                            # Limited environment to prevent arbitrary code execution
                            safe_globals = {"__builtins__": None, "True": True, "False": False}
                            evaluated_value = eval(value_expr, safe_globals, variables)
                            variables[var_name] = evaluated_value
                        except (NameError, SyntaxError):
                            print(f"Warning: Could not evaluate expression for '{var_name}': '{value_expr}'")
                            variables[var_name] = None # Or raise a hard error
                else:
                    raise ValueError("Invalid `set` command syntax.")

            # Command: page (main document structure)
            elif command == 'page':
                match = re.match(r'"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    page_title = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    page_attrs = parse_attributes(attrs_str)

                    append_html_line("<!DOCTYPE html>")
                    append_html_line("<html>")
                    append_html_line("    <head>")
                    append_html_line(f"        <title>{interpolate_variables(page_title)}</title>")
                    if 'stylesheet' in page_attrs:
                        append_html_line(f'        <link rel="stylesheet" href="{page_attrs["stylesheet"]}">')
                    if 'script' in page_attrs:
                        append_html_line(f'        <script src="{page_attrs["script"]}"></script>')
                    if 'favicon' in page_attrs:
                        append_html_line(f'        <link rel="icon" href="{page_attrs["favicon"]}">')
                    append_html_line("    </head>")
                    append_html_line("    <body>")
                    html_tag_stack.append('body') # Indicate body is open
                    block_stack.append((line_indent_spaces, 'page', {'body_open': True}))
                else:
                    raise ValueError("Invalid `page` command syntax.")

            # Command: meta_info
            elif command == 'meta_info':
                attrs = parse_attributes(arg_string.strip().rstrip(':').strip())
                attr_str = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                append_html_line(f"<meta {attr_str}>")

            # Command: section
            elif command == 'section':
                match = re.match(r'"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    section_id = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    
                    # Special handling for 'full_width'
                    if 'full_width' in attrs and attrs['full_width'].lower() == 'true':
                        # You can decide how to interpret this, e.g., add a specific class
                        if 'class' in attrs:
                            attr_str_html = attr_str_html.replace(f'class="{attrs["class"]}"', f'class="{attrs["class"]} full-width-section"')
                        else:
                             attr_str_html += ' class="full-width-section"'

                    append_html_line(f"<section id=\"{interpolate_variables(section_id)}\" {attr_str_html}>")
                    html_tag_stack.append('section')
                    block_stack.append((line_indent_spaces, 'section', {'html_tag': 'section'}))
                else:
                    raise ValueError("Invalid `section` command syntax.")

            # Command: container
            elif command == 'container':
                attrs = parse_attributes(arg_string.strip().rstrip(':').strip())
                attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                append_html_line(f"<div {attr_str_html}>")
                html_tag_stack.append('div')
                block_stack.append((line_indent_spaces, 'container', {'html_tag': 'div'}))

            # Command: heading
            elif command == 'heading':
                match = re.match(r'level=(\d+)\s*"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    level = int(match.group(1))
                    text_content = match.group(2)
                    attrs_str = match.group(3).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    if not (1 <= level <= 6):
                        raise ValueError("Heading level must be between 1 and 6.")
                    append_html_line(f"<h{level} {attr_str_html}>{interpolate_variables(text_content)}</h{level}>")
                else:
                    raise ValueError("Invalid `heading` command syntax.")

            # Command: paragraph
            elif command == 'paragraph':
                match = re.match(r'"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    text_content = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    append_html_line(f"<p {attr_str_html}>{interpolate_variables(text_content)}</p>")
                else:
                    raise ValueError("Invalid `paragraph` command syntax.")

            # Command: image
            elif command == 'image':
                match = re.match(r'"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    src = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    append_html_line(f'<img src="{interpolate_variables(src)}" {attr_str_html}>')
                else:
                    raise ValueError("Invalid `image` command syntax.")

            # Command: button
            elif command == 'button':
                match = re.match(r'"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    text_content = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    append_html_line(f"<button {attr_str_html}>{interpolate_variables(text_content)}</button>")
                else:
                    raise ValueError("Invalid `button` command syntax.")

            # Command: link
            elif command == 'link':
                match = re.match(r'"([^"]+)"\s*"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    display_text = match.group(1)
                    href = match.group(2)
                    attrs_str = match.group(3).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    append_html_line(f'<a href="{interpolate_variables(href)}" {attr_str_html}>{interpolate_variables(display_text)}</a>')
                else:
                    raise ValueError("Invalid `link` command syntax.")

            # Command: list
            elif command == 'list':
                match = re.match(r'type="(ordered|unordered)"\s*(.*):?', arg_string.strip())
                if match:
                    list_type = match.group(1)
                    html_tag = 'ol' if list_type == 'ordered' else 'ul'
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    append_html_line(f"<{html_tag} {attr_str_html}>")
                    html_tag_stack.append(html_tag)
                    block_stack.append((line_indent_spaces, 'list', {'html_tag': html_tag}))
                else:
                    raise ValueError("Invalid `list` command syntax.")

            # Command: item (must be inside a list)
            elif command == 'item':
                if not block_stack or block_stack[-1][1] != 'list':
                    raise ValueError("`item` command must be inside a `list` block.")
                
                match = re.match(r'"([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    text_content = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    append_html_line(f"<li {attr_str_html}>{interpolate_variables(text_content)}</li>")
                else:
                    raise ValueError("Invalid `item` command syntax.")

            # Command: card
            elif command == 'card':
                match = re.match(r'title="([^"]+)"\s*(.*):?', arg_string.strip())
                if match:
                    card_title_text = match.group(1)
                    attrs_str = match.group(2).strip().rstrip(':').strip()
                    attrs = parse_attributes(attrs_str)
                    attr_str_html = ' '.join([f'{k}="{interpolate_variables(v)}"' for k, v in attrs.items()])
                    
                    append_html_line(f"<div class=\"psu-card {attrs.get('class', '')}\" {attr_str_html.replace('class=\"' + attrs.get('class', '') + '\"', '')}>")
                    append_html_line(f"    <div class=\"psu-card-header\">")
                    append_html_line(f"        <h2>{interpolate_variables(card_title_text)}</h2>")
                    append_html_line(f"    </div>")
                    html_tag_stack.append('div') # For the main card div
                    block_stack.append((line_indent_spaces, 'card', {'html_tag': 'div'}))
                else:
                    raise ValueError("Invalid `card` command syntax.")

            # Command: card_body
            elif command == 'card_body':
                if not block_stack or block_stack[-1][1] != 'card':
                    raise ValueError("`card_body` must be inside a `card` block.")
                append_html_line(f"<div class=\"psu-card-body\">")
                html_tag_stack.append('div') # For card_body div
                block_stack.append((line_indent_spaces, 'card_body', {})) # No html_tag needed for closing here

            # Command: card_footer
            elif command == 'card_footer':
                if not block_stack or block_stack[-1][1] != 'card':
                    raise ValueError("`card_footer` must be inside a `card` block.")
                append_html_line(f"<div class=\"psu-card-footer\">")
                html_tag_stack.append('div') # For card_footer div
                block_stack.append((line_indent_spaces, 'card_footer', {})) # No html_tag needed for closing here

            # Command: if (simplified, only checks boolean variables for now)
            elif command == 'if':
                match = re.match(r'(.+):', arg_string.strip())
                if match:
                    condition_expr = match.group(1).strip()
                    # VERY SIMPLIFIED: Only checks if a variable is true/false or matches a value.
                    # Robust parsing would need a proper expression evaluator.
                    condition_met = False
                    if condition_expr.lower() in ['true', 'false']:
                        condition_met = (condition_expr.lower() == 'true')
                    elif '==' in condition_expr:
                        parts = [p.strip() for p in condition_expr.split('==')]
                        if len(parts) == 2:
                            var_val = variables.get(parts[0])
                            comp_val = parts[1].strip('"').lower() # Assuming string/boolean compare
                            if str(var_val).lower() == comp_val:
                                condition_met = True
                    else: # Assume it's a boolean variable name
                        condition_met = bool(variables.get(condition_expr, False))

                    block_stack.append((line_indent_spaces, 'if', {'condition_met': condition_met, 'executed': False}))
                else:
                    raise ValueError("Invalid `if` command syntax.")

            # Command: else (must follow an if)
            elif command == 'else':
                if not block_stack or block_stack[-1][1] != 'if':
                    raise ValueError("`else` command must immediately follow an `if` block.")
                
                if_data = block_stack[-1][2]
                # If the 'if' block was executed, 'else' should be skipped
                # If 'if' was NOT executed, 'else' should be executed IF condition was false
                if_data['condition_met'] = not if_data['condition_met']
                if_data['executed'] = False # Reset for else block execution

            # Command: loop (simplified to 'from X to Y')
            elif command == 'loop':
                match = re.match(r'(\w+)\s+from\s+(\d+)\s+to\s+(\d+):', arg_string.strip())
                if match:
                    loop_var = match.group(1)
                    start_val = int(match.group(2))
                    end_val = int(match.group(3))
                    
                    # For now, loops are unfolded during parsing.
                    # A more complex interpreter would process loop body multiple times.
                    # For simplicity, we'll just store loop info for now and not execute
                    # it in this basic line-by-line interpreter model directly.
                    # A true AST-based interpreter would be better for loops.
                    
                    # For a simple line-by-line model, loops are hard.
                    # Let's adjust PSU to say loops are processed at compile-time/unfolded.
                    # Or, more practically for this simple interpreter, implement loop
                    # by finding its end and re-processing lines.
                    print(f"Warning: `loop` command is a placeholder. Needs advanced interpreter features.")
                    # For now, just mark it and skip.
                    block_stack.append((line_indent_spaces, 'loop_placeholder', {'loop_var': loop_var, 'start': start_val, 'end': end_val}))
                    
                else:
                    raise ValueError("Invalid `loop` command syntax.")


            # Handle lines within blocks (indentation logic)
            # This is where the execution flow for if/else/loop would actually skip lines
            # For simplicity, this basic interpreter executes line by line.
            # A real interpreter would build an AST and traverse it, making skips easier.

            # If current command started a block, push to stack
            if trimmed_endswith_colon and command not in ['heading', 'paragraph', 'image', 'button', 'link', 'item', 'meta_info']:
                 # If this command expects children, push to block_stack
                 # (This needs more specific logic for each command type)
                 pass # Block management is already done in command handlers for page, section, container, list, card.


        except ValueError as ve:
            print(f"Error on line {current_line_index + 1}: {ve}")
            sys.exit(1)
        except Exception as e:
            print(f"Unhandled error on line {current_line_index + 1}: {e}")
            sys.exit(1)

        current_line_index += 1

    # Close any remaining open HTML tags from the stack (e.g., if page wasn't closed)
    while html_tag_stack:
        tag = html_tag_stack.pop()
        if tag == 'body': # Body is closed by 'page' closing itself
            pass
        elif tag == 'html': # Html is closed by 'page' closing itself
            pass
        else:
            append_html_line(f"</{tag}>", 0) # Use 0 indent, as we're just closing leftover

    # Final HTML wrapping (might be redundant if 'page' command handles it completely)
    # This ensures a basic valid HTML structure even if 'page' command isn't used
    # if 'html' not in html_tag_stack: # Ensure basic HTML structure if not handled by 'page'
    #     final_html_content = (
    #         "<!DOCTYPE html>\n<html>\n<head>\n    <title>PSU Generated Site</title>\n</head>\n<body>\n" +
    #         '\n'.join(html_output_buffer) +
    #         "\n</body>\n</html>"
    #     )
    # else:
    final_html_content = '\n'.join(html_output_buffer)

    # Write the accumulated HTML to the output file
    try:
        with open(output_filename, 'w', encoding='utf-8') as f:
            f.write(final_html_content)
        print(f"--- Successfully generated HTML file: {output_filename} ---")
    except Exception as e:
        print(f"Error writing HTML output file: {e}")
        sys.exit(1)

# --- How to run this interpreter from the command line ---
if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python interpreter/main.py <path_to_psu_script.psu>")
        sys.exit(1)

    psu_file_path = sys.argv[1]
    if not psu_file_path.lower().endswith('.psu'):
        print("Error: Input file must have a .psu extension.")
        sys.exit(1)

    execute_psu_script(psu_file_path)
