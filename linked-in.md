Use the GMail MCP to retrieve all LinkedIn emails that include job postings. The emails might come from different addresses,
but all addresses use the `@linkedin.com` domain. Note the subject of each email found; the value will be labeled as
"sourceEmail" in the eventual output. Do not skip any emails from LinkedIn.

Job postings are made up of

1. The job title
2. A link to the job posting
3. The name of the hiring company
4. An OPTIONAL job salary
5. An OPTIONAL job location, for example hybrid, on-site, in-person, or remote

The result of the analysis should be a JSON file named after the current date and time, for example `linkedin-jobs-2026-06-22-17-13-00.json` (with
seconds as the last component). The file should include the information found, in this JSON Schema format:

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
      }
    },
    "required": [
      "title",
      "url",
      "company",
      "sourceEmail",
      "receivedAtUtc",
      "receivedAtLa"
    ],
    "additionalProperties": false
  },
  "minItems": 1
}
```

A sample should look like this:

```json
[
  {
    "title": "Senior Software Engineer",
    "url": "https://www.linkedin.com/path-to-job",
    "company": "Microsoft",
    "salary": "180K per year",
    "location": "In person",
    "sourceEmail": "Your weekly jobs digest: 12 new roles matching your profile",
    "receivedAtUtc": "2026-06-22T14:30:00Z",
    "receivedAtLa": "2026-06-22T07:30:00-07:00"
  }
]
```

The JSON file should be saved in the `Professional` folder inside my Google Drive.

MUST HAVES:

1. Look at ALL emails that are currently in my inbox
2. ALL emails should have the `@linkedin.com` domain
3. Deduplicate job postings by URL as much as possible.
4. Most emails are pretty long. Download emails before processing them.
5. The output JSON file must validate against the JSON Schema provided. If the validation fails, replace the `.json` extension with `.FAILED.json`.
