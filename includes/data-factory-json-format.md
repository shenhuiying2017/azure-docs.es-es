### Especificación de JsonFormat
Si se establece el formato en **JsonFormat**, puede especificar las siguientes propiedades **opcionales** en la sección **format**.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| filePattern |Indica el patrón de los datos almacenados en cada archivo JSON. Estos son los valores permitidos: **setOfObjects** y **arrayOfObjects**. El valor **predeterminado** es **setOfObjects**. Vea las secciones siguientes para obtener más información sobre estos patrones. |No |
| encodingName |Especifique el nombre de codificación. Para obtener la lista de nombres de codificación válidos, vea el artículo sobre la propiedad [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por ejemplo: windows-1250 o shift\_jis. El valor **predeterminado** es **UTF-8**. |No |
| nestingSeparator |Carácter que se usa para separar los niveles de anidamiento. El valor **predeterminado** es **. (punto)**. |No |

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

| Id | Name.First | Name.Middle | Name.Last | Etiquetas |
| --- | --- | --- | --- | --- |
| 1 |John |null |Doe |["Data Factory", "Azure"] |

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

* Cada objeto con una colección de pares nombre-valor se asignará a una fila de datos en formato de tabla. Es posible anidar objetos y puede definir cómo aplanar la estructura del conjunto de datos con el separador de anidado (.) de manera predeterminada. Consulte la sección [Ejemplo de JsonFormat](#jsonformat-example) de arriba para ver un ejemplo.  
* Si no se define la estructura en el conjunto de datos de Data Factory, la actividad de copia detecta el esquema del primer objeto y aplana el objeto en su conjunto. 
* Si la entrada JSON tiene una matriz, la actividad de copia convierte el valor de toda la matriz en una cadena. Puede optar por omitir este paso mediante el [filtrado o la asignación de columnas](#column-mapping-with-translator-rules).
* Si hay algún nombre duplicado en el mismo nivel, la actividad de copia elegirá el último.
* Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Dos propiedades con el mismo nombre, pero con distintas mayúsculas y minúsculas se considerarán propiedades independientes. 

<!---HONumber=AcomDC_0316_2016-->