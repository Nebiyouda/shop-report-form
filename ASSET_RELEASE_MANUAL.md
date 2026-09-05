# ENG360 Asset Release Manual

## Purpose

Asset Release generates controlled cylinder release comments from an ingested asset table and a saved template for each part number.

The application has three routes:

- **Data**: load and inspect asset records
- **Template**: create and edit one comment template per P/N
- **Comment**: find an asset and generate its comment

## Data Route

Upload an `.xlsx`, `.xls`, or `.csv` file, or paste table data. For Excel workbooks, the first worksheet is imported.

Recommended headers:

```text
part number,serial number,DOM,HST
```

Example:

```csv
part number,serial number,DOM,HST
M03048-01,LH1175,1/7/23,1/2/23
```

Common header variants are accepted, including `part`, `part no`, `serial`, `serial no`, and `date of manufacture`.

### Preview, normalization, and sorting

After loading data, the preview shows:

- Part Number
- Serial Number
- DOM
- HST

The application normalizes and sorts rows by:

1. Part Number
2. Serial Number

Use the filter field in any preview header to narrow visible rows. Filtering does not delete or modify the lookup data.

### Supported date inputs

The importer interprets common date formats, including:

- `MM/YYYY`
- `MM-YYYY`
- `YYYY-MM-DD`
- `DD/MM/YYYY` when the first value cannot be a month
- `M/D/YY`, such as `1/7/23` and `1/13/23`
- Excel date serial values

Canonical output formats are:

- DOM: `MM/YYYY`
- Life expiry: `MM/YYYY`
- HST: `MM/YYYY`
- Next HST: `MM/YYYY`
- Next maintenance due: `DD/MM/YYYY`

## Template Route

1. Open **Template**.
2. Select a P/N, or enter a new P/N and click **Add part number**.
3. Edit the comment wording for that P/N.
4. Keep date placeholders where data should be inserted.
5. Click **Save template**.

Supported date placeholders:

```text
{{dom}}
{{hst}}
{{lifeExpiry}}
{{nextHst}}
{{nextMaintenanceDue}}
```

Only these date placeholders are replaced automatically. The rest of the template wording remains unchanged.

Built-in templates are available for:

- `9700G2ABF10A`
- `3552AAADAACXCD`
- `804044-15`
- `806835-01`
- `M03048`

## Comment Route

1. Load the asset data on the **Data** route.
2. Select a P/N, or enter a unique serial number directly.
3. If the serial uniquely identifies an asset, its P/N and template are selected automatically.
4. If the serial exists under multiple P/Ns, select the P/N first.
5. Click **Generate comment**.
6. Copy the generated comment or click **Print**.

Calculations:

```text
Life expiry      = DOM + 15 years
Next HST         = HST + 5 years
Maintenance due  = earlier of life expiry and next HST
```

P/N and serial matching ignores letter case, spaces, hyphens, slashes, and other punctuation. For example, `LH1175`, `lh1175`, and `LH 1175` can match the same source value.

## Data Privacy

Asset data is processed in the browser only:

1. The browser reads the selected file.
2. SheetJS converts the first worksheet into rows.
3. The browser normalizes and sorts the rows.
4. Lookup and date calculations run locally.
5. The browser combines the dates with the selected template.

The uploaded asset data is not sent to an ENG360 server or Supabase. It stays in page memory and is cleared when the page is refreshed or closed. Saved templates use browser `localStorage` and are specific to the current browser profile and device.

## Troubleshooting

### No matching asset

- Load the data on the **Data** route first.
- Confirm the serial appears in the preview.
- Select the P/N if the serial exists under multiple P/Ns.
- Confirm the row contains valid DOM and HST values.

### Invalid date

Use a supported date format. Consolidated tables using values such as `1/7/23` are accepted and displayed as `01/2023`.

### Template missing

- Open **Template**.
- Select the P/N.
- Use **Load saved** if needed.
- Remember that templates are saved only in the current browser profile.

### Excel file not loading

- Use `.xlsx`, `.xls`, or `.csv`.
- Put headers in the first row.
- Put records on the first worksheet.
- Check the Data preview after loading.
