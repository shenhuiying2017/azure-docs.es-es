## Especificación de formatos

### Especificación de TextFormat

Si se establece el formato en **TextFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **format**.

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | El carácter utilizado para separar las columnas en un archivo. | Solo se permite un carácter. El valor predeterminado es coma (','). | No |
| rowDelimiter | El carácter usado para separar las filas en un archivo. | Solo se permite un carácter. El valor predeterminado es cualquiera de los siguientes en lectura: ["\\r\\n", "\\r", "\\n"] y "\\r\\n" en escritura. | No |
| escapeChar | El carácter especial que se usa para anular un delimitador de columna en el contenido del archivo de entrada. <br/><br/>No se puede especificar escapeChar y quoteChar para una tabla. | Solo se permite un carácter. No hay ningún valor predeterminado. <br/><br/>Ejemplo: si tiene la coma (',') como el delimitador de columna, pero quiere tener el carácter de coma en el texto (ejemplo: "Hello, world"), puede definir '$' como carácter de escape y usar la cadena "Hello$, world" en el origen. | No | 
| quoteChar | El carácter usado para poner entre comillas un valor de cadena. Los delimitadores de columna y fila entre comillas se tratarán como parte del valor de la cadena. Esta propiedad se aplica a conjuntos de datos de entrada y salida.<br/><br/>No se puede especificar escapeChar y quoteChar para una tabla. | Solo se permite un carácter. No hay ningún valor predeterminado. <br/><br/>Por ejemplo, si tiene la coma (',') como delimitador de columna, pero quiere tener el carácter de coma en el texto (por ejemplo: <Hello, world>), puede definir " (comillas dobles) como comillas y usar la cadena "Hello, world" en el origen. | No |
| nullValue | Uno o más caracteres que se usan para representar un valor nulo. | Uno o más caracteres. Los valores predeterminados son "\\N" y "NULL" en lectura y "\\N" en escritura. | No |
| encodingName | Especifique el nombre de codificación. | Un nombre de codificación válido. Consulte la [propiedad Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por ejemplo: windows-1250 o shift\_jis. El valor predeterminado es UTF-8. | No | 
| firstRowAsHeader | Especifica si se tendrá en cuenta la primera fila como encabezado. Para un conjunto de datos de entrada, Data Factory lee la primera fila como encabezado. Para un conjunto de datos de salida, Data Factory escribe la primera fila como encabezado. <br/><br/>Consulte [Escenarios de uso de **firstRowAsHeader** y **skipLineCount**](#scenarios-for-using-firstrowasheader-and-skiplinecount) para ver casos de ejemplo. | True<br/>False (valor predeterminado) | No |
| skipLineCount | Indica el número de filas que se omitirán al leer datos de archivos de entrada. Si se especifican skipLineCount y firstRowAsHeader, las líneas se omiten primero y luego la información del encabezado se lee del archivo de entrada. <br/><br/>Consulte [Escenarios de uso de firstRowAsHeader y skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) para ver casos de ejemplo. | Entero | No | 
| treatEmptyAsNull | Especifica si las cadenas null o vacías se tratarán como valores null al leer datos de un archivo de entrada. | True (predeterminado)<br/>False | No |  

#### Ejemplo de TextFormat
En el ejemplo siguiente se muestran algunas de las propiedades de formato de TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
			"firstRowAsHeader": true,
			"skipLineCount": 0,
			"treatEmptyAsNull": true
	    }
	},

Para usar escapeChar, en lugar de quoteChar, reemplace la línea con quoteChar por la siguiente escapeChar:

	"escapeChar": "$",



### Escenarios de uso de firstRowAsHeader y skipLineCount

