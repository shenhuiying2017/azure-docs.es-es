### Especificación de TextFormat

Si se establece el formato en **TextFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **format**.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| columnDelimiter | El carácter que se usa como separador de columnas en un archivo. Actualmente solo se permite un carácter. Esta etiqueta es opcional. El valor predeterminado es coma (,). | No |
| rowDelimiter | El carácter que se usa como separador de filas en un archivo. Actualmente solo se permite un carácter. Esta etiqueta es opcional. El valor predeterminado es cualquiera de los siguientes: ["\\r\\n", "\\r", "\\n"]. | No |
| escapeChar | El carácter especial que se usa para anular el delimitador de columna que se muestra en el contenido. Esta etiqueta es opcional. No hay ningún valor predeterminado. No debe especificar más de un carácter para esta propiedad.<br/><br/>Por ejemplo, si utiliza la coma (,) como delimitador de columna, pero quiere usar el carácter de coma en el texto (por ejemplo: "Hello, world"), puede definir ‘$’ como carácter de escape y usar la cadena "Hello$, world" en el origen.<br/><br/>Tenga en cuenta que no se pueden especificar escapeChar y quoteChar a la vez para una tabla. | No | 
| quoteChar | El carácter especial que se usa para poner entre comillas el valor de la cadena. Los delimitadores de columna y fila entre comillas se tratarán como parte del valor de la cadena. Esta etiqueta es opcional. No hay ningún valor predeterminado. No debe especificar más de un carácter para esta propiedad.<br/><br/>Por ejemplo, si tiene la coma (,) como delimitador de columna, pero desea usar el carácter de coma en el texto (por ejemplo: <Hello  world>), puede definir ‘"’ como comillas y usar la cadena <"Hello, world"> en el origen. Esta propiedad se aplica a las tablas de entrada y de salida.<br/><br/>Tenga en cuenta que no se pueden especificar escapeChar y quoteChar a la vez para una tabla. | No |
| nullValue | Los caracteres que se usan para representar un valor nulo en el contenido del archivo de blob. Esta etiqueta es opcional. El valor predeterminado es "\\N".<br/><br/>Según el ejemplo anterior, "NaN" en el blob se traducirá como valor NULL al copiarse en SQL Server, por ejemplo. | No |
| encodingName | Especifique el nombre de codificación. Para obtener la lista de nombres de codificación válidos, vea: Propiedad [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por ejemplo: windows-1250 o shift\_jis. El valor predeterminado es: UTF-8. | No | 

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
	    }
	},

Para usar escapeChar, en lugar de quoteChar, reemplace la línea con quoteChar por la siguiente:

	"escapeChar": "$",

### Especificación de AvroFormat
Si se establece el formato en AvroFormat, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "AvroFormat",
	}

Para usar el formato Avro en una tabla de Hive, puede consultar [Tutorial de Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

### Especificación de JsonFormat

Si se establece el formato en **JsonFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **format**.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| filePattern | Indica el patrón de los datos almacenados en cada archivo JSON. Estos son los valores permitidos: **setOfObjects** y **arrayOfObjects**. El valor **predeterminado** es **setOfObjects**. Vea las secciones siguientes para obtener más información sobre estos patrones.| No |
| encodingName | Especifique el nombre de codificación. Para obtener la lista de nombres de codificación válidos, vea: Propiedad [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por ejemplo: windows-1250 o shift\_jis. El valor **predeterminado** es: **UTF-8**. | No | 
| nestingSeparator | Carácter que se usa para separar los niveles de anidamiento. El valor **predeterminado** es **. (punto)**. | No | 


#### Patrón de archivos setOfObjects

Cada archivo contiene un único objeto o bien varios objetos concatenados/delimitados por líneas. Si se elige esta opción en un conjunto de datos de salida, la copia generará un único archivo JSON con cada objeto por línea (delimitado por líneas).

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
Tenga en cuenta lo siguiente:

- Cada objeto con una colección de pares nombre-valor se asignará a una fila de datos en formato de tabla. Es posible anidar objetos y puede definir cómo aplanar la estructura del conjunto de datos con el separador de anidado (.) de manera predeterminada. Consulte la sección [Ejemplo de JsonFormat](#jsonformat-example) anterior para ver un ejemplo.  
- Si no se define la estructura en el conjunto de datos de Data Factory, la actividad de copia detecta el esquema del primer objeto y aplana el objeto en su conjunto. 
- Si la entrada JSON tiene una matriz, la actividad de copia convierte el valor de toda la matriz en una cadena. Puede optar por omitir este paso mediante el [filtrado o la asignación de columnas](#column-mapping-with-translator-rules).
- Si hay algún nombre duplicado en el mismo nivel, la actividad de copia elegirá el último.
- Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Dos propiedades con el mismo nombre, pero con distintas mayúsculas y minúsculas se considerarán propiedades independientes. 

### Especificación de OrcFormat
Si se establece el formato en OrcFormat, no es preciso especificar propiedades en la sección Format de la sección typeProperties. Ejemplo:

	"format":
	{
	    "type": "OrcFormat",
	}

Tenga en cuenta lo siguiente:
 
-	Si va a copiar datos entre almacenes de datos locales y en la nube con formato ORC, y no copia los archivos ORC tal y como están del origen al receptor, deberá instalar JRE (Java Runtime Environment) en el equipo de puerta de enlace. 
-	No se admiten tipos de daros complejos (STRUCT, MAP, LIST, UNION).
-	El archivo ORC tiene tres [opciones relacionadas con la compresión](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory admite la lectura de datos del archivo ORC en cualquiera de los formatos comprimidos mencionados anteriormente. Se utiliza el códec de compresión en los metadatos para leer los datos. Sin embargo, al escribir en un archivo ORC, Data Factory elige ZLIB que es el valor predeterminado para ORC. Por el momento, no hay ninguna opción para invalidar este comportamiento. 

<!---HONumber=AcomDC_0525_2016-->