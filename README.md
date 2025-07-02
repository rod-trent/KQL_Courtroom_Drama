# KQL Courtroom Drama: Using the CourtEvidenceLogs Dataset

This README provides instructions for using the `CourtEvidenceLogs.csv` dataset, designed for the [*KQL Courtroom Drama: The Data Trial*](https://rodtrent.substack.com/p/kql-courtroom-drama-the-data-trial) blog post. The dataset simulates server logs for the fictional case of “The Great Outage,” where you, Attorney Query, use Kusto Query Language (KQL) to analyze evidence and defend the Network.

## Dataset Overview
The `CourtEvidenceLogs.csv` file contains server log data with the following columns:

- **Timestamp**: Date and time of the log entry (e.g., `2025-07-01 14:00:01`).
- **ServerID**: Identifier of the server (e.g., `SRV-042`, `SRV-001`).
- **ErrorCode**: Error code reported (e.g., `ERR-500`, `ERR-404`, `ERR-200`).
- **RequestSource**: Source of the request (`External` or `Internal`).

The dataset covers logs from July 1, 2025, and is used to investigate a server crash by identifying patterns, such as excessive external requests.

## Prerequisites
To use the dataset, you need:
- Access to a KQL-compatible environment, such as **Azure Data Explorer** (ADX) or **Azure Log Analytics**.
- Basic knowledge of KQL for writing queries.
- A tool to view or import CSV files (e.g., Excel, text editor, or ADX).

## Setup Instructions
Follow these steps to load and use the dataset:

1. **Download the Dataset**
   - Obtain the `CourtEvidenceLogs.csv` file from the blog post or repository.

2. **Import into Azure Data Explorer**
   - **Option 1: Manual Import**
     - Log in to the Azure Data Explorer web UI (https://dataexplorer.azure.com).
     - Create a new database or use an existing one.
     - Create a table with the schema:
       ```kql
       .create table CourtEvidenceLogs (
           Timestamp: datetime,
           ServerID: string,
           ErrorCode: string,
           RequestSource: string
       )
       ```
     - Ingest the CSV file using the following command:
       ```kql
       .ingest inline into table CourtEvidenceLogs <|
       @'path/to/CourtEvidenceLogs.csv'
       ```
       Replace `'path/to/CourtEvidenceLogs.csv'` with the file’s location or upload it via the UI.
   - **Option 2: Inline Data**
     - If you prefer not to import the CSV, copy the dataset content into a query using the `datatable` operator:
       ```kql
       let CourtEvidenceLogs = datatable(Timestamp: datetime, ServerID: string, ErrorCode: string, RequestSource: string)
       [
           datetime(2025-07-01 14:00:01), "SRV-042", "ERR-500", "External",
           datetime(2025-07-01 14:00:02), "SRV-042", "ERR-500", "External",
           // ... (copy all rows from the CSV)
       ];
       CourtEvidenceLogs
       ```

3. **Verify the Data**
   - Run a simple query to ensure the data loaded correctly:
     ```kql
     CourtEvidenceLogs
     | count
     ```
     This should return the number of rows (e.g., 20 for the sample dataset).

## Using the Dataset
The dataset is designed for the [blog’s](https://rodtrent.substack.com/p/kql-courtroom-drama-the-data-trial) interactive KQL challenges. Here’s how to use it:

1. **Run the Blog Post Challenges**
   - **Challenge 1: Prove the Crash Cause**
     Query the dataset to count errors by `ServerID` and `ErrorCode` for July 1, 2025:
     ```kql
     CourtEvidenceLogs
     | where Timestamp >= datetime(2025-07-01 00:00:00) and Timestamp < datetime(2025-07-01 23:59:59)
     | summarize ErrorCount=count() by ServerID, ErrorCode
     | order by ErrorCount desc
     ```
     Expected output: High counts of `ERR-500` errors on `SRV-042`.

   - **Challenge 2: Dig Deeper**
     Analyze external requests by hour:
     ```kql
     CourtEvidenceLogs
     | where RequestSource == "External"
     | summarize RequestCount=count() by ServerID, bin(Timestamp, 1h)
     | order by RequestCount desc
     ```
     This helps pinpoint the hour with the most external requests.

2. **Explore Further**
   - Experiment with other KQL operators, such as `where`, `join`, or `take`, to uncover additional insights.
   - Example: Find the most common error codes:
     ```kql
     CourtEvidenceLogs
     | summarize Count=count() by ErrorCode
     | order by Count desc
     ```

3. **Share Your Findings**
   - Use the [blog’s](https://rodtrent.substack.com/p/kql-courtroom-drama-the-data-trial) “Jury Verdict” poll to vote on what the data suggests.
   - Share your queries or results in the blog comments or on social media with #KQLCourtroom.

## Troubleshooting
- **Data Not Loading**: Ensure the CSV file is properly formatted and the table schema matches the columns.
- **Query Errors**: Verify column names and data types (e.g., `Timestamp` is `datetime`, not `string`).
- **No Results**: Check the `where` clause filters; the dataset only contains data for July 1, 2025.

## Additional Resources
- **KQL Documentation**: [Microsoft Learn KQL Reference](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/)
- **Azure Data Explorer Tutorial**: [ADX Getting Started](https://docs.microsoft.com/en-us/azure/data-explorer/start-using-kusto)
- **Blog Post**: Refer to [*KQL Courtroom Drama: The Data Trial*](https://rodtrent.substack.com/p/kql-courtroom-drama-the-data-trial) for context and more challenges.

Get ready to interrogate the data and build your case, Attorney Query! If you have questions or need help, drop a comment on the blog or contact the Kusto County Courthouse clerk.
