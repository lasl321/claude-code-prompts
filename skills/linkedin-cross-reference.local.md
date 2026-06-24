# Goal

The user should provide two files for this task:

1. A LinkedIn job postings file, usually named `linkedin-jobs-YYYY-MM-DD-HH-mm-ss.json`, where a timestamp is used
   in the file name
2. A `Job Postings.csv` file that serves as the master list of jobs and job applications

If the user does not provide these files, stop the process and let the user know.

The goal of this task is to cross-reference the two files to determine which postings are worth applying to.

# Tools

1. A cross reference Python script (must be part of the context)

# High Level Workflow

1. Read the `Job Postings.csv` file
2. Read the LinkedIn job postings JSON file
3. Cross reference job postings in the LinkedIn file against the data from the `Job Postings.csv` file
4. Generate JSON data by _enhancing_ LinkedIn JSON data with new `jobStatus` and `notes` properties
5. Report success or failure, and include tips for speeding up the process

# Workflow Details

## Read Job Postings CSV file

The `Job Postings.csv` file is a CSV file. Ensure the file is present in the current context. If the file cannot
be found, stop the process and let the user know.

## Read LinkedIn Job Postings File

Read the LinkedIn job postings file. Ensure the file is present in the current context. If the file cannot be found,
stop the process and let the user know.

## Cross Referencing Process

The data from the two files should be cross referenced as follows. For each entry in the LinkedIn job postings file, check the
JSON `company` value against values in the `Job Postings.csv` `Company` column. If there is a match, save a `jobStatus` value
for later, as follows:

1. If the company name matches, if the `Applied` column value is `Yes` and the `Rejected` column value is `No`, set
   the `jobStatus` value to `Applied`.
2. If the company name matches, if the `Rejected` column value is `Yes`, set the `jobStatus` value to `Rejected`.
3. If the company name matches, if the `Applied` column value is `No`, set the `jobStatus` value to `Skipped`.
4. If the company name does not match any row, set the `jobStatus` value to `New`

Whenever present, the value of the `Notes` column should be saved as a new `notes` property.

IMPORTANT: If there are multiple matching rows in `Job Postings.csv`, use the most recent entry, where most recent
is defined as follows:

1. Latest `Last Contact` date
2. Later entries in the file win if `Last Contact` date is not present

## Generate JSON Document

The data from the LinkedIn file should be _enhanced_ to adhere to the following JSON Schema:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://lasl321.com/job-postings.schema.json",
  "title": "Job Postings",
  "description": "Schema for an array of job posting objects",
  "type": "array",
  "items": {
    "type": "object",
    "properties": {
      "title": {
        "type": "string",
        "description": "Job title"
      },
      "url": {
        "type": "string",
        "format": "uri",
        "description": "Link to the job posting"
      },
      "company": {
        "type": "string",
        "description": "Name of the hiring company"
      },
      "salary": {
        "type": "string",
        "description": "Compensation (e.g. '180K per year')"
      },
      "location": {
        "type": "string",
        "description": "Work arrangement or location (e.g. 'In person', 'Remote')"
      },
      "sourceEmail": {
        "type": "string",
        "description": "Subject line of the email that included this job posting"
      },
      "receivedAtUtc": {
        "type": "string",
        "format": "date-time",
        "description": "Timestamp of when the email arrived in UTC"
      },
      "receivedAtLa": {
        "type": "string",
        "format": "date-time",
        "description": "Timestamp of when the email arrived in Los Angeles time (America/Los_Angeles)"
      },
      "jobStatus": {
        "type": "string",
        "enum": ["New", "Applied", "Rejected", "Skipped"],
        "description": "Current status of the job: New (new posting), Applied (application outstanding), Rejected (previously rejected), Skipped (not worth applying)"
      },
      "notes": {
        "type": "string",
        "description": "Optional additional details about the job posting"
      }
    },
    "required": [
      "title",
      "url",
      "company",
      "sourceEmail",
      "receivedAtUtc",
      "receivedAtLa",
      "jobStatus"
    ],
    "additionalProperties": false
  },
  "minItems": 1
}
```

For example, a document might look like:

```json
[
  {
    "title": "Senior Software Engineer",
    "url": "httpsjobs matching Staff Engineer",
    "receivedAtUtc": "2026-06-21T18:00:00Z",
    "receivedAtLa": "2026-06-21T11:00:00-07:00",
    "jobStatus": "Skipped",
    "notes": "Office is in Irvine, too far to commute"
  },
  {
    "title": "Principal Engineer",
    "url": "https://hire.startupxyz.com/principal",
    "company": "StartupXYZ",
    "sourceEmail": "Top Tech Jobs – June 2026",
    "receivedAtUtc": "2026-06-23T09:15:00Z",
    "receivedAtLa": "2026-06-23T02:15:00-07:00",
    "jobStatus": "New"
  },
  {
    "title": "Engineering Manager",
    "url": "https://jobs.bigtech.com/em-role",
    "company": "BigTech Inc",
    "salary": "220K per year",
    "location": "Hybrid",
    "sourceEmail": "LinkedIn: Jobs picked for you",
    "receivedAtUtc": "2026-06-19T22:45:00Z",
    "receivedAtLa": "2026-06-19T15:45:00-07:00",
    "jobStatus": "Rejected",
    "notes": "Recruiter said they went with an internal candidate"
  }
]
```

## JSON File Validation

The JSON file should validate against the specified schema. If the validation fails, save the file with a `.FAILED.json` extension
instead, so that the file can be analyzed manually by a human.

## Save JSON Document Locally

The result of the analysis should be a JSON file named after the current date and time in the format `linkedin-jobs-cross-YYYY-MM-DD-HH-mm-ss.json`, for example `linkedin-jobs-cross-2026-06-22-17-13-00.json`. The file should be made available to the user so he can save it locally.
