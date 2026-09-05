# ENG360 User Manual

## 1. Open ENG360

1. Open the ENG360 Pages URL or the local URL.
2. Sign in to the current demo shell with any non-empty email and password.
3. Choose an application from the left navigation.

The left navigation can be collapsed with the menu button. In collapsed mode, application icons remain available and the preference is remembered in the browser.

## 2. Shop Report Builder

Use Shop Report Builder to complete a workshop inspection and findings report.

### Form data

Enter the inspection details, including:

- Finding title
- Inspection date and time
- Date of manufacture
- Staff and work order details
- Aircraft, registration, component, part number, and serial number
- Vendor and reason for removal

### Images

1. Add images by browsing, dragging, or using the camera control.
2. Images are resized in the browser before being stored in the draft state.
3. The upload card shows the resulting size in KB.
4. The report uses two images per row.
5. Use Preview PDF or Export to PDF when the report is ready.

### Remarks formatting

The remarks editor supports:

- Bold
- Italic
- Bulleted lists
- Numbered lists

Drafts are stored in browser local storage. Drafts are not synchronized between browsers or users.

## 3. Asset Release Comment Generator

The Comment Generator has three routes:

- **Data**: ingest and inspect asset records
- **Template**: create one comment template per part number
- **Comment**: find an asset and generate its comment

### Data route

Upload an Excel or CSV file, or paste table data. The first worksheet is used for Excel workbooks.

Recommended headers:

```text
part number,serial number,DOM,HST
```

Example:

```csv
part number,serial number,DOM,HST
M03048-01,LH1175,1/7/23,1/2/23
```

The importer also accepts common header variants such as `part`, `part no`, `serial`, `serial no`, and `date of manufacture`.

### Date interpretation

Incoming dates can be supplied in common formats, including:

- `MM/YYYY`
- `MM-YYYY`
- `YYYY-MM-DD`
- `DD/MM/YYYY` when the first value cannot be a month
- `M/D/YY`, including values such as `1/7/23` and `1/13/23`
- Excel date serial values

The sample consolidated tables use `M/D/YY`. For example:

```text
1/7/23  -> 01/2023
1/13/23 -> 01/2023
```

The app normalizes displayed values to:

- DOM: `MM/YYYY`
- Life expiry: `MM/YYYY`
- HST: `MM/YYYY`
- Next HST: `MM/YYYY`
- Next maintenance due: `DD/MM/YYYY`

### Data preview and sorting

After loading data, the preview shows:

- Part Number
- Serial Number
- DOM
- HST

The rows are normalized and sorted by:

1. Part Number
2. Serial Number

Each preview header has a local filter. Filters only change the visible preview and do not remove rows from the lookup dataset.

### Template route

1. Open Template.
2. Select an existing part number or enter a new part number.
3. Click Add part number for a new template.
4. Edit the wording for that part number.
5. Save the template.

Each part number keeps its own comment wording. The template should contain date placeholders when dates must be inserted automatically:

```text
{{dom}}
{{hst}}
{{lifeExpiry}}
{{nextHst}}
{{nextMaintenanceDue}}
```

Only these date placeholders are replaced automatically. Other text remains part of the part-number template.

### Comment route

1. Load data from the Data route first.
2. Select a P/N, or enter a unique serial number directly.
3. If the serial number is unique, the matching P/N and template are selected automatically.
4. If a serial number exists under more than one P/N, select the P/N to disambiguate it.
5. Click Generate comment.
6. Copy the result or use Print.

The calculations are:

```text
Life expiry       = DOM + 15 years
Next HST          = HST + 5 years
Next maintenance  = earlier of Life expiry and Next HST
```

### Identifier matching

P/N and serial matching ignores:

- Letter case
- Spaces
- Hyphens
- Slashes
- Other punctuation

For example, `LH1175`, `lh1175`, and `LH 1175` can identify the same serial value.

## 4. Browser-only Processing

The Asset Release data flow is client-side:

1. The browser reads the selected file.
2. SheetJS converts the first worksheet to rows.
3. The browser normalizes and sorts the rows.
4. The browser performs P/N and serial lookup.
5. The browser calculates dates.
6. The browser combines dates with the selected template.

The uploaded asset table is not stored on the ENG360 server and is not sent to Supabase. It remains in page memory unless the user saves or copies something manually.

Templates are saved in browser `localStorage`, so they are specific to the browser profile and device. Clearing site data removes them.

## 5. Troubleshooting

### No matching asset

- Confirm the data was loaded on the Data route.
- Confirm the serial number exists in the preview.
- If the serial appears under multiple P/Ns, select the P/N first.
- Check that the row has DOM and HST values.

### Invalid DOM/HST

Use one of the supported date formats. For consolidated tables, `M/D/YY` values such as `1/7/23` are accepted.

### Template not appearing

- Open Template and load saved templates.
- Confirm the correct P/N is selected.
- Remember that saved templates belong to the current browser profile.

### Excel file does not load

- Confirm the file is `.xlsx`, `.xls`, or `.csv`.
- Put the expected headers in the first row.
- Put the asset records on the first worksheet.
- Check the Data preview after loading.
