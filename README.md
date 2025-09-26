# ETL-csv-mysql: Automated CSV Email Processing and Database Integration Workflow

## English Version

### Overview
**ETL-csv-mysql** is a robust and scalable Extract, Transform, Load (ETL) pipeline built using [n8n](https://n8n.io/), designed to automate the processing of CSV files received via email and seamlessly integrate the extracted data into a MySQL database. This workflow ensures efficient data handling, validation, and transformation, making it ideal for financial and operational data processing in corporate environments.

The workflow is triggered by incoming Gmail messages, filters specific CSV attachments, validates and transforms the data, and loads it into a designated database table. It is optimized for reliability, error handling, and scalability, processing data in batches to ensure performance efficiency.

### Features
- **Automated Email Monitoring**: Polls Gmail every minute for new emails with specific subjects and attachments.
- **Robust CSV Parsing**: Detects delimiters (`,` or `;`), normalizes line breaks, and handles BOM (Byte Order Mark) for consistent data extraction.
- **Data Validation and Cleaning**: Filters emails by subject and attachment name, validates content, and normalizes financial data (e.g., currency formatting).
- **Batch Processing**: Processes data in batches of 100 records to optimize database insertion performance.
- **Database Integration**: Maps and inserts cleaned data into a MySQL table (`hechos_pptogastos`) with precise schema alignment.
- **Error Handling**: Includes checks for invalid file formats and missing data to ensure data integrity.

### Workflow Structure
The workflow consists of the following nodes, connected to form an end-to-end ETL pipeline:

1. **Gmail Trigger**:
   - Monitors Gmail for new emails every minute.
   - Downloads attachments for processing.
   - Credentials: Configured with Gmail OAuth2 for secure access.

2. **Filter and Prepare Attachment**:
   - Validates email subject (must match "DATOS").
   - Filters for specific CSV attachment (`ppto_bicorps (1).csv`).
   - Removes BOM and rejects invalid files containing "Exif" metadata.

3. **Convert Binary to Text**:
   - Converts the binary CSV attachment to UTF-8 encoded text for further processing.

4. **Parse CSV**:
   - Detects the CSV delimiter (`,` or `;`) based on the header row.
   - Normalizes line breaks and removes empty lines.
   - Parses the CSV into JSON objects for downstream transformation.

5. **Data Cleaning**:
   - Normalizes column names to uppercase and trims whitespace.
   - Converts financial amounts (e.g., "-1.378,08") to standardized numeric format (e.g., -1378.08).
   - Maps data to the required database schema.

6. **Split In Batches**:
   - Splits the processed data into batches of 100 records to optimize database insertion.

7. **Insert Rows in a Table**:
   - Inserts the cleaned and transformed data into the `hechos_pptogastos` table in a MySQL database.
   - Maps JSON fields to database columns: `ejercicio`, `mes`, `monto`, `centro_contable`, `moneda`, `centro_de_costo`, `cuenta`.

### Database Schema
The workflow targets the `hechos_pptogastos` table in a MySQL database. The table includes the following columns:

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

### Setup Instructions
1. **Clone the Repository**:
   ```bash
   git clone git@github.com:emercadogarcia/ETL-csv-mysql.git
   ```

2. **Install n8n**:
   - Follow the [n8n installation guide](https://docs.n8n.io/getting-started/installation/) to set up n8n locally or on a server.
   - Ensure you have a running n8n instance.

3. **Import the Workflow**:
   - Copy the provided JSON workflow configuration (`etl-csv-mysql.json`) into the n8n workflow editor.
   - Alternatively, import the JSON file directly via the n8n UI.

4. **Configure Credentials**:
   - **Gmail OAuth2**: Set up a Gmail account with OAuth2 credentials in n8n for the `Gmail Trigger` node.
   - **MySQL**: Configure MySQL credentials with access to the `hechos_pptogastos` table.

5. **Activate the Workflow**:
   - Enable the workflow in n8n to start polling Gmail and processing data.

### Prerequisites
- **n8n**: Version compatible with the provided workflow (tested with n8n 1.x).
- **Gmail Account**: With OAuth2 access configured for email monitoring.
- **MySQL Database**: With the `hechos_pptogastos` table created.
- **Node.js**: For local n8n installations.
- **Git**: For cloning the repository.

### Usage
- The workflow runs continuously, polling Gmail every minute for new emails.
- It processes emails with the subject "DATOS" and an attachment named `ppto_bicorps (1).csv`.
- The CSV data is cleaned, transformed, and inserted into the MySQL database in batches.
- Monitor the n8n execution logs for any errors or issues during processing.

### Contributing
Contributions are welcome! Please follow these steps:
1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/YourFeature`).
3. Commit your changes (`git commit -m 'Add YourFeature'`).
4. Push to the branch (`git push origin feature/YourFeature`).
5. Open a pull request.

### License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

### Contact
For questions or support, please contact [emercadogarcia@gmail.com](mailto:emercadogarcia@gmail.com) or open an issue in the repository.

---

# ETL-csv-mysql: Flujo de Trabajo Automatizado para Procesamiento de CSV por Correo y Carga a Base de Datos

## Versión en Español

### Descripción General
**ETL-csv-mysql** es un flujo de trabajo ETL (Extracción, Transformación y Carga) robusto y escalable desarrollado en [n8n](https://n8n.io/), diseñado para automatizar el procesamiento de archivos CSV recibidos por correo electrónico y su integración en una base de datos MySQL. Este flujo optimiza el manejo, validación y transformación de datos, siendo ideal para procesar información financiera y operativa en entornos corporativos.

El flujo se activa con correos entrantes de Gmail, filtra archivos CSV específicos, valida y transforma los datos, y los carga en una tabla de base de datos. Está optimizado para confiabilidad, manejo de errores y escalabilidad, procesando datos en lotes para garantizar un rendimiento eficiente.

### Características
- **Monitoreo Automático de Correos**: Revisa Gmail cada minuto en busca de nuevos correos con asuntos y adjuntos específicos.
- **Procesamiento Robusto de CSV**: Detecta delimitadores (`,` o `;`), normaliza saltos de línea y maneja BOM (Byte Order Mark) para una extracción consistente.
- **Validación y Limpieza de Datos**: Filtra correos por asunto y nombre de adjunto, valida contenido y normaliza datos financieros (por ejemplo, formato de moneda).
- **Procesamiento por Lotes**: Divide los datos en lotes de 100 registros para optimizar la inserción en la base de datos.
- **Integración con Base de Datos**: Mapea e inserta los datos limpios en la tabla `hechos_pptogastos` de MySQL con alineación precisa al esquema.
- **Manejo de Errores**: Incluye verificaciones para formatos de archivo inválidos y datos faltantes para garantizar la integridad de los datos.

### Estructura del Flujo de Trabajo
El flujo está compuesto por los siguientes nodos, conectados para formar un pipeline ETL completo:

1. **Disparador de Gmail**:
   - Monitorea Gmail cada minuto para detectar nuevos correos.
   - Descarga los adjuntos para su procesamiento.
   - Credenciales: Configurado con OAuth2 de Gmail para acceso seguro.

2. **Filtrar y Preparar Adjunto**:
   - Valida que el asunto del correo sea "DATOS".
   - Filtra el adjunto CSV específico (`ppto_bicorps (1).csv`).
   - Elimina BOM y rechaza archivos inválidos que contengan metadatos "Exif".

3. **Convertir Binario a Texto**:
   - Convierte el adjunto CSV de formato binario a texto codificado en UTF-8 para su procesamiento.

4. **Parsear CSV**:
   - Detecta el delimitador del CSV (`,` o `;`) según la fila de encabezados.
   - Normaliza saltos de línea y elimina líneas vacías.
   - Convierte el CSV en objetos JSON para la transformación posterior.

5. **Limpieza de Datos**:
   - Normaliza nombres de columnas a mayúsculas y elimina espacios.
   - Convierte montos financieros (por ejemplo, "-1.378,08") a un formato numérico estándar (por ejemplo, -1378.08).
   - Mapea los datos al esquema requerido por la base de datos.

6. **Dividir en Lotes**:
   - Divide los datos procesados en lotes de 100 registros para optimizar la inserción en la base de datos.

7. **Insertar Filas en Tabla**:
   - Inserta los datos limpios y transformados en la tabla `hechos_pptogastos` de MySQL.
   - Mapea los campos JSON a las columnas de la base de datos: `ejercicio`, `mes`, `monto`, `centro_contable`, `moneda`, `centro_de_costo`, `cuenta`.

### Esquema de la Base de Datos
El flujo apunta a la tabla `hechos_pptogastos` en una base de datos MySQL. La tabla incluye las siguientes columnas:

| Columna             | Tipo      | Requerido | Descripción                       |
|---------------------|-----------|-----------|-----------------------------------|
| `id`                | Número    | No        | ID autogenerado (excluido)       |
| `empresa`           | Texto     | No        | Identificador de empresa          |
| `centro_contable`   | Texto     | Sí        | Centro contable                  |
| `ejercicio`         | Número    | Sí        | Año fiscal                       |
| `mes`               | Número    | Sí        | Mes                              |
| `moneda`            | Texto     | Sí        | Moneda                           |
| `centro_de_costo`   | Texto     | Sí        | Centro de costo                  |
| `cuenta`            | Texto     | Sí        | Cuenta                           |
| `monto`             | Número    | Sí        | Monto                            |
| `created_at`        | Fecha/Hora| No        | Fecha de creación del registro    |
| `updated_at`        | Fecha/Hora| No        | Fecha de actualización del registro |

### Instrucciones de Configuración
1. **Clonar el Repositorio**:
   ```bash
   git clone git@github.com:emercadogarcia/ETL-csv-mysql.git
   ```

2. **Instalar n8n**:
   - Sigue la [guía de instalación de n8n](https://docs.n8n.io/getting-started/installation/) para configurar n8n localmente o en un servidor.
   - Asegúrate de tener una instancia de n8n activa.

3. **Importar el Flujo de Trabajo**:
   - Copia el archivo JSON del flujo (`etl-csv-mysql.json`) en el editor de flujos de n8n.
   - Alternativamente, importa el archivo JSON directamente desde la interfaz de n8n.

4. **Configurar Credenciales**:
   - **Gmail OAuth2**: Configura una cuenta de Gmail con credenciales OAuth2 en n8n para el nodo `Disparador de Gmail`.
   - **MySQL**: Configura las credenciales de MySQL con acceso a la tabla `hechos_pptogastos`.

5. **Activar el Flujo**:
   - Habilita el flujo en n8n para comenzar a monitorear Gmail y procesar datos.

### Requisitos Previos
- **n8n**: Versión compatible con el flujo proporcionado (probado con n8n 1.x).
- **Cuenta de Gmail**: Configurada con acceso OAuth2 para monitoreo de correos.
- **Base de Datos MySQL**: Con la tabla `hechos_pptogastos` creada.
- **Node.js**: Para instalaciones locales de n8n.
- **Git**: Para clonar el repositorio.

### Uso
- El flujo se ejecuta continuamente, revisando Gmail cada minuto en busca de nuevos correos.
- Procesa correos con el asunto "DATOS" y un adjunto llamado `ppto_bicorps (1).csv`.
- Los datos del CSV se limpian, transforman e insertan en la base de datos MySQL en lotes.
- Monitorea los registros de ejecución de n8n para detectar errores o problemas durante el procesamiento.

### Contribuciones
¡Las contribuciones son bienvenidas! Sigue estos pasos:
1. Haz un fork del repositorio.
2. Crea una rama para tu funcionalidad (`git checkout -b funcionalidad/TuFuncionalidad`).
3. Realiza tus cambios (`git commit -m 'Agregar TuFuncionalidad'`).
4. Sube la rama (`git push origin funcionalidad/TuFuncionalidad`).
5. Abre un pull request.

### Licencia
Este proyecto está licenciado bajo la Licencia MIT. Consulta el archivo [LICENSE](LICENSE) para más detalles.

### Contacto
Para preguntas o soporte, contáctame en [emercadogarcia@gmail.com](mailto:emercadogarcia@gmail.com) o abre un issue en el repositorio.