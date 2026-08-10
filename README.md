# API Data Pipeline

A production-style n8n workflow that fetches paginated data from an external REST API, normalizes the records, removes sensitive fields, deduplicates the dataset, and synchronizes the cleaned output to Google Sheets.

![n8n Workflow](screenshots/workflow.png)

## Overview

This project demonstrates a common business automation pattern:

```text
API → Pagination → Data Processing → Sensitive Data Filtering → Deduplication → Google Sheets
```

The workflow uses DummyJSON Users as a public test API and processes the complete dataset automatically.

## Features

- Manual and scheduled execution
- REST API integration
- Automatic API pagination
- Data extraction and normalization
- Sensitive-data removal using an allowlist
- Deduplication by user ID
- Google Sheets upsert
- Retry handling for Google Sheets
- Run summary after completion

## Workflow

```text
Manual Trigger / Daily Schedule
              ↓
       Fetch API Pages
              ↓
         Extract Users
              ↓
          Normalize
              ↓
   Remove Sensitive Data
              ↓
      Deduplicate by ID
              ↓
     Google Sheets Upsert
              ↓
         Run Summary
```

## Data Cleaning

The source API contains many fields that are not needed in the final dataset.

Instead of maintaining a blacklist of sensitive fields, the workflow uses an allowlist. Only these fields are allowed to leave the cleaning stage:

- `id`
- `fullName`
- `age`
- `gender`
- `company`
- `department`
- `jobTitle`
- `country`
- `role`

Everything else is removed automatically.

## Pagination

The API is requested in pages of 25 records.

The workflow updates the `skip` query parameter on every request and stops when the API indicates that all available records have been fetched.

A maximum-request limit is also configured as a safety guard.

## Google Sheets Output

Create a Google Sheet with these headers in the first row:

```text
id | fullName | age | gender | company | department | jobTitle | country | role
```

Rows are matched by `id`, allowing the workflow to update existing records instead of blindly appending duplicates.

## Setup

1. Import `workflow.json` into n8n.
2. Create a Google Sheet.
3. Add the required headers to the first row.
4. Open the `Google Sheets - Upsert Users` node.
5. Connect your own Google Sheets credential.
6. Replace `PASTE_GOOGLE_SHEET_ID_HERE` with your spreadsheet ID.
7. Run the workflow using `Manual Trigger`.
8. Activate the workflow if you want the scheduled daily run.

## Example Use Cases

This pattern can be adapted for:

- CRM exports
- reporting pipelines
- analytics datasets
- API-to-Sheets synchronization
- data migration
- scheduled data cleanup
- PII filtering
- dashboard data sources

## Tech

- n8n
- REST APIs
- JSON
- JavaScript
- Google Sheets
