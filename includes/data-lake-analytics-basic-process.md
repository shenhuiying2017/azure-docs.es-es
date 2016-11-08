**Proceso básico de Análisis de Data Lake:**

![Diagrama de flujo de proceso de Análisis de Azure Data Lake](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)

1. Cree una cuenta de Análisis de Data Lake.
2. Prepare los datos de origen. Los trabajos de Análisis de Data Lake pueden leer datos desde cuentas del Almacén de Azure Data Lake o desde cuentas de almacenamiento de blobs de Azure.
3. Desarrolle un script U-SQL.
4. Envíe un trabajo (script U-SQL) a la cuenta de Análisis de Data Lake. El trabajo lee desde el origen de datos, procesa los datos como se indica en el script U-SQL y después guarda la salida en una cuenta de Almacén de Data Lake o de almacenamiento de blobs.

<!---HONumber=AcomDC_0921_2016-->