- Va a copiar de un origen que no es archivo a un archivo de texto y quiere agregar una línea de encabezado que contenga los metadatos de esquema (por ejemplo, esquema SQL). Especifique **firstRowAsHeader** como true en el conjunto de datos de salida para este escenario.
- Va a copiar de un archivo de texto que contiene una línea de encabezado a un receptor que no es archivo y quiere eliminar esa línea. Especifique **firstRowAsHeader** como true en el conjunto de datos de entrada.
- Va a copiar de un archivo de texto y quiere omitir unas cuantas líneas al comienzo que no contienen datos ni información de encabezado. Especifique **skipLineCount** para indicar el número de líneas que se omitirá. Si el resto del archivo contiene una línea de encabezado, también puede especificar **firstRowAsHeader**. Si se especifican tanto **skipLineCount** como **firstRowAsHeader**, primero se omiten las líneas y luego se lee la información de encabezado del archivo de entrada.

### Especificación de AvroFormat
Si se establece el formato en AvroFormat, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "AvroFormat",
	}

Para usar el formato Avro en una tabla de Hive, puede consultar [Tutorial de Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

### Especificación de JsonFormat

Si se establece el formato en **JsonFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **Format**.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| filePattern | Indica el patrón de los datos almacenados en cada archivo JSON. Estos son los valores permitidos: **setOfObjects** y **arrayOfObjects**. El valor **predeterminado** es **setOfObjects**. Consulte las siguientes secciones para más información sobre estos patrones.| No |
| encodingName | Especifique el nombre de codificación. Para obtener la lista de nombres de codificación válidos, consulte la [propiedad Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por ejemplo: windows-1250 o shift\_jis. El valor **predeterminado** es **UTF-8**. | No | 
| nestingSeparator | Carácter que se usa para separar los niveles de anidamiento. El valor predeterminado es '.' (punto). | No | 


#### Patrón de archivos setOfObjects

Cada archivo contiene un único objeto o bien varios objetos concatenados/delimitados por líneas. Si se elige esta opción en un conjunto de datos de salida, la actividad de copia genera un único archivo JSON con cada objeto por línea (delimitado por líneas).

**Objeto único**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}

**JSON delimitado por líneas**

	{"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
	{"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
	{"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenado**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}
	{
		"time": "2015-04-29T07:13:21.0220000Z",
		"callingimsi": "466922202613463",
		"callingnum1": "123436380",
		"callingnum2": "789037573",
		"switch1": "US",
		"switch2": "UK"
	}
	{
		"time": "2015-04-29T07:13:21.4370000Z",
		"callingimsi": "466923101048691",
		"callingnum1": "678901578",
		"callingnum2": "345626404",
		"switch1": "Germany",
		"switch2": "UK"
	}


#### Patrón de archivos arrayOfObjects 

Cada archivo contiene una matriz de objetos.

	[
	    {
	        "time": "2015-04-29T07:12:20.9100000Z",
	        "callingimsi": "466920403025604",
	        "callingnum1": "678948008",
	        "callingnum2": "567834760",
	        "switch1": "China",
	        "switch2": "Germany"
	    },
	    {
	        "time": "2015-04-29T07:13:21.0220000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789037573",
	        "switch1": "US",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:21.4370000Z",
	        "callingimsi": "466923101048691",
	        "callingnum1": "678901578",
	        "callingnum2": "345626404",
	        "switch1": "Germany",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "789037573",
	        "callingnum2": "789044691",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789044691",
	        "switch1": "US",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:24.2120000Z",
	        "callingimsi": "466922201102759",
	        "callingnum1": "345698602",
	        "callingnum2": "789097900",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:25.6320000Z",
	        "callingimsi": "466923300236137",
	        "callingnum1": "567850552",
	        "callingnum2": "789086133",
	        "switch1": "China",
	        "switch2": "Germany"
	    }
	]

### Ejemplo de JsonFormat

Si tiene un archivo JSON con el siguiente contenido:

	{
		"Id": 1,
		"Name": {
			"First": "John",
			"Last": "Doe"
		},
		"Tags": ["Data Factory”, "Azure"]
	}

y quiere copiarlo en una tabla de SQL de Azure con el formato siguiente:

Id | Name.First | Name.Middle | Name.Last | Etiquetas
--- | ---------- | ----------- | --------- | ----
1 | John | null | Doe | ["Data Factory", "Azure"]

El conjunto de datos de entrada con el tipo JsonFormat se define de la siguiente manera: (definición parcial en la que solo se ilustran las secciones relevantes).

	"properties": {
		"structure": [
			{"name": "Id", "type": "Int"},
			{"name": "Name.First", "type": "String"},
			{"name": "Name.Middle", "type": "String"},
			{"name": "Name.Last", "type": "String"},
			{"name": "Tags", "type": "string"}
		],
		"typeProperties":
		{
			"folderPath": "mycontainer/myfolder",
			"format":
			{
				"type": "JsonFormat",
				"filePattern": "setOfObjects",
				"encodingName": "UTF-8",
				"nestingSeparator": "."
			}
		}
	}

Si no se define la estructura, la actividad de copia aplana la estructura de manera predeterminada y copia todos los elementos.

#### Estructura JSON admitida
Tenga en cuenta los siguientes puntos:

- Cada objeto con una colección de pares nombre-valor se asigna a una fila de datos en formato de tabla. Es posible anidar objetos y puede definir cómo aplanar la estructura del conjunto de datos con el separador de anidado (.) de manera predeterminada. Consulte la sección anterior de [ejemplo de JsonFormat](#jsonformat-example).
- Si no se define la estructura en el conjunto de datos de Data Factory, la actividad de copia detecta el esquema del primer objeto y aplana el objeto en su conjunto.
- Si la entrada JSON tiene una matriz, la actividad de copia convierte el valor de toda la matriz en una cadena. Puede optar por omitir este paso mediante el [filtrado o la asignación de columnas](#column-mapping-with-translator-rules).
- Si hay algún nombre duplicado en el mismo nivel, la actividad de copia elige el último.
- Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Dos propiedades con el mismo nombre, pero con distintas mayúsculas y minúsculas se consideran propiedades independientes.

### Especificación de OrcFormat
Si se establece el formato en OrcFormat, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "OrcFormat"
	}

> [AZURE.IMPORTANT] Si no va a copiar archivos ORC **como están** entre almacenes de datos locales y en la nube, debe instalar JRE 8 (Java Runtime Environment) en la máquina de puerta de enlace. Una puerta de enlace de 64 bits requiere JRE de 64 bits y una de 32 bits, JRE de 32 bits. Puede encontrar las dos versiones [aquí](http://go.microsoft.com/fwlink/?LinkId=808605). Elija la más adecuada.

Tenga en cuenta los siguientes puntos:

-	No se admiten tipos de daros complejos (STRUCT, MAP, LIST, UNION).
-	El archivo ORC tiene tres [opciones relacionadas con la compresión](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB y SNAPPY. Data Factory admite la lectura de datos del archivo ORC en cualquiera de los formatos comprimidos. Se utiliza el códec de compresión en los metadatos para leer los datos. Sin embargo, al escribir en un archivo ORC, Data Factory elige ZLIB que es el valor predeterminado para ORC. Por el momento, no hay ninguna opción para invalidar este comportamiento.

### Especificación de ParquetFormat
Si se establece el formato en ParquetFormat, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "ParquetFormat"
	}

> [AZURE.IMPORTANT] Si no va a copiar archivos Parquet **como están** entre almacenes de datos locales y en la nube, debe instalar JRE 8 (Java Runtime Environment) en la máquina de puerta de enlace. Una puerta de enlace de 64 bits requiere JRE de 64 bits y una de 32 bits, JRE de 32 bits. Puede encontrar las dos versiones [aquí](http://go.microsoft.com/fwlink/?LinkId=808605). Elija la más adecuada.

Tenga en cuenta los siguientes puntos:

-	No se admiten tipos de daros complejos (MAP, LIST).
-	El archivo Parquet tiene las siguientes opciones relacionadas con la compresión: NONE, SNAPPY, GZIP y LZO. Data Factory admite la lectura de datos del archivo ORC en cualquiera de los formatos comprimidos. Se utiliza el códec de compresión en los metadatos para leer los datos. Sin embargo, al escribir en un archivo Parquet, Data Factory elige SNAPPY que es el valor predeterminado para Parquet. Por el momento, no hay ninguna opción para invalidar este comportamiento.

<!---HONumber=AcomDC_0928_2016-->