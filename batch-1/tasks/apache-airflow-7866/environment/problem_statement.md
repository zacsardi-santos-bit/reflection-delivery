# Add Google Sheets Operators and Extend Hook with Spreadsheet Management Methods

## Description

The existing Google Sheets integration in Airflow provides a hook for reading and writing cell data, but it lacks the ability to manage spreadsheets at a higher level — there is no way to create a new spreadsheet, retrieve spreadsheet metadata, or enumerate the sheets within a spreadsheet. Additionally, there are no operators to automate data transfer between Google Cloud Storage and Google Sheets, which is a very common workflow for data engineers.

## Expected Behavior

- The Google Sheets hook should support retrieving a full spreadsheet by its ID, returning all metadata about the spreadsheet.
- The hook should support listing the titles of all sheets within a spreadsheet, with an optional filter to return only specific sheet titles.
- The hook should support creating a new spreadsheet from a configuration object and returning the created spreadsheet's metadata.
- A new operator should allow creating a new spreadsheet and publishing the resulting spreadsheet ID and URL as pipeline values for downstream tasks.
- A new operator should export all (or filtered) sheets from a Google Spreadsheet to Google Cloud Storage as individual CSV files, publishing the list of produced files for downstream use.
- A new operator should upload a CSV file from Google Cloud Storage into a Google Spreadsheet.

## Why This Matters

Many data workflows require moving data between Google Sheets and cloud storage systems. Without these operators, pipeline authors have to write custom code to perform these transfers. These additions close that gap and make it possible to build complete, declarative data pipelines involving Google Sheets entirely within Airflow.
