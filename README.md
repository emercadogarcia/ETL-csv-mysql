# ETL-CORP-01: Automated CSV Email Processing and Database Integration Workflow

## Overview
The **ETL-CORP-01** workflow is a robust and scalable Extract, Transform, Load (ETL) pipeline built using [n8n](https://n8n.io/), designed to automate the processing of CSV files received via email and seamlessly integrate the extracted data into a PostgreSQL database. This workflow ensures efficient data handling, validation, and transformation, making it ideal for financial and operational data processing in corporate environments.

The workflow is triggered by incoming Gmail messages, filters specific CSV attachments, validates and transforms the data, and loads it into a designated database table. It is optimized for reliability, error handling, and scalability, processing data in batches to ensure performance efficiency.

## Features
- **Automated Email Monitoring**: Polls Gmail every minute for new emails with specific subjects and attachments.
- **Robust CSV Parsing**: Detects delimiters (`,` or `;`), normalizes line breaks, and handles BOM (Byte Order Mark) for consistent data extraction.
- **Data Validation and Cleaning**: Filters emails by subject and attachment name, validates content, and normalizes financial data (e.g., currency formatting).
- **Batch Processing**: Processes data in batches of 100 records to optimize database insertion performance.
- **Database Integration**: Maps and inserts cleaned data into a PostgreSQL table (`hechos_pptogastos`) with precise schema alignment.
- **Error Handling**: Includes checks for invalid file formats and missing data to ensure data integrity.

## Workflow Structure
The workflow consists of the following nodes, connected to form an end-to-end ETL pipeline:

1. **Gmail Trigger**:
   - Monitors Gmail for new emails every minute.
   - Downloads attachments for processing.
   - Credentials: Configured with Gmail OAuth2 for secure access.

2. **Filtrar y Preparar Adjunto** (Filter and Prepare Attachment):
   - Validates email subject (must match "DATOS").
   - Filters for specific CSV attachment (`ppto_bicorps (1).csv`).
   - Removes BOM and rejects invalid files containing "Exif" metadata.

3. **Convertir Binario a Texto** (Convert Binary to Text):
   - Converts the binary CSV attachment to UTF-8 encoded text for further processing.

4. **Parsear CSV** (Parse CSV):
   - Detects the CSV delimiter (`,` or `;`) based on the header row.
   - Normalizes line breaks and removes empty lines.
   - Parses the CSV into JSON objects for downstream transformation.

5. **Limpieza Datos** (Data Cleaning):
   - Normalizes column names to uppercase and trims whitespace.
   - Converts financial amounts (e.g., "-1.378,08") to standardized numeric format (e.g., -1378.08).
   - Maps data to the required database schema.

6. **Split In Batches**:
   - Splits the processed data into batches of 100 records to optimize database insertion.

7. **Insert Rows in a Table**:
   - Inserts the cleaned and transformed data into the `hechos_pptogastos` table in a PostgreSQL database.
   - Maps JSON fields to database columns: `ejercicio`, `mes`, `monto`, `centro_contable`, `moneda`, `centro_de_costo`, `cuenta`.

## Database Schema
The workflow targets the `hechos_pptogastos` table in the `public` schema of a PostgreSQL database. The table includes the following columns:

| Column            | Type      | Required | Description                     |
|-------------------|-----------|----------|---------------------------------|
| `id`              | Number    | No       | Auto-generated ID (excluded)    |
| `empresa`         | String    | No       | Company identifier             |
| `centro_contable`| String    | Yes      | Accounting center              |
| `ejercicio`       | Number    | Yes      | Fiscal year                    |
| `mes`             | Number    | Yes      | Month                          |
| `moneda`          | String    | Yes      | Currency                       |
| `centro_de_costo`| String    | Yes      | Cost center                    |
| `cuenta`          | String    | Yes      | Account                        |
| `monto`           | Number    | Yes      | Amount                         |
| `created_at`      | DateTime  | No       | Record creation timestamp       |
| `updated_at`      | DateTime  | No       | Record update timestamp         |

## Setup Instructions
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/etl-corp-01.git
   ```

2. **Install n8n**:
   - Follow the [n8n installation guide](https://docs.n8n.io/getting-started/installation/) to set up n8n locally or on a server.
   - Ensure you have a running n8n instance.

3. **Import the Workflow**:
   - Copy the provided JSON workflow configuration (`etl-corp-01.json`) into the n8n workflow editor.
   - Alternatively, import the JSON file directly via the n8n UI.

4. **Configure Credentials**:
   - **Gmail OAuth2**: Set up a Gmail account with OAuth2 credentials in n8n for the `Gmail Trigger` node.
   - **PostgreSQL**: Configure PostgreSQL credentials with access to the `hechos_pptogastos` table in the `public` schema.

5. **Activate the Workflow**:
   - Enable the workflow in n8n to start polling Gmail and processing data.

## Prerequisites
- **n8n**: Version compatible with the provided workflow (tested with n8n 1.x).
- **Gmail Account**: With OAuth2 access configured for email monitoring.
- **PostgreSQL Database**: With the `hechos_pptogastos` table created in the `public` schema.
- **Node.js**: For local n8n installations.
- **Git**: For cloning the repository.

## Usage
- The workflow runs continuously, polling Gmail every minute for new emails.
- It processes emails with the subject "DATOS" and an attachment named `ppto_bicorps (1).csv`.
- The CSV data is cleaned, transformed, and inserted into the PostgreSQL database in batches.
- Monitor the n8n execution logs for any errors or issues during processing.

## Contributing
Contributions are welcome! Please follow these steps:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/YourFeature`).
3. Commit your changes (`git commit -m 'Add YourFeature'`).
4. Push to the branch (`git push origin feature/YourFeature`).
5. Open a pull request.

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact
For questions or support, please contact [your-email@example.com](mailto:your-email@example.com) or open an issue in the repository.