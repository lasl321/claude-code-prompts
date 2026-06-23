# Goal

The goal is to generate a JSON version of the `Job Postings` Google Sheet in the `Professional` folder. This file
is the main source of truth for job postings and application status.

# Tools

1. Google Drive connector
2. JSON Schema validator

# High Level Workflow

1. Download the Google Sheet
2. Convert column data to JSON data
3. Generate a JSON file with the results
4. Upload JSON results into Google Drive `Professional` folder

# Workflow Details

## Google Sheet Download

The `Job Postings` Google Sheet should be downloaded as a CSV file because of size. The Google Sheet is too large to process in the
original format.

## Converting Column Data to JSON Data

CSV data should be analyzed and converted to JSON data. Note that the `Notes` column may include commas which makes the
parsing process more complicated.

The output should adhere to the following JSON Schema:

```json{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "job-applications.schema.json",
  "title": "Job Applications",
  "type": "array",
  "items": {
    "type": "object",
    "required": ["company", "applied", "rejected", "lastContact"],
    "properties": {
      "company": {
        "type": "string"
      },
      "applied": {
        "type": "boolean"
      },
      "rejected": {
        "type": "boolean"
      },
      "lastContact": {
        "type": "string",
        "format": "date"
      },
      "source": {
        "type": "string",
        "enum": [
          "LinkedIn",
          "ZipRecruiter",
          "Builtin",
          "Wellfound",
          "Referral",
          "WelcomeToTheJungle",
          "RedBallon",
          "HackerNews",
          "RobertHalf"
        ]
      },
      "notes": {
        "type": "string"
      }
    },
    "additionalProperties": false
  }
}
```

For example, a document might look like:

```json
[
  {
    "company": "Acme Corp",
    "applied": true,
    "rejected": false,
    "lastContact": "2026-06-10",
    "source": "LinkedIn",
    "notes": "Had a great intro call with the hiring manager."
  },
  {
    "company": "Globex Inc",
    "applied": true,
    "rejected": true,
    "lastContact": "2026-05-28",
    "source": "Wellfound"
  },
  {
    "company": "Initech",
    "applied": false,
    "rejected": false,
    "lastContact": "2026-06-20",
    "source": "HackerNews",
    "notes": "Interesting role, still reviewing the JD."
  }
]
```

Columns in the source document should map as follows:

1. `Company` -> `company`
2. `Applied` -> `applied`
3. `Rejected` -> `rejected`
4. `Last Contact` -> `lastContact`
5. `Source` -> `source`
6. `Notes` -> `notes`

For boolean columns, all `Yes` values should map to `true`, while all `No` values should map to `false`.

## JSON File Generation

The result of the analysis should be a JSON file named after the current date and time in the format `all-job-postings-YYYY-MM-DD-HH-mm-ss.json`,
for example `all-job-postings-2026-06-22-17-13-00.json`.

## JSON File Validation

The JSON file should validate against the specified schema. If the validation fails, save the file with a `.FAILED.json` extension
instead, so that the file can be analyzed manually by a human.

## JSON File Storage

The JSON file should be saved in the `Professional` folder inside my Google Drive, using the Google Drive connector. The file name should be
based on the prefix `all-job-postings` and a times
