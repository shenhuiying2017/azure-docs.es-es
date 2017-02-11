## <a name="specifying-compression"></a>Especificación de la compresión
El procesamiento de grandes conjuntos de datos puede provocar cuellos de botella de E/S y red. Por lo tanto, los datos comprimidos en almacenes pueden no solo acelerar la transferencia de datos a través de la red y ahorrar espacio en disco, sino también introducir importantes mejoras de rendimiento en el procesamiento de macrodatos. En este momento, se admite la compresión para almacenes de datos basados en archivos como Blob de Azure o el sistema de archivos local.  

> [!NOTE]
> La configuración de la compresión no se admite para los datos con los formatos **AvroFormat**, **OrcFormat** o **ParquetFormat**. Al leer archivos que están en estos formatos, Data Factory detecta y usa el códec de compresión en los metadatos; al escribir en archivos que están en estos formatos, Data Factory elige el códec de compresión predeterminado para el formato concreto. Por ejemplo, ZLIB en el caso de OrcFormat y SNAPPY en el caso de ParquetFormat.
>
>

Para especificar la compresión para un conjunto de datos, use la propiedad **compression** del conjunto de datos JSON como en el ejemplo siguiente:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```
Supongamos que el conjunto de datos de ejemplo anterior se usa como salida de una actividad de copia; la actividad de copia comprime los datos de salida con el códec GZIP con una proporción óptima y, luego, escribe los datos comprimidos en un archivo denominado pagecounts.csv.gz en Azure Blob Storage.   

La sección **compression** tiene dos propiedades:  

* **Type**: el códec de compresión, que puede ser **GZIP**, **Deflate** o **BZIP2** o **ZipDeflate**.  
* **Level**: la relación de compresión, que puede ser **Optimal** o **Fastest**.

  * **Fastest:** la operación de compresión debe completarse tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima.
  * **Optimal:**la operación de compresión se debe comprimir óptimamente, incluso si tarda más tiempo en completarse.

    Para más información, consulte el tema [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

Cuando se especifica la propiedad `compression` en un JSON del conjunto de datos de entrada, la canalización puede leer los datos comprimidos del origen, mientras que cuando se especifica la propiedad en un conjunto del conjunto de datos de salida, la actividad de copia puede escribir datos comprimidos en el destino. Estos son algunos escenarios de ejemplo:

* Leer datos comprimidos con GZIP de un blob de Azure, descomprimirlos y escribir los datos de resultado en una base de datos SQL de Azure. Defina el conjunto de datos del blob de Azure de entrada con la propiedad de JSON `compression` `type` como GZIP.
* Leer datos de un archivo de texto sin formato del sistema de archivos local, comprimirlos con formato GZip y escribir los datos comprimidos en un blob de Azure. Defina el conjunto de datos del blob de Azure de salida con la propiedad de JSON `compression` `type` como GZip.
* Leer el archivo .zip del servidor FTP, descomprimirlo para obtener los archivos que contiene y colocar dichos archivos en Azure Data Lake Store. Defina el conjunto de datos del FTP de entrada con la propiedad de JSON `compression` `type` como ZipDeflate.
* Leer datos comprimidos con GZIP de un blob de Azure, descomprimirlos, comprimirlos con BZIP2 y escribir los datos de resultado en un blob de Azure. Defina el conjunto de datos de blob de Azure de entrada con `compression` `type` establecidos en GZIP y el conjunto de datos de salida con `compression` `type` establecido en BZIP2 en este caso.   


<!--HONumber=Feb17_HO2-->


