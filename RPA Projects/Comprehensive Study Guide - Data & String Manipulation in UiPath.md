# Comprehensive Study Guide: Data & String Manipulation in UiPath

## Table of Contents

1. [Core Concepts & Philosophy](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#core-concepts)
2. [Built-in UiPath Activities](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#builtin-activities)
3. [.NET String Manipulation](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#net-strings)
4. [Regular Expressions (Regex)](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#regex)
5. [LINQ Queries](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#linq)
6. [DataTable Operations](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#datatables)
7. [Practical Use Cases](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#use-cases)
8. [Best Practices & Decision Framework](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#best-practices)
9. [Code Examples & Exercises](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#examples)
10. [Troubleshooting & Common Pitfalls](https://claude.ai/chat/e82cf5d5-38e8-45d8-985c-1a72536bad56#troubleshooting)

---

## <a name="core-concepts"></a>1. Core Concepts & Philosophy

### The Activity vs. Code Paradigm

UiPath offers two fundamental approaches to automation:

**Visual Activities (Low-Code)**

- Drag-and-drop interface components
- Pre-built functionality for common tasks
- Easy to learn for non-programmers
- Self-documenting workflows
- Slower performance for complex operations

**Custom Code (C#/VB.NET)**

- Direct access to .NET Framework capabilities
- Superior performance (100x+ faster for loops)
- Concise solutions for complex logic
- Requires programming knowledge
- Better for data-intensive operations

### Performance Comparison

| Approach | Processing Speed | Use Case |
| --- | --- | --- |
| **For Each Row Activity** | ~1,500 rows/second | Simple iterations, small datasets |
| **VB.NET/C# Loop** | 100,000+ rows/second | Large datasets, performance-critical |

**Key Insight**: Activities can be 60-70x slower than equivalent code for data processing tasks.

### When to Choose Each Approach

**Choose Activities when:**

- Operations are straightforward (basic split, trim, replace)
- Team includes non-developers
- Visual documentation is important
- Data volumes are small (<1,000 rows)
- UI interaction is required (clicking, typing, scraping)

**Choose Code when:**

- Processing large datasets (>10,000 rows)
- Complex logic with multiple conditions
- Regular expressions are needed
- Performance is critical
- Advanced .NET libraries are beneficial
- Multiple activities would be needed (reducing visual clutter)

---

## <a name="builtin-activities"></a>2. Built-in UiPath Activities

### String Activities Reference

| Activity | Purpose | Example Use |
| --- | --- | --- |
| **Assign** | Set variable values | `myString = "Hello"` |
| **Trim** | Remove leading/trailing whitespace | Clean user input |
| **Substring** | Extract portion of string | Get first 10 characters |
| **Replace** | Substitute text | Replace "old" with "new" |
| **Split String** | Break string into array | Split CSV line by comma |
| **Join** | Combine array into string | Merge words with space |
| **Contains** | Check if substring exists | Validate presence of keyword |
| **StartsWith/EndsWith** | Check string boundaries | File extension validation |
| **IsMatch** | Regex pattern validation | Email format check |
| **Matches** | Extract all regex matches | Find all email addresses |
| **Pad Left/Right** | Add padding characters | Format fixed-width data |
| **Compare** | String comparison | Sort or validate order |

### DataTable Activities Reference

| Activity | Purpose | When to Use |
| --- | --- | --- |
| **Generate Data Table** | Create table from text | Parse structured text quickly |
| **Build Data Table** | Manually define structure | Create empty table template |
| **Filter Data Table** | Remove rows by criteria | Simple single-condition filtering |
| **Add Data Column** | Insert new column | Add calculated field |
| **Add Data Row** | Insert new row | Append single record |
| **Remove Data Column** | Delete column | Simplify table structure |
| **Remove Data Row** | Delete specific row | Remove invalid entry |
| **Remove Duplicate Rows** | Deduplicate | Clean duplicate records |
| **Sort Data Table** | Order rows | Organize before processing |
| **Merge Data Table** | Combine tables | Join data from sources |
| **Output Data Table** | Convert to string | Export for logging/display |
| **Write CSV** | Save to file | Persist data |

### Limitations of Activities

1. **Chaining Complexity**: Multiple activities needed for multi-step operations
2. **Performance Overhead**: Activity initialization adds latency
3. **Limited Logic**: No complex conditionals within single activity
4. **Verbosity**: Simple operations require multiple steps
5. **Maintenance**: Large workflows become unwieldy

**Example of Activity Chain Limitation:**

```
To extract email domain from multiple lines:
1. Split String (by newline)
2. For Each (iterate lines)
3. IsMatch (check email pattern)
4. Split String (split by @)
5. Assign (get second part)
6. Append to collection

```

This requires 6+ activities. In code: one regex line.

---

## <a name="net-strings"></a>3. .NET String Manipulation in C#

### Core String Methods

### 1. Substring & IndexOf

**Purpose**: Extract specific portions of text

```csharp
// Basic substring by position
string text = "Invoice #12345 dated 2023-10-15";
string invoiceNum = text.Substring(9, 5); // "12345"

// Find and extract after a marker
string source = "Name: John Doe\nAge: 30";
int startIndex = source.IndexOf("Name: ") + 6; // Position after "Name: "
int endIndex = source.IndexOf("\n", startIndex);
string name = source.Substring(startIndex, endIndex - startIndex); // "John Doe"

// Extract until newline (robust approach)
string nameValue = source.Substring(source.IndexOf("Name: ") + 6)
                         .Split(new[]{'\r','\n'}, StringSplitOptions.RemoveEmptyEntries)[0];

```

**Key Methods:**

- `IndexOf(substring)` - Find position of first occurrence (-1 if not found)
- `LastIndexOf(substring)` - Find position of last occurrence
- `Substring(start, length)` - Extract portion
- `Substring(start)` - Extract from position to end

### 2. Split & Join

**Purpose**: Break strings into parts or combine arrays

```csharp
// Split by single delimiter
string csv = "apple,banana,cherry";
string[] fruits = csv.Split(','); // ["apple", "banana", "cherry"]

// Split by multiple delimiters
string mixed = "apple,banana;cherry:date";
string[] items = mixed.Split(new[]{',',';',':'}, StringSplitOptions.RemoveEmptyEntries);

// Split by string (not char)
string multiWord = "one AND two AND three";
string[] parts = multiWord.Split(new[]{"AND"}, StringSplitOptions.None);

// Join array back to string
string result = String.Join(", ", fruits); // "apple, banana, cherry"

// Join with custom separator
string path = String.Join("\\", new[]{"C:", "Users", "Documents"}); // "C:\Users\Documents"

```

**Options:**

- `StringSplitOptions.None` - Keep empty entries
- `StringSplitOptions.RemoveEmptyEntries` - Discard empty strings

### 3. Replace & Trim

**Purpose**: Clean and normalize text

```csharp
// Basic replace
string text = "Hello World";
string replaced = text.Replace("World", "UiPath"); // "Hello UiPath"

// Chain replacements
string messy = "  too   many    spaces  ";
string clean = messy.Replace("\t", " ")      // Replace tabs
                    .Replace("  ", " ")      // Replace double spaces
                    .Replace("  ", " ")      // Repeat (may need multiple passes)
                    .Trim();                 // Remove leading/trailing

// Trim variants
string whitespace = "  content  ";
string trimmed = whitespace.Trim();           // "content"
string leftTrim = whitespace.TrimStart();     // "content  "
string rightTrim = whitespace.TrimEnd();      // "  content"

// Trim specific characters
string path = "///folder/file///";
string clean = path.Trim('/');                // "folder/file"

```

### 4. Case Conversion & Formatting

**Purpose**: Standardize text format

```csharp
// Case conversion
string mixed = "Hello World";
string upper = mixed.ToUpper();               // "HELLO WORLD"
string lower = mixed.ToLower();               // "hello world"
string title = Thread.CurrentThread.CurrentCulture.TextInfo.ToTitleCase(lower); // "Hello World"

// String interpolation (C# 6+)
string firstName = "John";
string lastName = "Doe";
DateTime date = DateTime.Now;
string formatted = $"{firstName} {lastName}, {date:yyyy-MM-dd}";
// "John Doe, 2025-10-21"

// String.Format (older approach)
string result = String.Format("{0} {1}, {2:yyyy-MM-dd}", firstName, lastName, date);

// Composite formatting
string invoice = $"Invoice #{123:D6} - Total: {499.99:C}";
// "Invoice #000123 - Total: $499.99"

```

**Format Specifiers:**

- `:D6` - Decimal with 6 digits (pad zeros)
- `:C` - Currency (locale-specific)
- `:P` - Percentage
- `:yyyy-MM-dd` - Date format

### 5. Validation & Checking

**Purpose**: Test string conditions

```csharp
// Null/empty checks
string text = "";
bool isEmpty = String.IsNullOrEmpty(text);           // true if null or ""
bool isWhitespace = String.IsNullOrWhiteSpace(text); // true if null, "", or whitespace

// Contains/StartsWith/EndsWith
string filename = "report.xlsx";
bool hasExtension = filename.Contains(".");          // true
bool isExcel = filename.EndsWith(".xlsx");           // true
bool startsWithReport = filename.StartsWith("rep");  // true

// Case-insensitive comparison
bool match = String.Equals(text1, text2, StringComparison.OrdinalIgnoreCase);

// Length check
if (password.Length >= 8) { /* valid */ }

```

### 6. Advanced String Operations

```csharp
// PadLeft/PadRight - fixed-width formatting
string id = "42";
string padded = id.PadLeft(5, '0');          // "00042"
string spaced = id.PadRight(10);             // "42        "

// Remove characters
string text = "Hello World!";
string removed = text.Remove(5, 6);          // "Hello!" (removes 6 chars from position 5)

// Insert text
string inserted = text.Insert(5, " Beautiful"); // "Hello Beautiful World!"

// Character array conversion
char[] chars = text.ToCharArray();
string rebuilt = new String(chars);

// Line operations
string[] lines = multiLineText.Split(new[]{"\r\n", "\r", "\n"}, StringSplitOptions.None);

```

### Using String Methods in UiPath

**Method 1: Assign Activity**

```
Variable: cleanText (String)
Value: rawText.Trim().Replace("\t"," ").ToUpper()

```

**Method 2: Invoke Method**

- Target Object: Leave empty (for static methods)
- Method Name: `Join`, `IsNullOrEmpty`, etc.
- Parameters: Add as needed

**Method 3: Invoke Code**

```csharp
// Input: rawText (String)
// Output: cleanText (String)

cleanText = rawText.Trim()
                   .Replace("\t", " ")
                   .Replace("  ", " ")
                   .ToUpper();

```

---

## <a name="regex"></a>4. Regular Expressions (Regex)

### Understanding Regex Fundamentals

Regular expressions are patterns that match text. They're essential for:

- **Validation** (email, phone, date formats)
- **Extraction** (pulling specific data from text)
- **Replacement** (cleaning or transforming text)
- **Splitting** (breaking text by complex patterns)

### Regex Syntax Basics

| Pattern | Meaning | Example |
| --- | --- | --- |
| `.` | Any single character | `a.c` matches "abc", "a9c" |
| `\d` | Any digit (0-9) | `\d\d\d` matches "123" |
| `\w` | Word character (a-z, A-Z, 0-9, _) | `\w+` matches "Hello_123" |
| `\s` | Whitespace (space, tab, newline) | `\s+` matches "   " |
| `\D` | Non-digit | `\D+` matches "abc" |
| `\W` | Non-word character | `\W` matches "@", " " |
| `\S` | Non-whitespace | `\S+` matches "hello" |
| `^` | Start of string/line | `^Hello` matches "Hello world" |
| `$` | End of string/line | `world$` matches "Hello world" |
| `*` | Zero or more times | `a*` matches "", "a", "aaa" |
| `+` | One or more times | `a+` matches "a", "aaa" (not "") |
| `?` | Zero or one time | `colou?r` matches "color" or "colour" |
| `{n}` | Exactly n times | `\d{4}` matches "2023" |
| `{n,}` | n or more times | `\d{2,}` matches "23", "2023" |
| `{n,m}` | Between n and m times | `\d{2,4}` matches "23", "234", "2345" |
| `[abc]` | Any of a, b, or c | `[aeiou]` matches vowels |
| `[^abc]` | Not a, b, or c | `[^0-9]` matches non-digits |
| `[a-z]` | Range (a through z) | `[A-Z]` matches uppercase |
| `(abc)` | Capture group | `(\d+)` captures numbers |
| `(?:abc)` | Non-capturing group | Groups without saving |
| `|` | OR operator | `cat|dog` matches "cat" or "dog" |
| `\` | Escape special char | `\.` matches literal "." |

### Common Regex Patterns

```csharp
// Email address
@"[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}"

// Phone number (US format)
@"\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}"
// Matches: (555) 123-4567, 555-123-4567, 5551234567

// Date (MM/DD/YYYY)
@"\d{2}/\d{2}/\d{4}"

// Date (YYYY-MM-DD)
@"\d{4}-\d{2}-\d{2}"

// Time (HH:MM or HH:MM:SS)
@"\d{1,2}:\d{2}(:\d{2})?"

// URL
@"https?://[^\s]+"

// IP Address
@"\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b"

// Credit card (basic)
@"\d{4}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}"

// US ZIP code
@"\d{5}(-\d{4})?"

// Currency (with optional cents)
@"\$?\d+(\.\d{2})?"

// Invoice number (example: INV-2023-00123)
@"INV-\d{4}-\d{5}"

```

### Using Regex in UiPath

### Method 1: IsMatch Activity

**Purpose**: Check if pattern exists (returns Boolean)

```
Input: emailText
Pattern: [A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}
Output: isValidEmail (Boolean)

```

### Method 2: Matches Activity

**Purpose**: Extract all occurrences (returns IEnumerable<Match>)

```
Input: documentText
Pattern: \d{4}-\d{2}-\d{2}
Output: dateMatches (IEnumerable<Match>)

// Access in For Each:
For Each match In dateMatches
    dateString = match.ToString()

```

### Method 3: Invoke Code (C#)

```csharp
using System.Text.RegularExpressions;

// Simple match
Match m = Regex.Match(text, @"\d{4}-\d{2}-\d{2}");
if (m.Success) {
    dateFound = m.Value; // e.g., "2023-10-15"
}

// Find all matches
MatchCollection matches = Regex.Matches(text, @"[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}");
foreach (Match match in matches) {
    string email = match.Value;
    // Process each email
}

// Replace operation
string cleaned = Regex.Replace("Hello World 001", @"\d+", "");
// Result: "Hello World " (digits removed)

// Split by pattern
string[] parts = Regex.Split("apple123banana456cherry", @"\d+");
// Result: ["apple", "banana", "cherry"]

```

### Capture Groups

Capture groups extract specific parts of a match:

```csharp
// Pattern with groups: (Name):(Value)
string text = "Name: John Doe, Age: 30, City: New York";
string pattern = @"(\w+):\s*([^,]+)";

MatchCollection matches = Regex.Matches(text, pattern);
foreach (Match match in matches) {
    string key = match.Groups[1].Value;   // "Name", "Age", "City"
    string value = match.Groups[2].Value; // "John Doe", "30", "New York"
}

// Named groups (more readable)
pattern = @"(?<key>\w+):\s*(?<value>[^,]+)";
foreach (Match match in Regex.Matches(text, pattern)) {
    string key = match.Groups["key"].Value;
    string value = match.Groups["value"].Value;
}

```

### Regex Options

```csharp
// Case-insensitive matching
Regex.Match(text, pattern, RegexOptions.IgnoreCase);

// Multi-line mode (^ and $ match line boundaries)
Regex.Match(text, pattern, RegexOptions.Multiline);

// Singleline mode (. matches newlines too)
Regex.Match(text, pattern, RegexOptions.Singleline);

// Combined options
Regex.Match(text, pattern, RegexOptions.IgnoreCase | RegexOptions.Multiline);

```

### Practical Regex Examples

### Example 1: Extract Invoice Details

```csharp
string invoice = @"
Invoice #: INV-2023-00456
Date: 10/15/2023
Amount: $1,234.56
";

// Extract invoice number
string invNum = Regex.Match(invoice, @"INV-\d{4}-\d{5}").Value;
// "INV-2023-00456"

// Extract date
string date = Regex.Match(invoice, @"\d{2}/\d{2}/\d{4}").Value;
// "10/15/2023"

// Extract amount
string amount = Regex.Match(invoice, @"\$[\d,]+\.\d{2}").Value;
// "$1,234.56"

```

### Example 2: Clean Phone Numbers

```csharp
string phones = "(555) 123-4567 or 555.987.6543 or 5551112222";

// Extract all phones
MatchCollection matches = Regex.Matches(phones, @"\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}");

// Normalize to format: 555-123-4567
foreach (Match m in matches) {
    string clean = Regex.Replace(m.Value, @"[^\d]", ""); // Remove non-digits
    string formatted = $"{clean.Substring(0,3)}-{clean.Substring(3,3)}-{clean.Substring(6)}";
    // Results: "555-123-4567", "555-987-6543", "555-111-2222"
}

```

### Example 3: Parse Email Body

```csharp
string emailBody = @"
Order Confirmation
Order #: 123456
Customer: john.doe@email.com
Item: Laptop
Quantity: 2
";

// Extract all data at once with named groups
string pattern = @"Order #:\s*(?<order>\d+).*?Customer:\s*(?<email>\S+).*?Item:\s*(?<item>\w+).*?Quantity:\s*(?<qty>\d+)";

Match m = Regex.Match(emailBody, pattern, RegexOptions.Singleline);
if (m.Success) {
    string orderNum = m.Groups["order"].Value;   // "123456"
    string email = m.Groups["email"].Value;      // "john.doe@email.com"
    string item = m.Groups["item"].Value;        // "Laptop"
    int quantity = int.Parse(m.Groups["qty"].Value); // 2
}

```

---

## <a name="linq"></a>5. LINQ Queries (Language-Integrated Query)

### What is LINQ?

LINQ provides SQL-like querying capabilities for .NET collections. In UiPath, it's invaluable for:

- **Filtering** DataTables without loops
- **Transforming** data (projections)
- **Aggregating** (sum, count, average)
- **Sorting** and **grouping**
- **Joining** multiple data sources

### LINQ with DataTables: The AsEnumerable() Requirement

**Critical**: To use LINQ on DataTables, you MUST call `AsEnumerable()` first.

```csharp
using System.Data;
using System.Linq;

// Import namespace in UiPath:
// System.Data.DataSetExtensions (for AsEnumerable)

// Wrong (won't compile):
var result = dt.Where(r => r["Age"] > 30);

// Correct:
var result = dt.AsEnumerable()
               .Where(r => r.Field<int>("Age") > 30);

```

### Basic LINQ Operations

### 1. Filtering with Where

```csharp
// Filter DataTable rows
var activeCustomers = dt.AsEnumerable()
    .Where(r => r.Field<string>("Status") == "Active");

// Multiple conditions
var filteredRows = dt.AsEnumerable()
    .Where(r => r.Field<int>("Age") > 18 &&
                r.Field<string>("Country") == "USA");

// Complex logic
var result = dt.AsEnumerable()
    .Where(r => {
        int age = r.Field<int>("Age");
        string status = r.Field<string>("Status");
        return (age >= 18 && age <= 65) || status == "VIP";
    });

// Convert back to DataTable
DataTable newTable = result.Any() ? result.CopyToDataTable() : dt.Clone();

```

**Important**: Always check `.Any()` before `CopyToDataTable()` to avoid exceptions on empty results. Use `dt.Clone()` to create empty table with same schema.

### 2. Selecting/Projecting Data

```csharp
// Select specific column values
var names = dt.AsEnumerable()
              .Select(r => r.Field<string>("Name"))
              .ToList(); // Returns List<string>

// Create anonymous objects (useful for transformations)
var summary = dt.AsEnumerable()
    .Select(r => new {
        FullName = r.Field<string>("FirstName") + " " + r.Field<string>("LastName"),
        Age = r.Field<int>("Age"),
        IsAdult = r.Field<int>("Age") >= 18
    })
    .ToList();

// Access: summary[0].FullName, summary[0].IsAdult

// Select into new DataTable structure
var newDt = new DataTable();
newDt.Columns.Add("DisplayName", typeof(string));
newDt.Columns.Add("Category", typeof(string));

foreach (var item in dt.AsEnumerable()) {
    newDt.Rows.Add(
        item.Field<string>("FirstName") + " " + item.Field<string>("LastName"),
        item.Field<int>("Age") >= 18 ? "Adult" : "Minor"
    );
}

```

### 3. Distinct Values

```csharp
// Get unique values from column
var uniqueCountries = dt.AsEnumerable()
    .Select(r => r.Field<string>("Country"))
    .Distinct()
    .ToList();

// Distinct rows based on specific columns (requires custom comparer or workaround)
var distinctRows = dt.AsEnumerable()
    .GroupBy(r => new {
        Name = r.Field<string>("Name"),
        Email = r.Field<string>("Email")
    })
    .Select(g => g.First())
    .CopyToDataTable();

```

### 4. Sorting (OrderBy)

```csharp
// Sort ascending
var sorted = dt.AsEnumerable()
               .OrderBy(r => r.Field<string>("LastName"))
               .CopyToDataTable();

// Sort descending
var sortedDesc = dt.AsEnumerable()
                   .OrderByDescending(r => r.Field<int>("Age"))
                   .CopyToDataTable();

// Multi-level sort
var multiSort = dt.AsEnumerable()
    .OrderBy(r => r.Field<string>("Department"))
    .ThenByDescending(r => r.Field<decimal>("Salary"))
    .CopyToDataTable();

```

### 5. Aggregations

```csharp
// Count rows
int totalRows = dt.AsEnumerable().Count();
int activeCount = dt.AsEnumerable()
                    .Count(r => r.Field<string>("Status") == "Active");

// Sum
decimal totalSalary = dt.AsEnumerable()
                        .Sum(r => r.Field<decimal>("Salary"));

// Average
double avgAge = dt.AsEnumerable()
                  .Average(r => r.Field<int>("Age"));

// Min/Max
int minAge = dt.AsEnumerable()
               .Min(r => r.Field<int>("Age"));

string highestPaid = dt.AsEnumerable()
    .OrderByDescending(r => r.Field<decimal>("Salary"))
    .First()
    .Field<string>("Name");

// Any/All
bool hasMinors = dt.AsEnumerable()
                   .Any(r => r.Field<int>("Age") < 18);

bool allActive = dt.AsEnumerable()
                   .All(r => r.Field<string>("Status") == "Active");

```

### 6. Grouping

```csharp
// Group by single column
var grouped = dt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Department"));

foreach (var group in grouped) {
    string dept = group.Key;
    int count = group.Count();
    decimal avgSalary = group.Average(r => r.Field<decimal>("Salary"));

    Console.WriteLine($"{dept}: {count} employees, Avg Salary: {avgSalary:C}");
}

// Group and create summary DataTable
var summaryDt = new DataTable();
summaryDt.Columns.Add("Department", typeof(string));
summaryDt.Columns.Add("EmployeeCount", typeof(int));
summaryDt.Columns.Add("TotalSalary", typeof(decimal));

var groupedData = dt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Department"))
    .Select(g => new {
        Dept = g.Key,
        Count = g.Count(),
        Total = g.Sum(r => r.Field<decimal>("Salary"))
    });

foreach (var item in groupedData) {
    summaryDt.Rows.Add(item.Dept, item.Count, item.Total);
}

```

### 7. Joining DataTables

```csharp
// Inner join
var joined = from emp in dtEmployees.AsEnumerable()
             join dept in dtDepartments.AsEnumerable()
             on emp.Field<int>("DeptID") equals dept.Field<int>("ID")
             select new {
                 EmployeeName = emp.Field<string>("Name"),
                 DepartmentName = dept.Field<string>("DeptName"),
                 Salary = emp.Field<decimal>("Salary")
             };

// Left join (with DefaultIfEmpty)
var leftJoin = from emp in dtEmployees.AsEnumerable()
               join dept in dtDepartments.AsEnumerable()
               on emp.Field<int>("DeptID") equals dept.Field<int>("ID")
               into deptGroup
               from dept in deptGroup.DefaultIfEmpty()
               select new {
                   EmployeeName = emp.Field<string>("Name"),
                   DepartmentName = dept?.Field<string>("DeptName") ?? "N/A"
               };

```

### LINQ Query Syntax vs. Method Syntax

LINQ supports two styles:

**Query Syntax** (SQL-like):

```csharp
var result = from r in dt.AsEnumerable()
             where r.Field<int>("Age") > 30
             orderby r.Field<string>("Name")
             select r.Field<string>("Name");

```

**Method Syntax** (Fluent):

```csharp
var result = dt.AsEnumerable()
               .Where(r => r.Field<int>("Age") > 30)
               .OrderBy(r => r.Field<string>("Name"))
               .Select(r => r.Field<string>("Name"));

```

Both are equivalent. Method syntax is more common in UiPath code.

### Practical LINQ Examples

### Example 1: Complex Filtering

```csharp
// Find all high-value customers with recent activity
var targetCustomers = dt.AsEnumerable()
    .Where(r => {
        decimal totalSpent = r.Field<decimal>("TotalSpent");
        DateTime lastPurchase = r.Field<DateTime>("LastPurchaseDate");
        string status = r.Field<string>("Status");

        return totalSpent > 1000 &&
               lastPurchase > DateTime.Now.AddMonths(-6) &&
               status == "Active";
    })
    .OrderByDescending(r => r.Field<decimal>("TotalSpent"))
    .CopyToDataTable();

```

### Example 2: Data Transformation

```csharp
// Transform order data into summary format
var orderSummary = dt.AsEnumerable()
    .GroupBy(r => new {
        Customer = r.Field<string>("CustomerName"),
        Month = r.Field<DateTime>("OrderDate").ToString("yyyy-MM")
    })
    .Select(g => new {
        Customer = g.Key.Customer,
        Month = g.Key.Month,
        OrderCount = g.Count(),
        TotalAmount = g.Sum(r => r.Field<decimal>("Amount")),
        AvgAmount = g.Average(r => r.Field<decimal>("Amount"))
    })
    .OrderByDescending(x => x.TotalAmount)
    .ToList();

// Convert to DataTable
var summaryDt = new DataTable();
summaryDt.Columns.Add("Customer", typeof(string));
summaryDt.Columns.Add("Month", typeof(string));
summaryDt.Columns.Add("OrderCount", typeof(int));
summaryDt.Columns.Add("TotalAmount", typeof(decimal));
summaryDt.Columns.Add("AvgAmount", typeof(decimal));

foreach (var item in orderSummary) {
    summaryDt.Rows.Add(item.Customer, item.Month, item.OrderCount,
                       item.TotalAmount, item.AvgAmount);
}
```

### Example 3: Deduplication with Priority

```csharp
// Remove duplicates, keeping the row with highest priority
var deduplicated = dt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Email")) // Group by email
    .Select(g => g.OrderByDescending(r => r.Field<int>("Priority"))
                  .ThenBy(r => r.Field<DateTime>("Date"))
                  .First()) // Take highest priority, earliest date
    .CopyToDataTable();

```

### Example 4: Conditional Aggregation

```csharp
// Calculate metrics by region
var regionalStats = dt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Region"))
    .Select(g => new {
        Region = g.Key,
        TotalSales = g.Sum(r => r.Field<decimal>("Amount")),
        HighValueOrders = g.Count(r => r.Field<decimal>("Amount") > 1000),
        AvgOrderSize = g.Average(r => r.Field<decimal>("Amount")),
        TopCustomer = g.OrderByDescending(r => r.Field<decimal>("Amount"))
                       .First()
                       .Field<string>("CustomerName")
    })
    .ToList();

```

### VB.NET LINQ Syntax

UiPath also supports VB.NET for LINQ:

```vbnet
' Filtering
Dim result = (From row In dt.AsEnumerable()
              Where row.Field(Of Integer)("Age") > 30
              Select row).CopyToDataTable()

' Grouping
Dim grouped = From row In dt.AsEnumerable()
              Group row By dept = row.Field(Of String)("Department")
              Into Count(), Average(row.Field(Of Decimal)("Salary"))

' Complex query
Dim summary = (From row In dt.AsEnumerable()
               Where row.Field(Of String)("Status") = "Active"
               Order By row.Field(Of String)("Name")
               Select New With {
                   .Name = row.Field(Of String)("Name"),
                   .Age = row.Field(Of Integer)("Age")
               }).ToList()

```

---

## <a name="datatables"></a>6. DataTable Operations in Code

### Creating DataTables Programmatically

```csharp
// Method 1: Define structure manually
DataTable dt = new DataTable("Employees");
dt.Columns.Add("ID", typeof(int));
dt.Columns.Add("Name", typeof(string));
dt.Columns.Add("Salary", typeof(decimal));
dt.Columns.Add("HireDate", typeof(DateTime));

// Set primary key
dt.PrimaryKey = new DataColumn[] { dt.Columns["ID"] };

// Method 2: Add columns with properties
DataColumn col = new DataColumn();
col.ColumnName = "Email";
col.DataType = typeof(string);
col.Unique = true;
col.AllowDBNull = false;
dt.Columns.Add(col);

// Method 3: Clone existing structure
DataTable emptyTable = sourceTable.Clone(); // Structure only
DataTable fullCopy = sourceTable.Copy();    // Structure + data

```

### Adding and Modifying Rows

```csharp
// Method 1: Add row with array
dt.Rows.Add(1, "John Doe", 50000, DateTime.Now);

// Method 2: Create row object first
DataRow newRow = dt.NewRow();
newRow["ID"] = 2;
newRow["Name"] = "Jane Smith";
newRow["Salary"] = 60000;
newRow["HireDate"] = new DateTime(2023, 1, 15);
dt.Rows.Add(newRow);

// Method 3: Import row from another table
DataRow rowToImport = otherTable.Rows[0];
dt.ImportRow(rowToImport); // Preserves original values

// Modify existing row
DataRow row = dt.Rows[0];
row["Salary"] = 55000;
row.AcceptChanges(); // Commit changes

// Delete row
dt.Rows[0].Delete();        // Mark for deletion
dt.Rows.RemoveAt(0);        // Immediate removal
dt.Rows.Remove(specificRow); // Remove specific row object

```

### Accessing and Iterating Data

```csharp
// Access by index
string name = dt.Rows[0]["Name"].ToString();
int id = Convert.ToInt32(dt.Rows[0]["ID"]);

// Access with type safety
string name = dt.Rows[0].Field<string>("Name");
int id = dt.Rows[0].Field<int>("ID");

// Check for null
if (!dt.Rows[0].IsNull("OptionalField")) {
    string value = dt.Rows[0]["OptionalField"].ToString();
}

// Iterate rows (traditional)
foreach (DataRow row in dt.Rows) {
    string name = row["Name"].ToString();
    decimal salary = Convert.ToDecimal(row["Salary"]);
}

// Iterate with LINQ (safer, allows filtering)
foreach (DataRow row in dt.AsEnumerable()) {
    string name = row.Field<string>("Name");
    decimal salary = row.Field<decimal>("Salary");
}

// Iterate columns
foreach (DataColumn col in dt.Columns) {
    Console.WriteLine($"{col.ColumnName} ({col.DataType})");
}

// Iterate with index
for (int i = 0; i < dt.Rows.Count; i++) {
    string value = dt.Rows[i]["Name"].ToString();
}

```

### DataTable.Select() Method

An alternative to LINQ for simple filtering:

```csharp
// Basic filter
DataRow[] results = dt.Select("Age > 30");

// Complex filter with operators
DataRow[] filtered = dt.Select("Age > 30 AND Status = 'Active'");

// With wildcards
DataRow[] nameSearch = dt.Select("Name LIKE 'John%'");

// With sorting
DataRow[] sorted = dt.Select("", "Salary DESC, Name ASC");

// Combined
DataRow[] result = dt.Select("Department = 'Sales' AND Salary > 50000", "Salary DESC");

// Create DataTable from results
DataTable newDt = dt.Clone();
foreach (DataRow row in results) {
    newDt.ImportRow(row);
}

```

**Limitations of Select():**

- Less flexible than LINQ
- String-based (no compile-time checking)
- Limited operators (no complex logic)
- Harder to debug

### DataTable.Compute() Method

Calculate aggregates directly:

```csharp
// Sum
object totalSalary = dt.Compute("SUM(Salary)", "");
decimal total = Convert.ToDecimal(totalSalary);

// Average
object avgAge = dt.Compute("AVG(Age)", "");

// Count (use expression)
object count = dt.Compute("COUNT(ID)", "");

// With filter
object deptTotal = dt.Compute("SUM(Salary)", "Department = 'Sales'");

// Min/Max
object maxSalary = dt.Compute("MAX(Salary)", "");
object minAge = dt.Compute("MIN(Age)", "Status = 'Active'");

```

### Merging DataTables

```csharp
// Simple merge (append rows)
dt1.Merge(dt2);

// Merge with options
dt1.Merge(dt2,
    preserveChanges: false,  // Overwrite changes
    missingSchemaAction: MissingSchemaAction.Add); // Add new columns

// Union (combine and deduplicate)
var union = dt1.AsEnumerable()
    .Union(dt2.AsEnumerable(), DataRowComparer.Default)
    .CopyToDataTable();

// Merge with conflict resolution
dt1.Merge(dt2);
if (dt1.HasErrors) {
    foreach (DataRow row in dt1.GetErrors()) {
        Console.WriteLine($"Error: {row.RowError}");
        row.ClearErrors();
    }
}

```

### Filtering and Cleaning Operations

### Remove Empty Rows

```csharp
// Method 1: Using LINQ
var nonEmptyRows = dt.AsEnumerable()
    .Where(r => !r.ItemArray.All(field =>
        field == null || string.IsNullOrWhiteSpace(field.ToString())))
    .CopyToDataTable();

// Method 2: Traditional loop (modify original)
for (int i = dt.Rows.Count - 1; i >= 0; i--) {
    DataRow row = dt.Rows[i];
    bool isEmpty = true;

    foreach (var item in row.ItemArray) {
        if (item != null && !string.IsNullOrWhiteSpace(item.ToString())) {
            isEmpty = false;
            break;
        }
    }

    if (isEmpty) {
        dt.Rows.RemoveAt(i);
    }
}

```

### Trim All Cell Values

```csharp
foreach (DataRow row in dt.Rows) {
    foreach (DataColumn col in dt.Columns) {
        if (col.DataType == typeof(string) && row[col] != DBNull.Value) {
            row[col] = row[col].ToString().Trim();
        }
    }
}

```

### Remove Duplicate Rows

```csharp
// Based on all columns
var distinctRows = dt.AsEnumerable()
    .Distinct(DataRowComparer.Default)
    .CopyToDataTable();

// Based on specific columns
var deduped = dt.AsEnumerable()
    .GroupBy(r => new {
        Email = r.Field<string>("Email"),
        Phone = r.Field<string>("Phone")
    })
    .Select(g => g.First())
    .CopyToDataTable();

// Using UiPath activity (alternative)
// Use "Remove Duplicate Rows" activity - simpler for basic cases

```

### Replace Null/Empty Values

```csharp
foreach (DataRow row in dt.Rows) {
    foreach (DataColumn col in dt.Columns) {
        if (row.IsNull(col) ||
            (col.DataType == typeof(string) && string.IsNullOrEmpty(row[col].ToString()))) {
            // Set default value based on type
            if (col.DataType == typeof(string))
                row[col] = "N/A";
            else if (col.DataType == typeof(int))
                row[col] = 0;
            else if (col.DataType == typeof(decimal))
                row[col] = 0.0m;
            else if (col.DataType == typeof(DateTime))
                row[col] = DateTime.MinValue;
        }
    }
}

```

### DataTable to/from Other Formats

### DataTable to CSV String

```csharp
using System.Text;

StringBuilder csv = new StringBuilder();

// Add headers
var headers = dt.Columns.Cast<DataColumn>()
                .Select(c => c.ColumnName);
csv.AppendLine(string.Join(",", headers));

// Add data rows
foreach (DataRow row in dt.Rows) {
    var fields = row.ItemArray
                    .Select(field => field.ToString().Replace(",", ";")) // Escape commas
                    .Select(field => $"\"{field}\""); // Quote fields
    csv.AppendLine(string.Join(",", fields));
}

string csvContent = csv.ToString();

```

### CSV String to DataTable

```csharp
DataTable dt = new DataTable();
string[] lines = csvContent.Split(new[] { "\r\n", "\n" }, StringSplitOptions.RemoveEmptyEntries);

// Parse header
string[] headers = lines[0].Split(',');
foreach (string header in headers) {
    dt.Columns.Add(header.Trim('"'));
}

// Parse data
for (int i = 1; i < lines.Length; i++) {
    string[] fields = lines[i].Split(',');
    DataRow row = dt.NewRow();
    for (int j = 0; j < fields.Length && j < dt.Columns.Count; j++) {
        row[j] = fields[j].Trim('"');
    }
    dt.Rows.Add(row);
}

```

### DataTable to JSON

```csharp
using Newtonsoft.Json;

// Simple conversion
string json = JsonConvert.SerializeObject(dt, Formatting.Indented);

// With custom formatting
var jsonList = dt.AsEnumerable()
    .Select(r => new {
        id = r.Field<int>("ID"),
        name = r.Field<string>("Name"),
        salary = r.Field<decimal>("Salary")
    });
string json = JsonConvert.SerializeObject(jsonList, Formatting.Indented);

```

### DataTable to Excel (via Interop)

```csharp
// In UiPath, prefer "Write Range" activity
// For code approach:
using Excel = Microsoft.Office.Interop.Excel;

Excel.Application excelApp = new Excel.Application();
Excel.Workbook workbook = excelApp.Workbooks.Add();
Excel.Worksheet worksheet = workbook.Sheets[1];

// Write headers
for (int i = 0; i < dt.Columns.Count; i++) {
    worksheet.Cells[1, i + 1] = dt.Columns[i].ColumnName;
}

// Write data
for (int i = 0; i < dt.Rows.Count; i++) {
    for (int j = 0; j < dt.Columns.Count; j++) {
        worksheet.Cells[i + 2, j + 1] = dt.Rows[i][j].ToString();
    }
}

workbook.SaveAs("output.xlsx");
workbook.Close();
excelApp.Quit();

```

### DataTable Performance Tips

1. **Use appropriate data types**: Don't make everything a string
2. **Set PrimaryKey**: Improves lookup performance
3. **Use Indexes**: For frequent searches
4. **Batch operations**: Don't commit after each row change
5. **Clone vs Copy**: Clone is faster when you only need structure
6. **LINQ vs loops**: LINQ is cleaner but loops can be faster for huge datasets
7. **AsEnumerable()**: Only call once, not in nested loops

```csharp
// Bad: Calling AsEnumerable repeatedly
foreach (var row1 in dt.AsEnumerable()) {
    foreach (var row2 in dt.AsEnumerable()) { // Expensive!
        // ...
    }
}

// Good: Cache the enumerable
var rows = dt.AsEnumerable().ToList();
foreach (var row1 in rows) {
    foreach (var row2 in rows) {
        // ...
    }
}

```

---

## <a name="use-cases"></a>7. Practical Use Cases

### Use Case 1: Email Body Parsing

**Scenario**: Extract structured data from email bodies

```csharp
// Input: emailBody (String from Get Outlook Mail Messages)
string emailBody = @"
Order Confirmation
==================
Order Number: ORD-2023-12345
Customer: John Doe
Email: john.doe@company.com
Phone: (555) 123-4567
Items:
- Laptop x1 - $999.99
- Mouse x2 - $29.99
Total: $1,059.97
Date: 10/15/2023
";

// Extract using regex patterns
string orderNum = Regex.Match(emailBody, @"Order Number:\s*([A-Z0-9-]+)").Groups[1].Value;
string customer = Regex.Match(emailBody, @"Customer:\s*(.+)").Groups[1].Value.Trim();
string email = Regex.Match(emailBody, @"Email:\s*([^\s]+)").Groups[1].Value;
string phone = Regex.Match(emailBody, @"Phone:\s*(.+)").Groups[1].Value.Trim();
string total = Regex.Match(emailBody, @"Total:\s*\$?([\d,]+\.\d{2})").Groups[1].Value;
string date = Regex.Match(emailBody, @"Date:\s*(\d{2}/\d{2}/\d{4})").Groups[1].Value;

// Extract line items
var itemPattern = @"-\s*(.+?)\s+x(\d+)\s+-\s+\$?([\d,]+\.\d{2})";
var itemMatches = Regex.Matches(emailBody, itemPattern);

DataTable itemsDt = new DataTable();
itemsDt.Columns.Add("Product", typeof(string));
itemsDt.Columns.Add("Quantity", typeof(int));
itemsDt.Columns.Add("Price", typeof(decimal));

foreach (Match match in itemMatches) {
    string product = match.Groups[1].Value.Trim();
    int quantity = int.Parse(match.Groups[2].Value);
    decimal price = decimal.Parse(match.Groups[3].Value);
    itemsDt.Rows.Add(product, quantity, price);
}

// Create summary object or DataTable
DataTable orderDt = new DataTable();
orderDt.Columns.Add("OrderNumber", typeof(string));
orderDt.Columns.Add("CustomerName", typeof(string));
orderDt.Columns.Add("Email", typeof(string));
orderDt.Columns.Add("Phone", typeof(string));
orderDt.Columns.Add("Total", typeof(decimal));
orderDt.Columns.Add("OrderDate", typeof(DateTime));

orderDt.Rows.Add(
    orderNum,
    customer,
    email,
    phone,
    decimal.Parse(total),
    DateTime.ParseExact(date, "MM/dd/yyyy", null)
);

```

**Alternative: Line-by-Line Parsing**

```csharp
// For semi-structured emails
string[] lines = emailBody.Split(new[] { '\r', '\n' }, StringSplitOptions.RemoveEmptyEntries);

Dictionary<string, string> data = new Dictionary<string, string>();

foreach (string line in lines) {
    if (line.Contains(":")) {
        string[] parts = line.Split(new[] { ':' }, 2);
        string key = parts[0].Trim();
        string value = parts[1].Trim();
        data[key] = value;
    }
}

// Access parsed data
string orderNum = data.ContainsKey("Order Number") ? data["Order Number"] : "";
string customer = data.ContainsKey("Customer") ? data["Customer"] : "";

```

### Use Case 2: PDF Text Extraction and Processing

**Scenario**: Extract tables from scraped PDF text

```csharp
// Input: pdfText (from Read PDF Text or Screen Scraping)
string pdfText = @"
Invoice #12345                    Date: 10/15/2023

Item                Qty    Price      Total
--------------------------------------------
Widget A            10     $5.00      $50.00
Widget B            5      $12.50     $62.50
Widget C            2      $100.00    $200.00
--------------------------------------------
                              Total: $312.50
";

// Split into lines
string[] lines = pdfText.Split(new[] { '\r', '\n' }, StringSplitOptions.RemoveEmptyEntries)
                        .Select(l => l.Trim())
                        .Where(l => !string.IsNullOrEmpty(l))
                        .ToArray();

// Extract invoice metadata
string invoiceNum = Regex.Match(lines[0], @"#(\d+)").Groups[1].Value;
string date = Regex.Match(lines[0], @"Date:\s*(\d{2}/\d{2}/\d{4})").Groups[1].Value;

// Find table data (skip header and separator lines)
DataTable itemsDt = new DataTable();
itemsDt.Columns.Add("Item", typeof(string));
itemsDt.Columns.Add("Quantity", typeof(int));
itemsDt.Columns.Add("Price", typeof(decimal));
itemsDt.Columns.Add("Total", typeof(decimal));

bool inTable = false;
foreach (string line in lines) {
    // Skip headers and separators
    if (line.StartsWith("Item") || line.StartsWith("---") || line.Contains("Total:")) {
        if (line.StartsWith("Item")) inTable = true;
        continue;
    }

    if (!inTable) continue;

    // Parse table row using regex for currency values
    var match = Regex.Match(line, @"^(.+?)\s+(\d+)\s+\$?([\d.]+)\s+\$?([\d.]+)$");
    if (match.Success) {
        string item = match.Groups[1].Value.Trim();
        int qty = int.Parse(match.Groups[2].Value);
        decimal price = decimal.Parse(match.Groups[3].Value);
        decimal total = decimal.Parse(match.Groups[4].Value);

        itemsDt.Rows.Add(item, qty, price, total);
    }
}

// Verify total
decimal calculatedTotal = itemsDt.AsEnumerable()
    .Sum(r => r.Field<decimal>("Total"));

```

**Fixed-Width Column Parsing**:

```csharp
// For PDFs with fixed-width columns
string line = "Widget A            10     $5.00      $50.00";

// Define column positions
string item = line.Substring(0, 20).Trim();      // Chars 0-19
string qty = line.Substring(20, 7).Trim();       // Chars 20-26
string price = line.Substring(27, 11).Trim();    // Chars 27-37
string total = line.Substring(38).Trim();        // Chars 38-end

// Remove currency symbols and parse
qty = Regex.Replace(qty, @"[^\d]", "");
price = Regex.Replace(price, @"[^\d.]", "");
total = Regex.Replace(total, @"[^\d.]", "");

```

### Use Case 3: Data Cleaning and Normalization

**Scenario**: Clean messy customer data from multiple sources

```csharp
// Input: rawDt (DataTable with inconsistent data)
DataTable cleanDt = rawDt.Clone(); // Same structure

foreach (DataRow row in rawDt.Rows) {
    DataRow cleanRow = cleanDt.NewRow();

    // Clean name: trim, title case, remove extra spaces
    string rawName = row["Name"].ToString();
    string cleanName = Regex.Replace(rawName.Trim(), @"\s+", " ");
    cleanName = Thread.CurrentThread.CurrentCulture.TextInfo.ToTitleCase(cleanName.ToLower());
    cleanRow["Name"] = cleanName;

    // Clean email: lowercase, validate format
    string rawEmail = row["Email"].ToString().Trim().ToLower();
    bool isValidEmail = Regex.IsMatch(rawEmail, @"^[^@\s]+@[^@\s]+\.[^@\s]+$");
    cleanRow["Email"] = isValidEmail ? rawEmail : "";

    // Clean phone: extract digits only, format
    string rawPhone = row["Phone"].ToString();
    string digitsOnly = Regex.Replace(rawPhone, @"[^\d]", "");
    if (digitsOnly.Length == 10) {
        cleanRow["Phone"] = $"({digitsOnly.Substring(0, 3)}) {digitsOnly.Substring(3, 3)}-{digitsOnly.Substring(6)}";
    } else {
        cleanRow["Phone"] = rawPhone; // Keep original if can't parse
    }

    // Clean date: parse multiple formats
    string rawDate = row["Date"].ToString();
    DateTime parsedDate;
    string[] dateFormats = { "MM/dd/yyyy", "yyyy-MM-dd", "dd-MMM-yyyy" };
    if (DateTime.TryParseExact(rawDate, dateFormats, null, System.Globalization.DateTimeStyles.None, out parsedDate)) {
        cleanRow["Date"] = parsedDate.ToString("yyyy-MM-dd");
    } else {
        cleanRow["Date"] = DBNull.Value;
    }

    // Clean currency: extract numeric value
    string rawAmount = row["Amount"].ToString();
    string numericAmount = Regex.Match(rawAmount, @"[\d,]+\.?\d*").Value.Replace(",", "");
    decimal amount;
    cleanRow["Amount"] = decimal.TryParse(numericAmount, out amount) ? amount : 0;

    cleanDt.Rows.Add(cleanRow);
}

// Remove rows with invalid data
var validRows = cleanDt.AsEnumerable()
    .Where(r => !string.IsNullOrWhiteSpace(r.Field<string>("Name")) &&
                !string.IsNullOrWhiteSpace(r.Field<string>("Email")) &&
                !r.IsNull("Date"))
    .CopyToDataTable();

```

### Use Case 4: Multi-Source Data Consolidation

**Scenario**: Merge customer data from multiple Excel files/sources

```csharp
// Inputs: dt1, dt2, dt3 (DataTables from different sources)

// Step 1: Standardize column names
DataTable standardDt = new DataTable();
standardDt.Columns.Add("CustomerID", typeof(string));
standardDt.Columns.Add("Name", typeof(string));
standardDt.Columns.Add("Email", typeof(string));
standardDt.Columns.Add("Phone", typeof(string));
standardDt.Columns.Add("Source", typeof(string));

// Map dt1 columns
foreach (DataRow row in dt1.Rows) {
    standardDt.Rows.Add(
        row["ID"].ToString(),
        row["CustomerName"].ToString(),
        row["EmailAddress"].ToString(),
        row["PhoneNumber"].ToString(),
        "Source1"
    );
}

// Map dt2 columns (different structure)
foreach (DataRow row in dt2.Rows) {
    standardDt.Rows.Add(
        row["CustID"].ToString(),
        $"{row["FirstName"]} {row["LastName"]}",
        row["Email"].ToString(),
        row["Contact"].ToString(),
        "Source2"
    );
}

// Step 2: Deduplicate by email (keep most recent)
var deduplicated = standardDt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Email").ToLower())
    .Select(g => g.OrderBy(r => r.Field<string>("Source")).First())
    .CopyToDataTable();

// Step 3: Clean and validate
foreach (DataRow row in deduplicated.Rows) {
    // Implement cleaning logic from Use Case 3
}

// Step 4: Enrich with additional data (e.g., add status column)
deduplicated.Columns.Add("Status", typeof(string));
foreach (DataRow row in deduplicated.Rows) {
    bool hasEmail = !string.IsNullOrWhiteSpace(row["Email"].ToString());
    bool hasPhone = !string.IsNullOrWhiteSpace(row["Phone"].ToString());

    if (hasEmail && hasPhone)
        row["Status"] = "Complete";
    else if (hasEmail || hasPhone)
        row["Status"] = "Partial";
    else
        row["Status"] = "Incomplete";
}

```

### Use Case 5: Report Generation with Aggregations

**Scenario**: Generate sales summary report from transaction data

```csharp
// Input: transactionsDt (columns: Date, Product, Category, Amount, SalesRep)

// Calculate summary metrics
var summary = transactionsDt.AsEnumerable()
    .GroupBy(r => new {
        Month = r.Field<DateTime>("Date").ToString("yyyy-MM"),
        Category = r.Field<string>("Category")
    })
    .Select(g => new {
        Month = g.Key.Month,
        Category = g.Key.Category,
        TotalSales = g.Sum(r => r.Field<decimal>("Amount")),
        AvgTransaction = g.Average(r => r.Field<decimal>("Amount")),
        TransactionCount = g.Count(),
        TopProduct = g.GroupBy(r => r.Field<string>("Product"))
                      .OrderByDescending(p => p.Sum(r => r.Field<decimal>("Amount")))
                      .First().Key
    })
    .OrderBy(x => x.Month)
    .ThenByDescending(x => x.TotalSales)
    .ToList();

// Create report DataTable
DataTable reportDt = new DataTable();
reportDt.Columns.Add("Month", typeof(string));
reportDt.Columns.Add("Category", typeof(string));
reportDt.Columns.Add("TotalSales", typeof(decimal));
reportDt.Columns.Add("AvgTransaction", typeof(decimal));
reportDt.Columns.Add("TransactionCount", typeof(int));
reportDt.Columns.Add("TopProduct", typeof(string));

foreach (var item in summary) {
    reportDt.Rows.Add(
        item.Month,
        item.Category,
        item.TotalSales,
        Math.Round(item.AvgTransaction, 2),
        item.TransactionCount,
        item.TopProduct
    );
}

// Calculate grand totals
decimal grandTotal = summary.Sum(s => s.TotalSales);
int totalTransactions = summary.Sum(s => s.TransactionCount);
decimal overallAvg = totalTransactions > 0 ? grandTotal / totalTransactions : 0;

// Add totals row
DataRow totalRow = reportDt.NewRow();
totalRow["Month"] = "TOTAL";
totalRow["Category"] = "ALL";
totalRow["TotalSales"] = grandTotal;
totalRow["AvgTransaction"] = Math.Round(overallAvg, 2);
totalRow["TransactionCount"] = totalTransactions;
totalRow["TopProduct"] = "";
reportDt.Rows.Add(totalRow);

// Format as HTML table for email
StringBuilder htmlReport = new StringBuilder();
htmlReport.AppendLine("<html><body>");
htmlReport.AppendLine("<h2>Sales Summary Report</h2>");
htmlReport.AppendLine("<table border='1' cellpadding='5' cellspacing='0'>");

// Headers
htmlReport.AppendLine("<tr style='background-color: #4CAF50; color: white;'>");
foreach (DataColumn col in reportDt.Columns) {
    htmlReport.AppendLine($"<th>{col.ColumnName}</th>");
}
htmlReport.AppendLine("</tr>");

// Data rows
foreach (DataRow row in reportDt.Rows) {
    string rowStyle = row["Month"].ToString() == "TOTAL" ?
        "style='font-weight: bold; background-color: #f2f2f2;'" : "";
    htmlReport.AppendLine($"<tr {rowStyle}>");
    foreach (var cell in row.ItemArray) {
        string value = cell.ToString();
        if (decimal.TryParse(value, out decimal num)) {
            value = num.ToString("C"); // Format as currency
        }
        htmlReport.AppendLine($"<td>{value}</td>");
    }
    htmlReport.AppendLine("</tr>");
}

htmlReport.AppendLine("</table>");
htmlReport.AppendLine("</body></html>");

string htmlOutput = htmlReport.ToString();
// Use Send Outlook Mail Message with htmlOutput as HTML body

```

### Use Case 6: Dynamic Validation and Error Reporting

**Scenario**: Validate data quality and generate error report

```csharp
// Input: inputDt (DataTable to validate)

DataTable errorsDt = new DataTable();
errorsDt.Columns.Add("RowNumber", typeof(int));
errorsDt.Columns.Add("ColumnName", typeof(string));
errorsDt.Columns.Add("Value", typeof(string));
errorsDt.Columns.Add("ErrorType", typeof(string));
errorsDt.Columns.Add("ErrorMessage", typeof(string));

// Define validation rules (continued)
var validations = new Dictionary<string, Func<object, (bool isValid, string message)>>
{
    ["Email"] = (value) => {
        string email = value.ToString();
        bool valid = Regex.IsMatch(email, @"^[^@\s]+@[^@\s]+\.[^@\s]+$");
        return (valid, valid ? "" : "Invalid email format");
    },

    ["Phone"] = (value) => {
        string phone = Regex.Replace(value.ToString(), @"[^\d]", "");
        bool valid = phone.Length == 10;
        return (valid, valid ? "" : "Phone must be 10 digits");
    },

    ["Amount"] = (value) => {
        if (decimal.TryParse(value.ToString(), out decimal amount)) {
            bool valid = amount > 0;
            return (valid, valid ? "" : "Amount must be positive");
        }
        return (false, "Amount must be a valid number");
    },

    ["Date"] = (value) => {
        if (DateTime.TryParse(value.ToString(), out DateTime date)) {
            bool valid = date >= DateTime.Now.AddYears(-1) && date <= DateTime.Now;
            return (valid, valid ? "" : "Date must be within last year");
        }
        return (false, "Invalid date format");
    },

    ["Name"] = (value) => {
        string name = value.ToString().Trim();
        bool valid = !string.IsNullOrWhiteSpace(name) && name.Length >= 2;
        return (valid, valid ? "" : "Name must be at least 2 characters");
    }
};

// Validate each row
for (int i = 0; i < inputDt.Rows.Count; i++) {
    DataRow row = inputDt.Rows[i];

    foreach (DataColumn col in inputDt.Columns) {
        // Skip if no validation rule for this column
        if (!validations.ContainsKey(col.ColumnName)) continue;

        // Check for null/empty
        if (row.IsNull(col) || string.IsNullOrWhiteSpace(row[col].ToString())) {
            errorsDt.Rows.Add(i + 1, col.ColumnName, "", "Missing Value", $"{col.ColumnName} is required");
            continue;
        }

        // Apply validation rule
        var result = validations[col.ColumnName](row[col]);
        if (!result.isValid) {
            errorsDt.Rows.Add(
                i + 1,
                col.ColumnName,
                row[col].ToString(),
                "Validation Error",
                result.message
            );
        }
    }
}

// Check for duplicate emails
var duplicateEmails = inputDt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Email"))
    .Where(g => g.Count() > 1)
    .Select(g => g.Key);

foreach (var email in duplicateEmails) {
    var rowNumbers = inputDt.AsEnumerable()
        .Select((row, index) => new { row, index })
        .Where(x => x.row.Field<string>("Email") == email)
        .Select(x => x.index + 1);

    errorsDt.Rows.Add(
        string.Join(", ", rowNumbers),
        "Email",
        email,
        "Duplicate",
        $"Email appears in multiple rows: {string.Join(", ", rowNumbers)}"
    );
}

// Generate error summary
var errorSummary = errorsDt.AsEnumerable()
    .GroupBy(r => r.Field<string>("ErrorType"))
    .Select(g => new {
        ErrorType = g.Key,
        Count = g.Count(),
        AffectedRows = g.Select(r => r.Field<int>("RowNumber")).Distinct().Count()
    });

// Log or display errors
StringBuilder errorReport = new StringBuilder();
errorReport.AppendLine("=== DATA VALIDATION REPORT ===");
errorReport.AppendLine($"Total Rows Processed: {inputDt.Rows.Count}");
errorReport.AppendLine($"Total Errors Found: {errorsDt.Rows.Count}");
errorReport.AppendLine($"Rows with Errors: {errorsDt.AsEnumerable().Select(r => r.Field<int>("RowNumber")).Distinct().Count()}");
errorReport.AppendLine();

errorReport.AppendLine("Error Summary by Type:");
foreach (var summary in errorSummary) {
    errorReport.AppendLine($"  {summary.ErrorType}: {summary.Count} errors in {summary.AffectedRows} rows");
}

errorReport.AppendLine();
errorReport.AppendLine("Detailed Errors:");
foreach (DataRow error in errorsDt.Rows) {
    errorReport.AppendLine($"  Row {error["RowNumber"]}, {error["ColumnName"]}: {error["ErrorMessage"]}");
}

string errorReportText = errorReport.ToString();

// Filter out invalid rows for processing
var validRows = new HashSet<int>(
    errorsDt.AsEnumerable()
        .Select(r => r.Field<int>("RowNumber"))
);

DataTable validDt = inputDt.Clone();
for (int i = 0; i < inputDt.Rows.Count; i++) {
    if (!validRows.Contains(i + 1)) {
        validDt.ImportRow(inputDt.Rows[i]);
    }
}

// Output: validDt (clean data), errorsDt (errors), errorReportText (summary)

```

---

## <a name="best-practices"></a>8. Best Practices & Decision Framework

### Decision Tree: Activity vs Code

```
START: Need to process data?
│
├─► Is it a simple, single operation?
│   ├─► YES → Use Built-in Activity
│   │   Examples: Basic trim, single replace, simple split
│   │
│   └─► NO → Continue...
│
├─► Does it involve complex logic or multiple steps?
│   ├─► YES → Use Code (Invoke Code)
│   │   Examples: Multi-condition filtering, regex parsing
│   │
│   └─► NO → Continue...
│
├─► Processing >10,000 rows?
│   ├─► YES → Use Code (much faster)
│   │
│   └─► NO → Continue...
│
├─► Need regex or advanced string manipulation?
│   ├─► YES → Use Code
│   │
│   └─► NO → Continue...
│
├─► Team has programming skills?
│   ├─► YES → Prefer Code (more maintainable)
│   │
│   └─► NO → Use Activities (better for non-developers)
│
└─► RESULT: Consider both, use what's clearest

```

### Code Organization Best Practices

### 1. Namespace Imports

Always import required namespaces in UiPath:

```csharp
// In UiPath Studio: Imports panel
System
System.Data
System.Linq
System.Text.RegularExpressions
System.Collections.Generic
System.Text
System.IO

```

### 2. Error Handling in Code

```csharp
// Always wrap risky operations in try-catch
try {
    var filtered = dt.AsEnumerable()
        .Where(r => r.Field<int>("Age") > 30);

    // Handle empty results
    outputDt = filtered.Any() ? filtered.CopyToDataTable() : dt.Clone();
}
catch (InvalidCastException ex) {
    // Column type mismatch
    throw new Exception($"Data type error: {ex.Message}");
}
catch (ArgumentException ex) {
    // No rows to copy
    outputDt = dt.Clone();
}
catch (Exception ex) {
    // Log and rethrow
    Console.WriteLine($"Error processing data: {ex.Message}");
    throw;
}

```

### 3. Null Safety

```csharp
// Always check for null before operations
if (dt != null && dt.Rows.Count > 0) {
    // Process data
}

// Use null-conditional operators (C# 6+)
string value = row["Email"]?.ToString()?.Trim() ?? "";

// Check for DBNull
if (!row.IsNull("OptionalColumn")) {
    string val = row["OptionalColumn"].ToString();
}

```

### 4. Variable Naming Conventions

```csharp
// Good naming
DataTable customersDt;           // Suffix indicates type
string cleanEmail;               // Descriptive
var filteredRows;                // Clear purpose

// Avoid
DataTable dt1, dt2, dt3;        // Meaningless
string s, str, temp;            // Too generic
var x, y, z;                    // Unclear

```

### 5. Code Comments

```csharp
// Explain WHY, not WHAT
// Bad: Loop through rows
foreach (DataRow row in dt.Rows) { }

// Good: Filter out test accounts before processing
foreach (DataRow row in dt.Rows) {
    if (!row["Email"].ToString().Contains("@test.com")) {
        // Process real accounts
    }
}

// Document complex regex
// Pattern matches invoice format: INV-YYYY-NNNNN
// Example: INV-2023-00123
string pattern = @"INV-\d{4}-\d{5}";

```

### 6. Modular Code Structure

```csharp
// Break complex operations into methods (in Invoke Method or custom activities)

// Method 1: Validation
public static bool IsValidEmail(string email) {
    if (string.IsNullOrWhiteSpace(email)) return false;
    return Regex.IsMatch(email, @"^[^@\s]+@[^@\s]+\.[^@\s]+$");
}

// Method 2: Cleaning
public static string CleanPhoneNumber(string phone) {
    string digits = Regex.Replace(phone, @"[^\d]", "");
    if (digits.Length != 10) return phone;
    return $"({digits.Substring(0, 3)}) {digits.Substring(3, 3)}-{digits.Substring(6)}";
}

// Method 3: Processing
public static DataTable ProcessCustomers(DataTable input) {
    var output = input.Clone();
    foreach (DataRow row in input.Rows) {
        if (IsValidEmail(row["Email"].ToString())) {
            row["Phone"] = CleanPhoneNumber(row["Phone"].ToString());
            output.ImportRow(row);
        }
    }
    return output;
}

// Main workflow: simply call ProcessCustomers(rawDt)

```

### Performance Optimization Tips

### 1. Minimize CopyToDataTable() Calls

```csharp
// Bad: Multiple CopyToDataTable calls
var temp1 = dt.AsEnumerable().Where(...).CopyToDataTable();
var temp2 = temp1.AsEnumerable().Where(...).CopyToDataTable();
var final = temp2.AsEnumerable().OrderBy(...).CopyToDataTable();

// Good: Chain operations, single copy
var final = dt.AsEnumerable()
    .Where(...)
    .Where(...)
    .OrderBy(...)
    .CopyToDataTable();

```

### 2. Use Compiled Regex for Repeated Patterns

```csharp
// Bad: Recompile regex in loop
foreach (string text in texts) {
    bool match = Regex.IsMatch(text, @"\d{4}-\d{2}-\d{2}");
}

// Good: Compile once
Regex dateRegex = new Regex(@"\d{4}-\d{2}-\d{2}", RegexOptions.Compiled);
foreach (string text in texts) {
    bool match = dateRegex.IsMatch(text);
}

```

### 3. StringBuilder for String Concatenation

```csharp
// Bad: String concatenation in loop (creates many objects)
string result = "";
foreach (DataRow row in dt.Rows) {
    result += row["Name"].ToString() + ",";
}

// Good: StringBuilder (efficient)
StringBuilder sb = new StringBuilder();
foreach (DataRow row in dt.Rows) {
    sb.Append(row["Name"].ToString()).Append(",");
}
string result = sb.ToString();

```

### 4. Batch Database Operations

```csharp
// Bad: Individual database updates
foreach (DataRow row in dt.Rows) {
    ExecuteNonQuery($"UPDATE Customers SET Status='Active' WHERE ID={row["ID"]}");
}

// Good: Bulk update or batch operations
using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connectionString)) {
    bulkCopy.DestinationTableName = "Customers";
    bulkCopy.WriteToServer(dt);
}

```

### 5. Avoid Nested LINQ Queries

```csharp
// Bad: O(n²) complexity
var result = dt1.AsEnumerable()
    .Select(r1 => new {
        ID = r1.Field<int>("ID"),
        MatchCount = dt2.AsEnumerable()
            .Count(r2 => r2.Field<int>("CustomerID") == r1.Field<int>("ID"))
    });

// Good: Join or Dictionary lookup O(n)
var countDict = dt2.AsEnumerable()
    .GroupBy(r => r.Field<int>("CustomerID"))
    .ToDictionary(g => g.Key, g => g.Count());

var result = dt1.AsEnumerable()
    .Select(r1 => new {
        ID = r1.Field<int>("ID"),
        MatchCount = countDict.ContainsKey(r1.Field<int>("ID")) ? countDict[r1.Field<int>("ID")] : 0
    });

```

### Code Reusability Strategies

### 1. Create Workflow Files

Store commonly used code in separate workflow files:

```
Project Structure:
├── Main.xaml
├── Workflows/
│   ├── CleanDataTable.xaml (Invoke Code with cleaning logic)
│   ├── ValidateEmail.xaml (Invoke Method)
│   └── ParseInvoiceText.xaml (Invoke Code with regex)
└── Config/
    └── RegexPatterns.xlsx (Store patterns)

```

### 2. Use Arguments for Flexibility

```csharp
// In Invoke Code activity:
// Arguments:
//   IN - inputDt (DataTable)
//   IN - filterColumn (String)
//   IN - filterValue (String)
//   OUT - outputDt (DataTable)

outputDt = inputDt.AsEnumerable()
    .Where(r => r.Field<string>(filterColumn) == filterValue)
    .CopyToDataTable();

```

### 3. Library Project for Custom Activities

For advanced users, create a UiPath Library project with reusable components:

```csharp
// In Visual Studio (UiPath Library):
[Activity(Name = "Clean Customer Data")]
public class CleanCustomerData : CodeActivity<DataTable>
{
    [RequiredArgument]
    [Input("Input DataTable")]
    public InArgument<DataTable> InputTable { get; set; }

    protected override DataTable Execute(CodeActivityContext context)
    {
        DataTable input = InputTable.Get(context);
        // Cleaning logic here
        return cleanedTable;
    }
}

```

### Testing and Debugging

### 1. Log Intermediate Results

```csharp
// Add logging in Invoke Code
Console.WriteLine($"Processing {dt.Rows.Count} rows");

var filtered = dt.AsEnumerable().Where(...);
Console.WriteLine($"After filter: {filtered.Count()} rows");

// Or use UiPath Log Message activity after code blocks

```

### 2. Test with Small Datasets

```csharp
// During development, limit data
if (dt.Rows.Count > 100) {
    dt = dt.AsEnumerable().Take(100).CopyToDataTable();
}
// Remove or comment out before production

```

### 3. Unit Test Complex Methods

```csharp
// Create test cases
public static void TestEmailValidation()
{
    Debug.Assert(IsValidEmail("test@example.com") == true);
    Debug.Assert(IsValidEmail("invalid.email") == false);
    Debug.Assert(IsValidEmail("") == false);
    Console.WriteLine("Email validation tests passed");
}

```

### 4. Use Try-Catch with Detailed Messages

```csharp
try {
    // Complex operation
    var result = dt.AsEnumerable()
        .Where(r => r.Field<decimal>("Amount") > 1000)
        .CopyToDataTable();
}
catch (Exception ex) {
    string errorDetails = $@"
        Error: {ex.Message}
        Stack: {ex.StackTrace}
        DataTable Rows: {dt?.Rows.Count ?? 0}
        DataTable Columns: {dt?.Columns.Count ?? 0}
    ";
    throw new Exception(errorDetails, ex);
}

```

---

## <a name="examples"></a>9. Code Examples & Exercises

### Example 1: Complete Email Parser

```csharp
// ===== Email Parser Class =====
using System;
using System.Data;
using System.Text.RegularExpressions;
using System.Linq;

// Input: emailBody (String), outputDt (DataTable - out)

// Create output structure
outputDt = new DataTable("ParsedEmails");
outputDt.Columns.Add("OrderNumber", typeof(string));
outputDt.Columns.Add("CustomerName", typeof(string));
outputDt.Columns.Add("Email", typeof(string));
outputDt.Columns.Add("Phone", typeof(string));
outputDt.Columns.Add("TotalAmount", typeof(decimal));
outputDt.Columns.Add("OrderDate", typeof(DateTime));
outputDt.Columns.Add("ItemCount", typeof(int));

// Define patterns
string orderPattern = @"Order\s*(?:Number|#):\s*([A-Z0-9-]+)";
string namePattern = @"Customer:\s*(.+?)(?:\r|\n|$)";
string emailPattern = @"Email:\s*([^\s@]+@[^\s@]+\.[^\s@]+)";
string phonePattern = @"Phone:\s*([\d\(\)\s\-\.]+)";
string totalPattern = @"Total:\s*\$?([\d,]+\.\d{2})";
string datePattern = @"Date:\s*(\d{1,2}[\/\-]\d{1,2}[\/\-]\d{4})";
string itemPattern = @"-\s*(.+?)\s+x(\d+)";

try {
    // Extract main fields
    Match orderMatch = Regex.Match(emailBody, orderPattern);
    Match nameMatch = Regex.Match(emailBody, namePattern);
    Match emailMatch = Regex.Match(emailBody, emailPattern);
    Match phoneMatch = Regex.Match(emailBody, phonePattern);
    Match totalMatch = Regex.Match(emailBody, totalPattern);
    Match dateMatch = Regex.Match(emailBody, datePattern);

    // Parse values
    string orderNum = orderMatch.Success ? orderMatch.Groups[1].Value : "UNKNOWN";
    string customerName = nameMatch.Success ? nameMatch.Groups[1].Value.Trim() : "UNKNOWN";
    string email = emailMatch.Success ? emailMatch.Groups[1].Value : "";
    string phone = phoneMatch.Success ? phoneMatch.Groups[1].Value.Trim() : "";

    decimal total = 0;
    if (totalMatch.Success) {
        decimal.TryParse(totalMatch.Groups[1].Value.Replace(",", ""), out total);
    }

    DateTime orderDate = DateTime.Now;
    if (dateMatch.Success) {
        DateTime.TryParse(dateMatch.Groups[1].Value, out orderDate);
    }

    // Count items
    MatchCollection items = Regex.Matches(emailBody, itemPattern);
    int itemCount = items.Count;

    // Add row
    outputDt.Rows.Add(orderNum, customerName, email, phone, total, orderDate, itemCount);

    Console.WriteLine($"Successfully parsed order {orderNum}");
}
catch (Exception ex) {
    Console.WriteLine($"Error parsing email: {ex.Message}");
    // Add error row
    outputDt.Rows.Add("ERROR", ex.Message, "", "", 0, DateTime.Now, 0);
}

```

### Example 2: Advanced DataTable Filtering

```csharp
// ===== Multi-Criteria Filter with Dynamic Conditions =====
// Inputs: sourceDt, conditions (Dictionary<string,object>), operator (string: "AND" or "OR")

using System;
using System.Data;
using System.Linq;
using System.Collections.Generic;

// Example conditions:
// conditions["Status"] = "Active"
// conditions["MinAge"] = 18
// conditions["Country"] = "USA"

try {
    var query = sourceDt.AsEnumerable();

    // Apply filters based on operator
    if (operator == "AND") {
        // All conditions must match
        foreach (var condition in conditions) {
            string columnName = condition.Key;
            object value = condition.Value;

            // Handle different data types
            if (value is int) {
                int intValue = (int)value;
                if (columnName.StartsWith("Min")) {
                    string actualColumn = columnName.Substring(3); // Remove "Min" prefix
                    query = query.Where(r => r.Field<int>(actualColumn) >= intValue);
                }
                else if (columnName.StartsWith("Max")) {
                    string actualColumn = columnName.Substring(3);
                    query = query.Where(r => r.Field<int>(actualColumn) <= intValue);
                }
                else {
                    query = query.Where(r => r.Field<int>(columnName) == intValue);
                }
            }
            else if (value is string) {
                string strValue = value.ToString();
                if (strValue.Contains("*")) {
                    // Wildcard matching
                    string pattern = "^" + Regex.Escape(strValue).Replace("\\*", ".*") + "$";
                    query = query.Where(r => Regex.IsMatch(r.Field<string>(columnName), pattern));
                }
                else {
                    query = query.Where(r => r.Field<string>(columnName) == strValue);
                }
            }
            else if (value is DateTime) {
                DateTime dateValue = (DateTime)value;
                query = query.Where(r => r.Field<DateTime>(columnName).Date == dateValue.Date);
            }
        }
    }
    else { // OR operator
        // At least one condition must match
        var orResults = new HashSet<DataRow>();

        foreach (var condition in conditions) {
            var matches = sourceDt.AsEnumerable()
                .Where(r => {
                    // Similar logic as above for each condition
                    try {
                        if (condition.Value is string) {
                            return r.Field<string>(condition.Key) == condition.Value.ToString();
                        }
                        // Add other type checks
                        return false;
                    }
                    catch { return false; }
                });

            foreach (var match in matches) {
                orResults.Add(match);
            }
        }

        query = orResults.AsEnumerable();
    }

    // Convert back to DataTable
    filteredDt = query.Any() ? query.CopyToDataTable() : sourceDt.Clone();

    Console.WriteLine($"Filtered from {sourceDt.Rows.Count} to {filteredDt.Rows.Count} rows");
}
catch (Exception ex) {
    Console.WriteLine($"Filter error: {ex.Message}");
    filteredDt = sourceDt.Clone();
}

```

### Example 3: CSV Export with Custom Formatting

```csharp
// ===== Export DataTable to Formatted CSV =====
using System;
using System.Data;
using System.Text;
using System.Linq;
using System.IO;

// Inputs: dataDt, outputPath, includeHeaders, dateFormat, numberFormat

try {
    StringBuilder csv = new StringBuilder();

    // Add headers
    if (includeHeaders) {
        var headers = dataDt.Columns.Cast<DataColumn>()
            .Select(c => EscapeCsvField(c.ColumnName));
        csv.AppendLine(string.Join(",", headers));
    }

    // Add data rows
    foreach (DataRow row in dataDt.Rows) {
        var fields = dataDt.Columns.Cast<DataColumn>()
            .Select(col => {
                if (row.IsNull(col)) return "";

                object value = row[col];

                // Format based on type
                if (value is DateTime) {
                    return ((DateTime)value).ToString(dateFormat ?? "yyyy-MM-dd");
                }
                else if (value is decimal || value is double) {
                    decimal num = Convert.ToDecimal(value);
                    return num.ToString(numberFormat ?? "0.00");
                }
                else {
                    return EscapeCsvField(value.ToString());
                }
            });

        csv.AppendLine(string.Join(",", fields));
    }

    // Write to file
    File.WriteAllText(outputPath, csv.ToString(), Encoding.UTF8);

    Console.WriteLine($"Exported {dataDt.Rows.Count} rows to {outputPath}");
}
catch (Exception ex) {
    throw new Exception($"CSV export failed: {ex.Message}", ex);
}

// Helper method
string EscapeCsvField(string field) {
    if (field.Contains(",") || field.Contains("\"") || field.Contains("\n")) {
        return "\"" + field.Replace("\"", "\"\"") + "\"";
    }
    return field;
}

```

### Exercise Problems

### Exercise 1: Email Address Extractor

**Task**: Extract all unique email addresses from a text file and categorize by domain.

**Input**: Text file content (String)
**Output**: DataTable with columns: Email, Domain, Count

**Hints**:

- Use regex pattern: `[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}`
- Group by domain using LINQ
- Sort by count descending

### Exercise 2: Transaction Reconciliation

**Task**: Compare two transaction lists and identify discrepancies.

**Inputs**: bankDt, systemDt (both have columns: Date, Amount, Reference)
**Output**: Three DataTables: matchedDt, bankOnlyDt, systemOnlyDt

**Hints**:

- Use LINQ join operations
- Compare based on Reference field
- Handle Amount mismatches

### Exercise 3: Data Quality Scorer

**Task**: Score each row based on completeness and validity.

**Input**: customerDt (columns: Name, Email, Phone, Address, City, Zip)
**Output**: Add "QualityScore" column (0-100)

**Scoring Rules**:

- Each filled field: +10 points
- Valid email format: +20 points
- Valid phone format: +15 points
- Valid ZIP format: +5 points

### Exercise 4: Text Report Parser

**Task**: Parse a structured text report into a DataTable.

**Input**:

```
SALES REPORT - Q3 2023
======================
Region: North | Rep: John Doe | Sales: $50,000
Region: South | Rep: Jane Smith | Sales: $45,000
Region: East | Rep: Bob Johnson | Sales: $60,000

```

**Output**: DataTable with columns: Region, Rep, Sales

**Hints**:

- Split by lines
- Use regex with named groups: `Region:\s*(?<region>\w+)\s*\|\s*Rep:\s*(?<rep>[\w\s]+)\s*\|\s*Sales:\s*\$?(?<sales>[\d,]+)`

### Exercise 5: Hierarchical Data Flattener

**Task**: Convert parent-child relationship data to flat structure.

**Input**: employeeDt (columns: EmployeeID, Name, ManagerID)
**Output**: flatDt (columns: EmployeeID, EmployeeName, ManagerName, Level)

**Hints**:

- Use recursive or iterative approach
- Join table with itself
- Calculate organizational level

---

## <a name="troubleshooting"></a>10. Troubleshooting & Common Pitfalls

### Common Errors and Solutions

### 1. "Cannot create DataTable from empty enumerable"

**Problem**: Calling `CopyToDataTable()` on empty LINQ result

```csharp
// Error:
var result = dt.AsEnumerable()
    .Where(r => r.Field<int>("Age") > 100) // No matches
    .CopyToDataTable(); // ERROR!

// Solution 1: Check before copy
var filtered = dt.AsEnumerable().Where(r => r.Field<int>("Age") > 100);
DataTable result = filtered.Any() ? filtered.CopyToDataTable() : dt.Clone();

// Solution 2: Extension method (create once, reuse)
public static DataTable SafeCopyToDataTable<T>(this IEnumerable<T> source, DataTable template) where T : DataRow {
    return source.Any() ? source.CopyToDataTable() : template.Clone();
}

```

### 2. "Specified cast is not valid"

**Problem**: Wrong data type in `Field<T>()`

```csharp
// Error: Column is string, not int
int age = row.Field<int>("Age"); // ERROR if Age contains "thirty"

// Solution: Check type and convert safely
int age = 0;
if (!row.IsNull("Age")) {
    if (row["Age"] is int) {
        age = row.Field<int>("Age");
    }
    else {
        int.TryParse(row["Age"].ToString(), out age);
    }
}

// Or use Convert
int age = row.IsNull("Age") ? 0 : Convert.ToInt32(row["Age"]);

```

### 3. "Column 'X' does not belong to table"

**Problem**: Referencing non-existent column

```csharp
// Error:
string email = row["EmailAddress"].ToString(); // Column is actually "Email"

// Solution 1: Check if column exists
if (dt.Columns.Contains("EmailAddress")) {
    string email = row["EmailAddress"].ToString();
}

// Solution 2: List all columns for debugging
string columnNames = string.Join(", ", dt.Columns.Cast<DataColumn>().Select(c => c.ColumnName));
Console.WriteLine($"Available columns: {columnNames}");

```

### 4. "Input string was not in a correct format"

**Problem**: Parsing failure (string to number/date)

```csharp
// Error:
decimal amount = decimal.Parse("$1,234.56"); // ERROR: contains $ and comma

// Solution: Clean before parsing
string cleaned = Regex.Replace("$1,234.56", @"[^\d.]", "");
decimal amount = decimal.Parse(cleaned); // 1234.56

// Or use TryParse
decimal amount;
if (!decimal.TryParse(cleaned, out amount)) {
    amount = 0; // Default value
    Console.WriteLine("Failed to parse amount");
}

```

### 5. "Object reference not set to an instance"

**Problem**: Null reference

```csharp
// Error:
string upper = myString.ToUpper(); // myString is null

// Solution 1: Null check
if (myString != null) {
    string upper = myString.ToUpper();
}

// Solution 2: Null-conditional operator
string upper = myString?.ToUpper() ?? "";

// Solution 3: Null-coalescing
string safe = myString ?? "default";

```

### 6. Regex "Unrecognized escape sequence"

**Problem**: Not using verbatim string

```csharp
// Error:
string pattern = "\d{4}-\d{2}-\d{2}"; // \d not recognized

// Solution: Use @ for verbatim strings
string pattern = @"\d{4}-\d{2}-\d{2}";

// Or escape backslashes (not recommended)
string pattern = "\\d{4}-\\d{2}-\\d{2}";
```

### 7. "Index was outside the bounds of the array"

**Problem**: Accessing non-existent array/collection element

```csharp
// Error:
string[] parts = "name@domain".Split('@');
string domain = parts[2]; // Only 2 elements (0 and 1)

// Solution: Check length
string[] parts = "name@domain".Split('@');
if (parts.Length > 1) {
    string domain = parts[1];
}

// For DataTable rows
if (dt.Rows.Count > 0) {
    string value = dt.Rows[0]["Column"].ToString();
}

```

### 8. LINQ "Sequence contains no elements"

**Problem**: Calling First(), Last(), Single() on empty collection

```csharp
// Error:
var row = dt.AsEnumerable()
    .Where(r => r.Field<string>("Email") == "notfound@example.com")
    .First(); // ERROR if no matches

// Solution: Use FirstOrDefault
var row = dt.AsEnumerable()
    .Where(r => r.Field<string>("Email") == "notfound@example.com")
    .FirstOrDefault();

if (row != null) {
    // Process row
}

// Or check Any() first
var query = dt.AsEnumerable().Where(r => r.Field<string>("Email") == "notfound@example.com");
if (query.Any()) {
    var row = query.First();
}

```

### 9. "The given DataRow is not in the current DataRowCollection"

**Problem**: Trying to modify or remove row from wrong table

```csharp
// Error:
DataRow row = dt1.Rows[0];
dt2.Rows.Remove(row); // Row belongs to dt1, not dt2

// Solution: Use ImportRow to copy
DataRow row = dt1.Rows[0];
dt2.ImportRow(row); // Copies row to dt2

// For removing, ensure correct table
dt1.Rows.Remove(row); // Correct

```

### 10. Performance Issues with Large Datasets

**Problem**: Code runs slowly on large DataTables

```csharp
// Slow: Multiple iterations
foreach (DataRow row in dt.Rows) {
    // Process
}
foreach (DataRow row in dt.Rows) {
    // More processing
}

// Fast: Single iteration with all logic
foreach (DataRow row in dt.Rows) {
    // All processing here
}

// Even faster: LINQ single-pass operations
var result = dt.AsEnumerable()
    .Where(r => /* filter */)
    .Select(r => /* transform */)
    .CopyToDataTable();

```

### Debugging Strategies

### 1. Log DataTable Contents

```csharp
// Helper method to inspect DataTable
public static void LogDataTable(DataTable dt, string name, int maxRows = 5) {
    Console.WriteLine($"=== {name} ===");
    Console.WriteLine($"Rows: {dt.Rows.Count}, Columns: {dt.Columns.Count}");

    // Log column names and types
    Console.WriteLine("Columns:");
    foreach (DataColumn col in dt.Columns) {
        Console.WriteLine($"  {col.ColumnName} ({col.DataType.Name})");
    }

    // Log sample data
    Console.WriteLine($"First {Math.Min(maxRows, dt.Rows.Count)} rows:");
    for (int i = 0; i < Math.Min(maxRows, dt.Rows.Count); i++) {
        var values = dt.Rows[i].ItemArray.Select(v => v?.ToString() ?? "NULL");
        Console.WriteLine($"  Row {i}: {string.Join(" | ", values)}");
    }
    Console.WriteLine();
}

// Usage in Invoke Code
LogDataTable(inputDt, "Input Data");
// ... processing ...
LogDataTable(outputDt, "Output Data");

```

### 2. Step-by-Step LINQ Debugging

```csharp
// Break complex queries into steps
var step1 = dt.AsEnumerable();
Console.WriteLine($"Step 1 - Total rows: {step1.Count()}");

var step2 = step1.Where(r => r.Field<string>("Status") == "Active");
Console.WriteLine($"Step 2 - After status filter: {step2.Count()}");

var step3 = step2.Where(r => r.Field<int>("Age") > 18);
Console.WriteLine($"Step 3 - After age filter: {step3.Count()}");

var step4 = step3.OrderBy(r => r.Field<string>("Name"));
Console.WriteLine($"Step 4 - After sort: {step4.Count()}");

var result = step4.CopyToDataTable();

```

### 3. Regex Pattern Testing

```csharp
// Test regex patterns with sample data
string[] testCases = {
    "valid@email.com",
    "invalid.email",
    "another@test.co.uk",
    "bad@"
};

string pattern = @"^[^@\s]+@[^@\s]+\.[^@\s]+$";

Console.WriteLine($"Testing pattern: {pattern}");
foreach (string test in testCases) {
    bool match = Regex.IsMatch(test, pattern);
    Console.WriteLine($"  {test}: {(match ? "MATCH" : "NO MATCH")}");
}

```

### 4. Exception Details Logger

```csharp
public static string GetExceptionDetails(Exception ex) {
    StringBuilder sb = new StringBuilder();
    sb.AppendLine($"Exception Type: {ex.GetType().Name}");
    sb.AppendLine($"Message: {ex.Message}");
    sb.AppendLine($"Source: {ex.Source}");
    sb.AppendLine($"Stack Trace:");
    sb.AppendLine(ex.StackTrace);

    if (ex.InnerException != null) {
        sb.AppendLine();
        sb.AppendLine("Inner Exception:");
        sb.AppendLine(GetExceptionDetails(ex.InnerException));
    }

    return sb.ToString();
}

// Usage
try {
    // Risky operation
}
catch (Exception ex) {
    string details = GetExceptionDetails(ex);
    Console.WriteLine(details);
    // Log to file or UiPath Log Message
}

```

### Memory Management Tips

### 1. Dispose of Large Objects

```csharp
// For very large DataTables
DataTable largeDt = GetLargeData();
try {
    // Process data
}
finally {
    if (largeDt != null) {
        largeDt.Dispose();
        largeDt = null;
    }
}

// Or use using statement (if applicable)
using (DataTable tempDt = ProcessData()) {
    // Use tempDt
} // Automatically disposed

```

### 2. Clear Collections When Done

```csharp
// Clear DataTable rows (keeps structure)
dt.Rows.Clear();

// Clear specific collection
List<string> largeList = GetData();
// ... use list ...
largeList.Clear();
largeList = null; // Help GC

```

### 3. Process in Batches

```csharp
// Instead of loading entire dataset
int batchSize = 1000;
int totalRows = 100000;

for (int i = 0; i < totalRows; i += batchSize) {
    // Get batch (using Take/Skip or database pagination)
    var batch = allData.Skip(i).Take(batchSize);

    // Process batch
    ProcessBatch(batch);

    // Batch is automatically GC'd after each iteration
}

```

### Best Practices Checklist

### Before Writing Code

- [ ]  Can this be done with activities more simply?
- [ ]  What is the expected data size?
- [ ]  Are there edge cases (nulls, empty strings, special characters)?
- [ ]  Will this code be reused?

### While Writing Code

- [ ]  Import necessary namespaces
- [ ]  Use meaningful variable names
- [ ]  Add comments for complex logic
- [ ]  Handle null/empty cases
- [ ]  Use appropriate data types
- [ ]  Wrap risky operations in try-catch

### After Writing Code

- [ ]  Test with various inputs (valid, invalid, empty, large)
- [ ]  Check performance with realistic data sizes
- [ ]  Log intermediate results for debugging
- [ ]  Remove debug code/comments
- [ ]  Document inputs and outputs

### UiPath-Specific Tips

### 1. Invoke Code vs Invoke Method

```
Use Invoke Code when:
- Multiple statements needed
- Complex logic with variables
- Need loops or conditionals
- Want to keep related logic together

Use Invoke Method when:
- Single method call
- Static utility methods (String.Join, Regex.Match)
- Calling custom assembly methods
- Cleaner for simple operations

```

### 2. Managing Imports

```csharp
// In UiPath Studio, go to: Project Panel > Dependencies > Imports
// Add these commonly needed namespaces:

System                           // Basic types
System.Collections.Generic       // List, Dictionary, etc.
System.Data                      // DataTable
System.IO                        // File operations
System.Linq                      // LINQ queries
System.Text                      // StringBuilder, Encoding
System.Text.RegularExpressions   // Regex
System.Xml                       // XML processing (if needed)

// For DataTable LINQ:
System.Data.DataSetExtensions    // AsEnumerable()

```

### 3. Argument Direction

```
IN arguments:
- Read-only in code
- Pass data into code block

OUT arguments:
- Write-only in code
- Return data from code block
- Must be assigned in code

IN/OUT arguments:
- Can read and modify
- Changes affect original variable
- Use for DataTables you're modifying

```

### 4. Code vs Activities Performance Matrix

| Task | Activities | Code | Winner |
| --- | --- | --- | --- |
| Simple string split | Fast | Fast | Activities (readability) |
| 10,000 row loop | Slow | Fast | Code (60-70x faster) |
| Single regex match | Fast | Fast | Either |
| Complex multi-regex parsing | Verbose | Concise | Code (maintainability) |
| Basic DataTable filter | Fast | Fast | Activities (readability) |
| Multi-condition LINQ filter | N/A | Fast | Code (only option) |
| UI interaction | Fast | N/A | Activities (only option) |
| File I/O (small files) | Fast | Fast | Either |
| Large CSV processing | Slow | Fast | Code |

### Quick Reference: Common Code Snippets

### Filter DataTable by Multiple Conditions

```csharp
var filtered = dt.AsEnumerable()
    .Where(r => r.Field<string>("Status") == "Active" &&
                r.Field<int>("Age") >= 18 &&
                !r.IsNull("Email"))
    .CopyToDataTable();

```

### Extract Value with Regex

```csharp
string value = Regex.Match(text, @"Invoice:\s*(\d+)").Groups[1].Value;

```

### Safe DataTable Row Access

```csharp
string value = dt.Rows.Count > 0 && !dt.Rows[0].IsNull("Column")
    ? dt.Rows[0]["Column"].ToString()
    : "default";

```

### Remove Empty/Null Rows

```csharp
var nonEmpty = dt.AsEnumerable()
    .Where(r => r.ItemArray.Any(field =>
        field != null && !string.IsNullOrWhiteSpace(field.ToString())))
    .CopyToDataTable();

```

### Get Distinct Column Values

```csharp
var distinct = dt.AsEnumerable()
    .Select(r => r.Field<string>("Category"))
    .Distinct()
    .OrderBy(x => x)
    .ToList();

```

### Join Two DataTables

```csharp
var joined = from t1 in dt1.AsEnumerable()
             join t2 in dt2.AsEnumerable()
             on t1.Field<int>("ID") equals t2.Field<int>("CustomerID")
             select new {
                 Name = t1.Field<string>("Name"),
                 Order = t2.Field<string>("OrderNumber")
             };

```

### Group and Aggregate

```csharp
var summary = dt.AsEnumerable()
    .GroupBy(r => r.Field<string>("Category"))
    .Select(g => new {
        Category = g.Key,
        Count = g.Count(),
        Total = g.Sum(r => r.Field<decimal>("Amount"))
    })
    .OrderByDescending(x => x.Total)
    .ToList();

```

---

## Additional Resources & References

### UiPath Documentation

- **Invoke Code Activity**: [docs.uipath.com](https://docs.uipath.com/activities/other/latest/workflow/invoke-code)
- **String Activities**: [docs.uipath.com](https://docs.uipath.com/activities/other/latest/user-guide/string-activities)
- **DataTable Activities**: [docs.uipath.com](https://docs.uipath.com/activities/other/latest/user-guide/datatable-activities)

### .NET Documentation

- **String Class**: [Microsoft Docs - String](https://docs.microsoft.com/en-us/dotnet/api/system.string)
- **Regex Class**: [Microsoft Docs - Regex](https://docs.microsoft.com/en-us/dotnet/api/system.text.regularexpressions.regex)
- **LINQ**: [Microsoft Docs - LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)
- **DataTable**: [Microsoft Docs - DataTable](https://docs.microsoft.com/en-us/dotnet/api/system.data.datatable)

### UiPath Community Forum

- **Regex Cheat Sheet**: [forum.uipath.com](https://forum.uipath.com/t/cheatsheet-system-text-regularexpressions-regex/354895)
- **When to Use Code**: [forum.uipath.com](https://forum.uipath.com/t/when-to-write-code-custom-activity-vs-using-uipath-activities/84957)
- **Performance Discussions**: [forum.uipath.com](https://forum.uipath.com/t/loop-processing-time-activity-vs-code/205444)

### Online Tools

- **Regex Tester**: [regex101.com](https://regex101.com/) - Test and debug regex patterns
- **C# Fiddle**: [dotnetfiddle.net](https://dotnetfiddle.net/) - Test C# code snippets online
- **LINQ Pad**: [linqpad.net](https://www.linqpad.net/) - Test LINQ queries

---

## Summary & Key Takeaways

### Core Principles

1. **Use the right tool**: Activities for simple tasks, code for complex/performance-critical operations
2. **Performance matters**: Code is 60-100x faster for data-intensive loops
3. **Readability counts**: Write clear, maintainable code with good naming and comments
4. **Error handling is essential**: Always handle nulls, empty results, and exceptions
5. **Test thoroughly**: Validate with various inputs including edge cases

### When to Use Code

- Processing >10,000 rows
- Complex regex parsing
- Multi-condition filtering
- Advanced LINQ operations
- Custom algorithms
- Performance bottlenecks

### When to Use Activities

- Simple operations
- UI interaction
- Small datasets
- Team with non-developers
- Visual documentation needed

### Essential Skills

1. **String manipulation**: Substring, Split, Replace, Regex
2. **DataTable operations**: LINQ, filtering, aggregations
3. **Regex patterns**: Extraction, validation, cleaning
4. **Error handling**: Try-catch, null checks, validation
5. **Performance optimization**: Batching, efficient loops, compiled regex

### Quick Decision Guide

```
Question: Should I use code?
├─ Is it UI interaction? → NO (use activities)
├─ Is it a simple single operation? → MAYBE (activities are fine)
├─ Does it involve complex logic? → YES
├─ Are you processing >10K rows? → YES
├─ Do you need regex or advanced string parsing? → YES
└─ Is performance critical? → YES

```

### Final Tips

- Start simple, optimize when needed
- Document your code and regex patterns
- Create reusable components for common operations
- Use version control for complex workflows
- Test with realistic data volumes
- Monitor performance and iterate

---

**This study guide provides a comprehensive foundation for data and string manipulation in UiPath using C#, .NET, Regex, and LINQ. Practice the examples, work through the exercises, and refer back to specific sections as needed. The combination of UiPath's visual workflow capabilities with powerful .NET code creates highly efficient and maintainable automation solutions.**