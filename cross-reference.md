In my Google Drive, in the `Professional` folder, find the most recent file that starts with `linked-in-jobs-` and ends with
`.json`.

For each entry in this file, cross reference each company name against the company names in the `Job Postings` file
in the same directory. Note that `Job Postings` should be downloaded and converted to a CSV file so that the
entire content can be analyzed.

If a company exists in `Job Postings`, check that status as follows:

1. If the company has been applied to, but not rejected, mark the job posting as `Applied`
2. If the company has been applied to, _and_ rejected, mark the job posting as `Rejected`
3. If the company has NOT been applied to, make the job posting as `Skipped` and note the `Notes` value for later
4. If the company has not been mentioned in the `Job Postings` data previously, make the job posting as `New`

The result of this analysis should be stored in a new Google Drive document in the same `Professional`
folder. The name of the Google Sheet should be `linked-in-jobs-cross-reference-2026-06-22-18-32-00` where the
last component is the seconds of the current time stamp.

MUST HAVES:

1. The `Job Postings` file should be downloaded as a CSV to perform proper cross-referencing
2. The output should be a formatted Google Sheet, NOT a CSV file
3. The headers of the Google Sheet should include all property names of the JSON file, plus the
   job posting status determined earlier (column name `Job Posting Status`). If a job posting is marked
   as `Skipped`, include a notes column with the name `Notes`.
4. URLs should be clickable
5. The background of each row should be colored according to the following legend:
   - Green: job posting is `New`
   - Red: job posting is `Rejected`
   - Yellow: job posting is `Skipped`
   - Orange: job posting is `Applied`
