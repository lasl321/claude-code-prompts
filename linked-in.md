# Goal

The goal is to retrieve job postings from LinkedIn emails that are currently in the Inbox. Only emails in the
Inbox should be analyzed. Whenever possible, job postings should be deduplicated, with the latest job posting
appearing in the output.

# Tools

1. GMail connector
2. Google Drive connector
3. JSON Schema validator

# High Level Workflow

1. Using the GMail connector, retrieve all emails from the Inbox that come from `@linkedin.com` addresses
2. For emails in step 1 that include job postings, extract the information listed in the `Job Posting Details` section below
3. Generate JSON objects with the job posting details. See the `JSON File Generation` section for details.
4. Validate the output JSON document against the JSON Schema in the `JSON File Generation` section below
5. Save the JSON document in Google Drive using the Google Drive connector
6. Report success or failure, and tips for speeding up the process

# Workflow Details

## Email Content Extraction

Use the GMail connector to retrieve all emails currently in the Inbox, that come from `@linkedin.com` addresses. For example,
email addresses include (but are not limited to):

- jobalerts-noreply@linkedin.com
- jobs-noreply@linkedin.com

IMPORTANT: If there are no emails in the Inbox, DO NOT look in other folders. Instead, indicate that there were no emails in the Inbox.
IMPORTANT: Emails can be very long. Download emails before performing analysis.

If there are emails in the Inbox, for each email, retrieve

1. The email content
2. The subject of the email (will be used later as `sourceEmail`)

## Job Posting Details

Using the email content extract the following information:

1. The job title (e.g. Senior Software Engineer)
2. The link to the job posting
3. The name of the hiring company
4. An OPTIONAL job salary
5. An OPTIONAL job location, for example hybrid, on-site, in-person, or remote
6. The subject of the email that included the job posting

## JSON File Generation

The result of the analysis should be a JSON file named after the current date and time in the format `linkedin-jobs-YYYY-MM-DD-HH-mm-ss.json`,
for example `linkedin-jobs-2026-06-22-17-13-00.json`. The file should include the information found, in this JSON Schema format:

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

## JSON File Validation

The JSON file should validate against the specified schema. If the validation fails, save the file with a `.FAILED.json` extension
instead, so that the file can be analyzed manually by a human.

## JSON File Storage

The JSON file should be saved in the `Professional` folder inside my Google Drive, using the Google Drive connector
