---
title: "Guía de programación de U-SQL para Azure Data Lake | Microsoft Docs"
description: "Conozca más información acerca del conjunto de servicios de Azure Data Lake que le permiten crear una plataforma de macrodatos basada en la nube."
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: ca9937d4c0d87827f8b78ecc495fdad791d139e8
ms.lasthandoff: 03/23/2017


---

# <a name="u-sql-programmability-guide"></a>Guía de programación de U-SQL
## <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake incluye funcionalidades que facilitan a los desarrolladores, científicos de datos y analistas el almacenamiento de datos de cualquier tamaño, forma y velocidad. También permite a los desarrolladores usar muchos tipos de procesamiento y análisis entre plataformas y lenguajes. Elimina las complejidades de ingestión y almacenamiento de todos los datos acelerando el uso de análisis de lotes, streaming e interactivos.

Azure Data Lake es un conjunto de servicios que funcionan conjuntamente para proporcionar una plataforma de macrodatos basada en la nube. Estos servicios incluyen:

- HDInsight de Azure
- Almacén de Azure Data Lake
- Análisis con Azure Data Lake

U-SQL es un lenguaje de consulta diseñado para tipo de macrodatos de cargas de trabajo. Una de las características exclusivas de U-SQL es la combinación del lenguaje declarativo similar a SQL con la extensibilidad y capacidad de programación que proporciona C#. También proporciona la capacidad de acceder a metadatos de esquema y manipularlos, y de crear componentes personalizados, como procesadores de datos y reductores.

En esta guía, nos centramos en la extensibilidad y capacidad de programación del lenguaje U-SQL que proporciona C#.

## <a name="requirements"></a>Requisitos
Para comenzar con el desarrollo de Azure Data Lake, debe descargar e instalar [Herramientas de Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Introducción a U-SQL  
La descripción del lenguaje U-SQL está fuera del ámbito de este documento. Sin embargo, se describen unas construcciones U-SQL básicas para introducir gradualmente características de programación de U-SQL. Para más información, consulte la guía de [referencia del lenguaje U-SQL](http://aka.ms/usql_reference).

Empecemos con el ejemplo siguiente:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

En este ejemplo, tenemos un **archivo de entrada** (input_file.tsv) definido por la **variable Local** @input_file.

El script U-SQL que aparece en este ejemplo realiza las siguientes acciones:

* La instrucción **EXTRACT** inicial carga datos en memoria mediante la conversión del archivo de entrada en el **conjunto de filas de memoria**.
* **SELECT** opera en conjuntos de filas de datos para agregar datos y prepararlos para la exportación.
* **OUTPUT** exporta el conjunto de filas de datos al **archivo de salida**, que es un archivo externo.

En primer lugar, echemos un vistazo a algunas opciones para utilizar una expresión de C# directamente en un script U-SQL.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Expresiones y tipos de C# en un script U-SQL
Una expresión de C# de U-SQL, al igual que una expresión de C# en general, es una secuencia de uno o más operadores que se pueden evaluar como un valor, objeto, método o espacio de nombres único. Las expresiones pueden constar de un valor literal, una invocación de método, un operador o un nombre simple. Los nombres simples pueden ser el nombre de una variable, miembro de tipo, parámetro de método, espacio de nombres o tipo.

Cuando hablamos de expresiones de C# de U-SQL, nos referimos específicamente a expresiones de C# de script base U-SQL. La sección de código subyacente de C#, que se describe más adelante en este documento, también puede contener expresiones de C# como elementos basados en código de C#.

Las expresiones pueden usar operadores que a su vez usen otras expresiones como parámetros. También pueden utilizar llamadas a métodos con parámetros que constituyen otras llamadas a métodos. Los siguientes son ejemplos de una expresión:  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

El lenguaje U-SQL permite el uso de expresiones estándar de C# a partir de espacios de nombres integrados.  

```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

Las expresiones de C# generales se pueden usar en SELECT y EXTRACT de U-SQL.

Las expresiones de C# también pueden utilizarse en instrucciones DECLARE o IF. A continuación aparece un ejemplo de este tipo de expresión:   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

Ejemplo de script basado en U-SQL:  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

Las expresiones de C# pueden proporcionar una funcionalidad ampliada al manipular columnas como parte de un conjunto de filas. Por ejemplo, si va a convertir una columna de fecha y hora a la fecha con cero horas, puede utilizar la siguiente parte SELECT de script basado en U-SQL:

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Como puede observar, se usa el método `System.Convert.ToDateTime` para ejecutarse a través de la conversión.

A continuación aparece un escenario algo más complicado que muestra el uso de algunos operadores de C# básicos:

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Esto muestra un ejemplo de una expresión del operador condicional de C#.

Los ejemplos anteriores muestran el uso de expresiones de C# en el script base U-SQL. Sin embargo, U-SQL habilita más características de programación extensibles que se tratan más adelante en este documento.  

Script completo:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Uso de expresiones de C# para conversiones de tipo de datos
En el ejemplo siguiente se muestra cómo se puede hacer una conversión de datos de fecha y hora mediante el uso de expresiones de C#. En este escenario en particular, los datos de fecha y hora de cadena se convierten en la fecha y hora estándar con la notación de hora de medianoche 00:00:00.

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Uso de expresiones de C# para la fecha de hoy
Para extraer la fecha de hoy, podemos usar la siguiente expresión de C#:

```c#
DateTime.Now.ToString("M/d/yyyy")
```

Este es un ejemplo de cómo utilizar esta expresión en un script:

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

## <a name="use-inline-c-function-expressions"></a>Uso de expresiones de función insertada de C#
U-SQL permite el uso de definición de expresiones de función insertada como parte de expresiones de C#. Esto crea más posibilidades de utilizar funciones de C# con parámetros de referencia de salida.

El siguiente es un ejemplo de definición de una expresión de función insertada general:

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

Ejemplo:

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

En este ejemplo, definimos una función insertada con el parámetro de entrada de cadena input_p. Dentro de esta función, se comprueba si la cadena de entrada es un valor de fecha y hora válido. Si es así, se devuelve; de lo contrario se devuelve null.

La función insertada se necesita en este escenario, puesto que la función DateTime.TryParse contiene el parámetro de salida `out dt_result`. Lo definimos como `DateTime dt_result;`.


## <a name="verify-data-type-values"></a>Comprobación de valores de tipo de datos
Algunas funciones de C# no se pueden usar directamente en el script base U-SQL como expresiones de C#. En concreto, las funciones que no se pueden usar directamente son aquellas que requieren un parámetro de referencia de salida. Sin embargo, estas funciones pueden definirse y utilizarse como parte de la expresión de función insertada como hemos hablado anteriormente.

### <a name="use-inline-function-expressions"></a>Uso de expresiones de función insertada
Para comprobar si el valor de DateTime es válido, podemos usar `DateTime.TryParse`.

Este es un ejemplo completo que muestra cómo utilizar una expresión de función insertada para comprobar el valor de tipo de datos con `DateTime.TryParse`.

En este escenario, comprobamos el valor de tipo de datos de DateTime:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-code-behind"></a>Uso de código subyacente
Para usar la misma funcionalidad en la sección de código subyacente del programa U-SQL, definimos la función ToDateTime de C#.

Esta es la sección del script U-SQL básico en el que hemos realizado los cambios necesarios:

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

Aquí se muestra la función de código subyacente:

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="use-code-behind"></a>Uso de código subyacente
El código subyacente es una sección de programación de C# del proyecto U-SQL. Conceptualmente, el código subyacente es un ensamblado compilado (DLL) al que se hace referencia en el script U-SQL. Visual Studio Tools le permite administrar y depurar una sección de programación de C# como parte del proyecto de U-SQL.

Cuando se crea un proyecto típico de U-SQL en Visual Studio, hay dos partes del proyecto: script básico y un archivo con extensión **.usql**.

![Archivo de script básico](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


Proyecto de solución típica:   
![Proyecto de solución típica](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


En la segunda parte del proyecto llamamos a un archivo de código subyacente: Script.usql.cs.  
![Código subyacente](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

Este archivo contiene una definición de espacio de nombres predeterminada para los objetos de programación.

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

La plantilla de código subyacente de vista previa se genera automáticamente. Este archivo contiene una definición de espacio de nombres predeterminada para los objetos de programación. Durante la ejecución del proyecto, se compila y se hace referencia a él en el script U-SQL.

Para empezar a desarrollar objetos de programación, es necesario crear una clase **public**.

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

Los objetos de programación pueden ser funciones definidas por el usuario o UDF, tipos definidos por el usuario o UDT, PROCESS, REDUCER, etc.

## <a name="register-u-sql-assemblies"></a>Registro de ensamblados de U-SQL
El modelo de extensibilidad de U-SQL depende en gran medida de la capacidad de agregar código personalizado. Actualmente, U-SQL le proporciona formas sencillas de agregar su propio código basado en Microsoft .NET (en particular, C#). Sin embargo, también puede agregar código personalizado escrito en otros lenguajes .NET como, por ejemplo, F# o VB.NET.

Puede incluso implementar su propio tiempo de ejecución para otros lenguajes, aunque todavía necesita proporcionar la interoperabilidad a través de una capa .NET. Si desea que admitamos un lenguaje específico, archive una solicitud de característica o deje un comentario en http://aka.ms/adlfeedback.

### <a name="learn-the-difference-between-code-behind-and-assembly-registration-through-azure-data-lake-tools-in-visual-studio"></a>Diferencias entre el código subyacente y el registro de ensamblado a través de herramientas de Azure Data Lake en Visual Studio
La manera más fácil de usar el código personalizado es utilizar las funcionalidades de código subyacente de Herramientas de Azure Data Lake para Visual Studio.

Como mencionamos anteriormente, rellene el código personalizado para el script (por ejemplo, Script.usql) en su archivo de código subyacente (por ejemplo, Script.usql.cs).

![Ejemplo de código subyacente](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**Figura 1**: ejemplo de código subyacente de Herramientas de Azure Data Lake en Visual Studio. (Haga clic en la imagen para ampliarla. Hay [código de ejemplo](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis) disponible).


La ventaja del código subyacente es que las herramientas se hacen cargo de los pasos siguientes para cuando se envía el script:  

1. Compila el ensamblado para el archivo de código subyacente.  

2. Agrega un prólogo para el script que usa la instrucción [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) para registrar el archivo de ensamblado. También utiliza [ENSAMBLADO DE REFERENCIA] (https://msdn.microsoft.com/library/azure/mt763294.aspx) para cargar el ensamblado en el contexto del script.

3. Agrega un epílogo al script que usa [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) para quitar el ensamblado registrado temporalmente de nuevo.

Puede ver el prólogo y el epílogo generados al abrir el script:

![Prólogo generado](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**Ilustración 2**: prólogo y epílogo generados automáticamente para código subyacente
<br />

Algunas de las desventajas del código subyacente son las siguientes:

* El código se carga para cada envío de script.
* La funcionalidad no se puede compartir con otros.

Por lo tanto, puede agregar una biblioteca de clases de C# independiente (para U-SQL) a la solución (consulte la figura 3), desarrollar el código o copiar el código subyacente ya existente (sin cambios en el código de C# requerido, tal y como se muestra en la figura 4). A continuación, use la opción de menú **Registrar ensamblado** en el proyecto para registrar el ensamblado (como se muestra en el paso 1 de la figura 5).

![Crear proyecto](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**Figura 3**: Creación de un proyecto de código de C# de U-SQL  
<br />

![Biblioteca de clases](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**Figura 4**: Biblioteca de clases de C# de U-SQL junto al archivo de código subyacente
<br />

![Registrar código](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**Figura 5**: Registro del proyecto de código de C# de U-SQL
<br />

El cuadro de diálogo de registro (consulte el paso 2 de la figura 5) le proporciona las opciones sobre cómo registrar el ensamblado (por ejemplo, qué cuenta de Data Lake Analytics desea usar o qué base de datos). También le proporciona información acerca de cómo asignar un nombre al ensamblado. (La herramienta rellena la ruta de acceso del ensamblado local). También proporciona una opción para volver a registrar un ensamblado ya registrado y ofrece dos opciones para agregar dependencias adicionales:

**Dependencias administradas**: muestra los ensamblados administrados que son necesarios. Cada ensamblado seleccionado se registra por separado y se convierte en referenciable en scripts. Se usa para otros ensamblados .NET.

**Archivos adicionales**: le permite agregar archivos de recursos adicionales que son necesarios para el ensamblado. Se registran junto con el ensamblado y se cargan automáticamente cuando se hace referencia al ensamblado. Utilice esto para los archivos de configuración, los ensamblados nativos u otros lenguajes en tiempo de ejecución, sus recursos, etc.

Ambas opciones se usan en los ejemplos siguientes. La [entrada de blog reciente sobre el procesamiento de imágenes](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/) es otro ejemplo que muestra el uso de un ensamblado predefinido que puede usar estas opciones para el registro.

Ahora puede hacer referencia a los ensamblados registrados de cualquier script U-SQL que tenga permisos para la base de datos de los ensamblados registrados. (Para más información, consulte el código del script U-SQL en la figura 4). Tendrá que agregar una referencia para cada ensamblado registrado por separado. Los archivos de recursos adicionales se implementarán automáticamente. Ese script no debe tener nunca más un archivo de código subyacente para el código que está en los ensamblados de referencia, pero el archivo de código subyacente todavía puede proporcionar otro código.

### <a name="register-assemblies-via-azure-data-lake-tools-in-visual-studio-and-in-u-sql-scripts"></a>Registro de ensamblados mediante herramientas de Azure Data Lake en Visual Studio y en scripts U-SQL
Aunque las herramientas de Azure Data Lake en Visual Studio facilitan el registro de un ensamblado, también puede hacerlo con un script (de la misma forma que las herramientas lo hacen por usted) si está desarrollando, por ejemplo, en una plataforma diferente y ya ha compilado los ensamblados que desea cargar y registrar. Siga estos pasos:

1. Cargue el DLL del ensamblado y todos los archivos DLL necesarios que no sean del sistema y los archivos de recurso en una ubicación que elija. También puede cargarla en la cuenta de almacenamiento de Azure Data Lake o incluso en una cuenta de Microsoft Azure Blob Storage que esté vinculada a su cuenta de Azure Data Lake. Puede utilizar cualquiera de las numerosas herramientas de carga disponibles (por ejemplo, comandos de Windows Powershell, carga del explorador de Data Lake de las herramientas de Azure Data Lake de Visual Studio, comando de carga del SDK favorito o a través de Azure Portal).

1. Una vez que ha cargado los archivos DLL, utilice las instrucciones [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) para registrarlos.

Usamos este enfoque en el siguiente ejemplo espacial.

### <a name="register-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>Registro de ensamblados que usan otros ensamblados .NET (basados en la biblioteca de ejemplos JSON y XML)
Nuestro [sitio de GitHub de U-SQL](https://github.com/Azure/usql/) ofrece un conjunto de ensamblados de ejemplo compartidos que puede usar. Uno de los ensamblados denominado [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats) proporciona extractores, funciones y outputters para controlar los documentos XML y JSON. El ensamblado de Microsoft.Analytics.Samples.Formats depende de dos ensamblados específicos de dominio existentes para realizar el procesamiento de JSON y XML respectivamente. Usa la biblioteca [Newtonsoft Json.Net](http://www.newtonsoft.com/) para el procesamiento de documentos JSON y el ensamblado [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx) para procesar XML. Se usa para mostrar cómo se registran y utilizan los ensamblados de nuestros scripts.

En primer lugar descargamos el [proyecto de Visual Studio](https://github.com/Azure/usql/tree/master/Examples/DataFormats) para nuestro entorno de desarrollo local (por ejemplo, con la realización de una copia local con la herramienta de GitHub para Windows). Después abrimos la solución en Visual Studio, hacemos clic con el botón derecho en el proyecto como se explicó anteriormente para registrar el ensamblado.

Aunque este ensamblado tiene dos dependencias, solo tenemos que incluir la dependencia Newtonsoft porque System.Xml ya está disponible en Azure Data Lake (sin embargo, se debe hacer referencia explícita a ella). En la figura 6 se muestra cómo se asigna nombre al ensamblado (tenga en cuenta que puede elegir un nombre diferente sin puntos también) y se agrega el archivo DLL de Newtonsoft. Cada uno de los dos ensamblados ahora se registrará individualmente en la base de datos especificada (por ejemplo, JSONBlog).

![Registro del ensamblado](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**Ilustración 6**: cómo registrar el ensamblado Microsoft.Analytics.Samples.Formats desde Visual Studio
<br />

Si usted u otros usuarios, con los que comparte los ensamblados registrados concediéndoles acceso de lectura a la base de datos, ahora desean utilizar la funcionalidad JSON en sus scripts, agregue las siguientes dos referencias al script:

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Y si desea usar la funcionalidad XML, agregue una referencia de ensamblado de sistema y una referencia al ensamblado registrado:

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Para más información sobre cómo usar la funcionalidad JSON, consulte [esta entrada de blog](https://blogs.msdn.microsoft.com/mrys/?p=755).

### <a name="register-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>Registro de ensamblados que utilizan ensamblados nativos de C++ (con el ensamblado de tipo espacial de SQL Server 2016 del Feature Pack)
Ahora veamos un escenario ligeramente diferente. Supongamos que el ensamblado que se va a utilizar tiene una dependencia en el código que no se basa en .NET. Más concretamente, el ensamblado tiene una dependencia en un ensamblado de C++ nativo. Un ejemplo de este tipo de ensamblado es el ensamblado del tipo de SQL Server [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676). Proporciona implementaciones basadas en .NET de hierarchyID, la geometría y los tipos de geografía de SQL Server que van a utilizar las aplicaciones del lado del cliente de SQL Server para controlar los tipos. (Originalmente, también era el ensamblado que proporcionaba la implementación de los tipos espaciales de SQL Server antes del lanzamiento de SQL Server 2016).

Echemos un vistazo a cómo registrar este ensamblado en U-SQL.

En primer lugar, se descarga e instala el ensamblado desde el [Feature Pack de SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=52676). Seleccione la versión de 64 bits del instalador (ENU\x64\SQLSysClrTypes.msi), para asegurarnos de que tenemos la versión de 64 bits de las bibliotecas.

El instalador instala el ensamblado administrado Microsoft.SqlServer.Types.dll en C:\Archivos de programa (x86) \Microsoft SQL Server\130\SDK\Assemblies y el ensamblado nativo SqlServerSpatial130.dll en \Windows\System32\. Ahora se cargan los ensamblados en el almacén de Azure Data Lake Store (por ejemplo, en una carpeta denominada /upload/asm/spatial).

Puesto que el instalador ha instalado la biblioteca nativa en la carpeta del sistema C:\Windows\System32, nos tenemos que asegurar de que copiamos el archivo SqlServerSpatial130.dll fuera de esa carpeta antes de cargarlo, o de que la herramienta que usamos no lleva a cabo la [redirección del sistema de archivos](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx) de carpetas del sistema.

Por ejemplo, si desea cargarlo con el actual explorador de archivos de Azure Data Lake para Visual Studio copie primero el archivo en otro directorio. En caso contrario, al menos en la fecha de redacción de este artículo, se cargará la versión de 32 bits. La razón de esto es que Visual Studio es una aplicación de 32 bits que realiza una redirección del sistema de archivos en la ventana de selección de archivo de carga de Azure Data Lake. Posteriormente, cuando ejecute un script U-SQL que llame al ensamblado nativo, obtendrá el siguiente error (interno) en tiempo de ejecución:

**Excepción interna de expresión de usuario: Se ha intentado cargar un programa con un formato incorrecto. (Excepción de HRESULT: 0x8007000B).**

Después de cargar los dos archivos de ensamblado, ahora los registramos en una base de datos SQLSpatial con el siguiente script:

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

En este caso, registramos solo un ensamblado de U-SQL e incluimos el ensamblado nativo como una dependencia de cadena en el ensamblado de U-SQL. Para usar los ensamblados espaciales, únicamente se necesita hacer referencia al ensamblado U-SQL. El archivo adicional automáticamente estará disponible para el ensamblado. Este es un script de ejemplo simple que usa el ensamblado espacial:

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

La biblioteca de tipos de SQL tiene una dependencia en el ensamblado System.XML, por lo que es necesario hacer referencia a él. Además, algunos de los métodos utilizan los tipos de System.Data.SqlTypes en lugar de los tipos integrados de C#. Dado que System.Data ya se incluye de forma predeterminada, puedo hacer referencia al tipo SQL necesario. El código anterior está disponible en nuestro [sitio de Github](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample).


### <a name="use-assembly-versioning"></a>Uso del control de versiones de ensamblado
Actualmente, U-SQL usa la versión 4.5 de .NET Framework. Por lo tanto, asegúrese de que sus propios ensamblados son compatibles con esa versión del tiempo de ejecución.

Como se mencionó anteriormente, U-SQL ejecuta código en un formato de 64 bits (x64). Por lo tanto, asegúrese de que el código se compila para ejecutarse en x64. De lo contrario, se produce el error de formato incorrecto mostrado anteriormente.

El archivo DLL y el de recursos de cada ensamblado cargado como, por ejemplo, un tiempo de ejecución diferente, un ensamblado nativo o un archivo de configuración pueden tener 400 MB como máximo. El tamaño total de recursos implementados, ya sea mediante DEPLOY RESOURCE o mediante referencias a ensamblados y a sus archivos adicionales, no puede superar los 3 GB.

Por último, tenga en cuenta que cada base de datos de U-SQL solo puede contener una versión de cualquier ensamblado dado. Por ejemplo, si necesita la versión 7 y la versión 8 de la biblioteca de NewtonSoft Json.Net, debe registrarlos en dos bases de datos diferentes. Además, cada script solo puede hacer referencia a una versión de un archivo DLL de ensamblado dado. En este sentido, U-SQL sigue la semántica de control de versiones y la administración de ensamblados de C#.


## <a name="use-user-defined-functions-udf"></a>Funciones definidas por el usuario: UDF
Las funciones definidas por el usuario de U-SQL o UDF son rutinas programadas que aceptan parámetros, realizan una acción (como un cálculo complejo) y devuelven el resultado de esa acción como un valor. El valor devuelto de UDF solo puede ser un escalar único. Se puede llamar a UDF de U-SQL en un script base U-SQL como a cualquier otra función escalar de C#.

Es recomendable que inicialice las funciones definidas por el usuario de U-SQL como **públicas** y **estáticas**.

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

Primero veamos el ejemplo sencillo de creación de una UDF.

En este escenario de caso de uso, es necesario determinar el período fiscal, incluido el trimestre fiscal y el mes fiscal del primer inicio de sesión del usuario específico. El primer mes fiscal del año en nuestro escenario es junio.

Para calcular el período fiscal, se introduce la siguiente función de C#:

```c#
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
        }
```

Simplemente calcula el trimestre y mes fiscal y se devuelve un valor de cadena. Para junio (primer mes del primer trimestre fiscal) se usa "Q1:P1". Para julio: "Q1:P2" y así sucesivamente.

Se trata de una función de C# que vamos a usar en nuestro proyecto U-SQL.

Este es el aspecto de la sección de código subyacente en este escenario:

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }

    }

}
```

Ahora vamos a llamar a esta función desde el script base U-SQL. Para ello, se debe proporcionar un nombre completo de la función, incluido el espacio de nombres que, en este caso, es EspacioDeNombres.Clase.Función(parámetro).

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

El siguiente es el script base U-SQL real:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

El siguiente es el archivo de salida de la ejecución del script:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Este ejemplo muestra un uso simple de UDF insertada en U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Mantenimiento del estado entre las invocaciones de UDF
Los objetos de programación de C# de U-SQL pueden ser más sofisticados utilizando interactividad a través de variables globales de código subyacente. Veamos el siguiente escenario de caso de uso empresarial.

En organizaciones grandes, los usuarios pueden cambiar entre variedades de aplicaciones internas. Estas pueden incluir Microsoft Dynamics CRM, PowerBI, etc. Es posible que los clientes deseen aplicar un análisis de telemetría de cómo los usuarios cambian entre distintas aplicaciones, cuáles son las tendencias de uso, etc. El objetivo para la empresa es optimizar el uso de las aplicaciones. Es posible que también quieran combinar aplicaciones diferentes o rutinas de inicio de sesión específicas.

Para lograr este objetivo, tenemos que determinar identificadores de sesión y retardo de tiempo entre las últimas sesiones que han tenido lugar.

Es necesario encontrar un inicio de sesión anterior y luego asignar este inicio de sesión a todas las sesiones que se generan en la misma aplicación. El primer desafío es que el script base U-SQL no nos permitirá aplicar cálculos sobre una columna ya calculada con la función LAG. El segundo reto es que se debe mantener la sesión específica para todas las sesiones dentro del mismo período de tiempo.

Para solucionar este problema, vamos a usar una variable global dentro de una sección de código subyacente: `static public string globalSession;`.

Esta variable global se aplica a todo el conjunto de filas durante nuestra ejecución de script.

Esta es la sección de código subyacente de nuestro programa U-SQL:

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

Este ejemplo muestra la variable global `static public string globalSession;` que se usa dentro de la función `getStampUserSession` y se reinicializa cada vez que cambia el parámetro Session.

El script base de U-SQL es como sigue:

```sql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
               EventDateTime,
               LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
               LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

Se llama a la función `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` aquí durante el segundo cálculo del conjunto de filas de memoria. Pasa la columna `UserSessionTimestamp` y devuelve el valor hasta que `UserSessionTimestamp` se cambia.

El archivo de salida tiene el siguiente aspecto:

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Este ejemplo muestra un escenario de caso de uso más complicado en el que se usa una variable global dentro de la sección de código subyacente que se aplica al conjunto de filas de memoria completo.

## <a name="use-user-defined-types-udt"></a>Uso de tipos definidos por el usuario: UDT
Los tipos de definidos por el usuario o UDT constituyen otra característica de programación de U-SQL. UDT de U-SQL actúa como un tipo de definido por el usuario de C# normal. C# es un lenguaje fuertemente tipado que permite el uso de tipos integrados y definidos por el usuario personalizados.

U-SQL no puede serializar ni deserializar de forma implícita UDT arbitrarios mientras el UDT se pasa entre vértices de conjuntos de filas. Esto implica que el usuario debe proporcionar un formateador explícito mediante la interfaz de IFormatter. Esto proporcionará a U-SQL los métodos parar serializar o deserializar el UDT.

> [!NOTE]
> En la actualidad, los extractores y outputters integrados de U-SQL no pueden serializar ni deserializar los datos de UDT en archivos ni desde estos ni siquiera con el conjunto IFormatter. Por ello, cuando esté escribiendo datos UDT en un archivo con la instrucción OUTPUT o leyéndolos con un extractor, tiene que pasarlos como una cadena o matriz de bytes. A continuación, llama al código de serialización y deserialización explícitamente (es decir, al método ToString() del UDT). Por otra parte, los extractores y outputters definidos por el usuario pueden leer y escribir UDT.

Si se intenta utilizar UDT en EXTRACTOR o OUTPUTTER (fuera de SELECT anterior), como se muestra a continuación:

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Recibirá el error siguiente:

```
    Error    1    E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
    the preceding SELECT.    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
    USQL-Programmability\Types.usql    52    1    USQL-Programmability
```

Para trabajar con UDT en outptutter tenemos que serializar en una cadena con el método ToString() o crear un outputter personalizado.

Actualmente los UDT no se pueden usar en GROUP BY. Si UDT se usa en GROUP BY, se produce el siguiente error:

```
    Error    1    E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
    for column myfield

    Description:

    GROUP BY doesn't support UDT or Complex types.

    Resolution:

    Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
    62    5    USQL-Programmability
```

Para definir un UDT, tenemos que:

* Agregue los siguientes espacios de nombres:

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* Agregue `Microsoft.Analytics.Interfaces`, necesario para las interfaces UDT. También se podría necesitar `System.IO` para definir la interfaz de IFormatter.

* Defina un tipo definido por el usuario con el atributo SqlUserDefinedType.

**SqlUserDefinedType** se utiliza para marcar una definición de tipo en un ensamblado como un tipo definido por el usuario (UDT) en U-SQL. Las propiedades del atributo reflejan las características físicas del UDT. Esta clase no se puede heredar.

SqlUserDefinedType es un atributo necesario para la definición de UDT.

El constructor de la clase:  

* SqlUserDefinedTypeAttribute (formateador de tipos)

* Formateador de tipos: parámetro necesario para definir un formateador UDT. En concreto, el tipo de interfaz `IFormatter` que se debe pasar aquí.

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
              {
             …
           }
```

* El UDT típico también requerirá la definición de la interfaz IFormatter, tal como se muestra en el ejemplo siguiente:

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

La interfaz `IFormatter` serializa y deserializa un gráfico de objetos con el tipo de raíz de \<typeparamref name="T">.

\<typeparam name="T">El tipo de raíz para el gráfico de objetos para serializar y deserializar.

* **Deserializar**: deserializa los datos en el flujo proporcionado y reconstituye el gráfico de objetos.

* **Serializar**: serializa un objeto o un gráfico de objetos con el directorio raíz especificado en el flujo proporcionado.

`MyType` instancia: Instancia del tipo.  
`IColumnWriter`Escritor y lector`IColumnReader`: flujo de columna subyacente.  
Contexto `ISerializationContext`: enumeración que define un conjunto de marcas que especifica el contexto de origen o destino para el flujo durante la serialización.

   * **Intermedio**: especifica que el contexto de origen o destino no es un almacén persistente.

   * **Persistencia**: especifica que el contexto de origen o destino es un almacén persistente.

Como un tipo de C# normal, la definición de UDT de U-SQL puede incluir invalidaciones para los operadores como +/==/!=. Puede incluir también métodos estáticos. Por ejemplo, si vamos a usar este UDT como un parámetro para la función de agregado MIN de U-SQL, tenemos que definir la invalidación del operador <.

Anteriormente en esta guía se mostró un ejemplo para la identificación del período fiscal a partir de la fecha específica con el formato Qn:Pn (Q1:P10). En el ejemplo siguiente se muestra cómo definir un tipo personalizado para los valores del período fiscal.

A continuación aparece un ejemplo de sección de código subyacente con interfaz IFormatter y UDT personalizados:

```c#
        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
```

El tipo definido incluye dos números: trimestre y mes. Los operadores ==/!=/>/< y el método estático ToString() se definen aquí.

Como se mencionó anteriormente, UDT se puede utilizar en expresiones SELECT, pero no se puede usar en OUTPUTTER/EXTRACTOR sin serialización personalizada. Debe serializarse como una cadena con ToString() o usarse con OUTPUTTER/EXTRACTOR personalizado.

Ahora vamos a analizar el uso de UDT. En una sección de código subyacente, cambiamos nuestra función GetFiscalPeriod a la siguiente:

```c#
        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }
```

Como puede ver, devuelve el valor de nuestro tipo FiscalPeriod.

Vea este ejemplo de cómo se usa más en el script base de U-SQL. En este ejemplo se muestran distintas maneras de invocación de UDT a partir del script U-SQL.

```sql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

Este es un ejemplo de una sección de código subyacente completa:

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }

    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Uso de agregados definidos por el usuario: UDAGG
Los agregados definidos por el usuario son todas las funciones relacionadas con la agregación que no se distribuyen de fábrica con U-SQL. El ejemplo puede ser un agregado para realizar un cálculo matemático personalizado, realizar concatenaciones de cadenas, manipulaciones con cadenas, etc.

La definición de clase base agregada definida por el usuario es la siguiente:

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica que el tipo se debe registrar como un agregado definido por el usuario. Esta clase no se puede heredar.

El atributo SqlUserDefinedType es **opcional** para la definición de UDAGG.


La clase base le permite pasar tres parámetros abstractos: dos de entrada y uno de resultado. Los tipos de datos son variables y se deben definirse durante la herencia de clases.

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* **Init** se invoca una vez para cada grupo durante el cálculo. Proporciona la rutina de inicialización para cada grupo de agregación.  
* **Accumulate** se ejecuta una vez para cada valor. Proporciona la funcionalidad principal para el algoritmo de agregación. Puede utilizarse para agregar valores con varios tipos de datos definidos durante la herencia de clases. Puede aceptar dos parámetros de tipos de datos de variable.
* **Terminate** se ejecuta una vez por cada grupo de agregación al final del procesamiento para generar el resultado para cada grupo.

Para declarar tipos de datos de entrada y salida correctos, utilice la definición de clase de la siguiente manera:

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Primer parámetro para Accumulate
* T2: Primer parámetro para Accumulate
* TResult: Tipo de valor devuelto de Terminate

Por ejemplo:

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

o

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Uso de UDAGG en U-SQL
Para usar UDAGG, defínalo primero en código subyacente o haga referencia a él desde el archivo DLL de programación existente como se indicó anteriormente.

Después use la sintaxis siguiente:

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

A continuación se muestra un ejemplo de UDAGG:

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
            guid_agg = "";
        }

        public override void Accumulate(string guid, string user)
        {
            if (user.ToUpper()== "USER1")
            {
                guid_agg += "{" + guid + "}";
            }
        }

        public override string Terminate()
        {
            return guid_agg;
        }

    }
```

Y el script base U-SQL:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

En este escenario de caso de uso, se concatenan los GUID de clase para los usuarios específicos.

## <a name="use-user-defined-objects-udo"></a>Uso de objetos definidos por el usuario: UDO
U-SQL le permite definir objetos de programación personalizados, que se denominan objetos definidos por el usuario o UDO.

A continuación aparece una lista de UDO en U-SQL:

* Extractores definidos por el usuario
    * Extraer fila por fila
    * Se usa para implementar la extracción de datos de archivos estructurados personalizados

* Outputters definidos por el usuario
    * Dar salida fila por fila
    * Se usa para la salida de tipos de datos personalizados o para formatos de archivo personalizados

* Procesadores definidos por el usuario
    * Tomar una fila y producir una fila
    * Se usa para reducir el número de columnas o producir nuevas columnas con valores derivados de un conjunto de columnas existente

* Aplicadores definidos por el usuario
    * Tomar una fila y producir de 0 a n filas
    * Se usa con CROSS/OUTER APPLY

* Combinadores definidos por el usuario
    * Combina conjuntos de filas (JOIN definidas por el usuario)

* Reductores definidos por el usuario
    * Tomar n filas y producir una fila
    * Se usa para reducir el número de filas

UDO normalmente se llama explícitamente en el script U-SQL como parte de las siguientes instrucciones U-SQL:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="use-user-defined-extractors"></a>Uso de extractores definidos por el usuario
U-SQL permite importar datos externos utilizando una instrucción EXTRACT. La instrucción EXTRACT puede utilizar extractores UDO integrados:  

* *Extractors.Text()*: Proporciona extracción de archivos de texto delimitado de diferentes codificaciones.

* *Extractors.Csv()*: Proporciona extracción de archivos de valores separados por comas (CSV) de diferentes codificaciones.

* *Extractors.Tsv()*: Proporciona extracción de archivos de valores separados por tabulaciones (TSV) de diferentes codificaciones.

Puede ser útil desarrollar un extractor personalizado. Esto puede resultar útil durante la importación de datos si se desea realizar alguna de las siguientes tareas:

* Modificar datos de entrada mediante la división de columnas y modificar valores individuales. La funcionalidad PROCESSOR es mejor para combinar las columnas.
* Analizar datos no estructurados, como páginas web o correos electrónicos o datos parcialmente no estructurados, como XML o JSON.
* Analizar datos en codificación no compatible.

Para definir un extractor definido por el usuario o UDE, necesitamos crear una interfaz `IExtractor`. Todos los parámetros de entrada al extractor, como delimitadores de columna o fila, codificación, etc., deben definirse en el constructor de la clase. La interfaz `IExtractor` también debe contener la definición de la invalidación `IEnumerable<IRow>` como se indica a continuación:

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

El atributo **SqlUserDefinedExtractor** indica que el tipo se debe registrar como un extractor definido por el usuario. Esta clase no se puede heredar.

SqlUserDefinedExtractor es un atributo opcional para la definición de UDE. Se utiliza para definir la propiedad AtomicFileProcessing para el objeto UDE.

* bool     AtomicFileProcessing   

* **true** = Indica que este extractor requiere archivos de entrada atómicos (JSON, XML...)
* **false** = Indica que este extractor puede tratar con archivos divididos o distribuidos (CSV, SEQ...)

Los objetos de programación UDE principales son **input** y **output**. El objeto de entrada se utiliza para enumerar los datos de entrada como `IUnstructuredReader`. El objeto de salida se utiliza para establecer datos de salida como resultado de la actividad del extractor.

Se tiene acceso a los datos de entrada a través de `System.IO.Stream` y `System.IO.StreamReader`.

Para la enumeración de columnas de entrada, primero dividiremos el flujo de entrada mediante un delimitador de filas.

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

Después, dividiremos aún más la fila de entrada en partes de la columna.

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
               foreach (string part in parts)
        {
        …
        }
    }
```

Para establecer datos de salida, usamos el método `output.Set`.

Es importante comprender que el extractor personalizado solo genera columnas y valores que se definen con la salida. Establezca la llamada del método.

```c#
    output.Set<string>(count, part);
```

La salida del extractor real se desencadena mediante una llamada a `yield return output.AsReadOnly();`.

Este es el ejemplo de extractor:

```c#
    [SqlUserDefinedExtractor(AtomicFileProcessing = true)]
    public class FullDescriptionExtractor : IExtractor
    {
         private Encoding _encoding;
         private byte[] _row_delim;
         private char _col_delim;

        public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
        {
             this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
             this._row_delim = this._encoding.GetBytes(row_delim);
             this._col_delim = col_delim;

        }

        public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
        {
             string line;
             //Read the input line by line
             foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
             {
                using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
                 {
                     line = streamReader.ReadToEnd().Trim();
                     //Split the input by the column delimiter
                     string[] parts = line.Split(this._col_delim);
                     int count = 0; // start with first column
                     foreach (string part in parts)
                     {
    if (count == 0)
                         {  // for column “guid”, re-generated guid
                             Guid new_guid = Guid.NewGuid();
                             output.Set<Guid>(count, new_guid);
                         }
                         else if (count == 2)
                         {
                             // for column “user”, convert to UPPER case
                             output.Set<string>(count, part.ToUpper());

                         }
                         else
                         {
                             // keep the rest of the columns as-is
                             output.Set<string>(count, part);
                         }
                         count += 1;
                     }

                 }
                 yield return output.AsReadOnly();
             }
             yield break;
         }
     }
```

En este escenario de caso de uso, el extractor vuelve a generar un GUID para la columna “guid” y convierte los valores de la columna “user” en mayúsculas. Los extractores personalizados pueden producir resultados más complicados mediante el análisis de datos de entrada y la manipulación de estos.

Este es un script base U-SQL que utiliza un extractor personalizado:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Uso de outputters definidos por el usuario
Outputter definido por el usuario es otro UDO de U-SQL que permite extender una funcionalidad integrada de U-SQL. Al igual que el extractor, hay varios outputters integrados.

* *Outputters.Text()*: Escribe datos en archivos de texto delimitado de diferentes codificaciones.
* *Outputters.Csv()*: Escribe datos en archivos de valores separados por coma (CSV) de diferentes codificaciones.
* *Outputters.Tsv()*: Escribe datos en archivos de valores separados por tabulaciones (TSV) de diferentes codificaciones.

El outputter personalizado le permite escribir datos en un formato definido personalizado. Esto puede resultar útil para las siguientes tareas:

* Escribir datos en archivos semiestructurados o no estructurados.
* Escribir datos en codificaciones no admitidas.
* Modificar datos de salida o agregar atributos personalizados.

Para definir el outputter definido por el usuario, necesitamos crear la interfaz `IOutputter`.

Esta es la implementación de la clase base `IOutputter`:

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

Todos los parámetros de entrada al outputter, como delimitadores de columna o fila, codificación, etc., deben definirse en el constructor de la clase. La interfaz `IOutputter` también debe contener una definición de la invalidación `void Output`. El atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)`, opcionalmente, se puede establecer para el procesamiento de archivos atómico. Para más información, consulte los siguientes detalles.

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class MyOutputter : IOutputter
        {

            public MyOutputter(myparam1, myparam2)
            {
              …
            }

            public override void Close()
            {
              …
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
              …
            }
        }
```

* `Output` se llama para cada fila de entrada. Devuelve el conjunto de filas `IUnstructuredWriter output`.
* La clase Constructor se utiliza para pasar parámetros al outputter definido por el usuario.
* `Close` se usa para invalidar opcionalmente para liberar el estado costoso o saber cuándo se ha escrito la última fila.

El atributo **SqlUserDefinedOutputter** indica que el tipo se debe registrar como un outputter definido por el usuario. Esta clase no se puede heredar.

SqlUserDefinedOutputter es un atributo opcional para la definición del outputter definido por el usuario. Se utiliza para definir la propiedad AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true** = Indica que este outputter requiere archivos de salida atómicos (JSON, XML...)
* **false** = Indica que este outputter puede tratar con archivos divididos o distribuidos (CSV, SEQ...)

Los objetos de programación principales son **row** y **output**. El objeto **fila** se usa para enumerar los datos de salida como la interfaz `IRow`. **Salida** se usa para establecer los datos de salida al archivo de destino.

Se tiene acceso a los datos de entrada a través de la interfaz `IRow`. Los datos de salida pasan una fila en cada momento.

Los valores individuales se enumeran mediante una llamada al método Get de la interfaz IRow:

```c#
    row.Get<string>("column_name")
```

Los nombres de columna individuales se pueden determinar mediante una llamada a `row.Schema`:

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Este enfoque permite crear un outputter flexible para cualquier esquema de metadatos.

Los datos de salida se escriben en el archivo mediante `System.IO.StreamWriter`. El parámetro de flujo se establece en `output.BaseStrea` como parte de `IUnstructuredWriter output`.

Tenga en cuenta que esto es importante para vaciar el búfer de datos en el archivo después de cada iteración de fila. Además, el objeto `StreamWriter` debe utilizarse con el atributo Disposable habilitado (valor predeterminado) y con la palabra clave **using**:

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

De lo contrario, llame al método Flush() explícitamente después de cada iteración. Esto se muestra en el ejemplo siguiente.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Establecimiento de encabezados y pies de página para el outputter definido por el usuario
Para establecer un encabezado, use el flujo de ejecución de iteración único.

```c#
            public override void Output(IRow row, IUnstructuredWriter output)
            {
             …
                if (isHeaderRow)
                {
                    …                
                }

             …
                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
             …
            }
        }
```

El código del primer bloque `if (isHeaderRow)` se ejecuta solo una vez.

Para el pie de página, use la referencia a la instancia del objeto `System.IO.Stream` (`output.BaseStream`). Escriba el pie de página en el método Close() de la interfaz `IOutputter`.  Para más información, consulte el siguiente ejemplo.

Este es un ejemplo de un outputter definido por el usuario:

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class HTMLOutputter : IOutputter
        {
            // Local variables initialization
            private string row_delimiter;
            private char col_delimiter;
            private bool isHeaderRow;
            private Encoding encoding;
            private bool IsTableHeader = true;
            private Stream g_writer;

            // Parameters definition            
            public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                this.isHeaderRow = isHeader;
                this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
            }

            // The Close method is used to write the footer to the file. It's executed only once, after all rows
            public override void Close().
            {
                //Reference to IO.Stream object - g_writer
                StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
                streamWriter.Write("</table>");
                streamWriter.Flush();
                streamWriter.Close();
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
                System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

                // Metadata schema initialization to enumerate column names
                ISchema schema = row.Schema;

                // This is a data-independent header--HTML table definition
                if (IsTableHeader)
                {
                    streamWriter.Write("<table border=1>");
                    IsTableHeader = false;
                }

                // HTML table attributes
                string header_wrapper_on = "<th>";
                string header_wrapper_off = "</th>";
                string data_wrapper_on = "<td>";
                string data_wrapper_off = "</td>";

                // Header row output--runs only once
                if (isHeaderRow)
                {
                    streamWriter.Write("<tr>");
                    for (int i = 0; i < schema.Count(); i++)
                    {
                        var col = schema[i];
                        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
                    }
                    streamWriter.Write("</tr>");
                }

                // Data row output
                streamWriter.Write("<tr>");                
                for (int i = 0; i < schema.Count(); i++)
                {
                    var col = schema[i];
                    string val = "";
                    try
                    {
                        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Handling NULL values--keeping them empty
                    catch (System.NullReferenceException)
                    {
                    }
                    streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
                }
                streamWriter.Write("</tr>");

                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
                // Reference to the instance of the IO.Stream object for footer generation
                g_writer = output.BaseStream;
                streamWriter.Flush();
            }
        }

        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Y el script base U-SQL:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Se trata de un outputter HTML que crea un archivo HTML con los datos de la tabla.

### <a name="call-outputter-from-u-sql-base-script"></a>Llamada a un outputter desde el script base U-SQL
Para llamar a un outputter personalizado desde el script base U-SQL, se debe crear la nueva instancia del objeto outputter.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar la creación de una instancia del objeto en el script base, podemos crear un contenedor de función como se indicó en el ejemplo anterior:

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

En este caso, la llamada original es similar a la siguiente:

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Uso de procesadores definidos por el usuario
El procesador definido por el usuario o UDP es un tipo de UDO de U-SQL que permite procesar las filas entrantes mediante la aplicación de características de programación. UDP permite combinar columnas, modificar valores y agregar nuevas columnas si es necesario. Básicamente le ayuda a procesar un conjunto de filas para generar elementos de datos necesarios.

Para definir un UDP, necesitamos crear una interfaz `IProcessor` con el atributo `SqlUserDefinedProcessor`, que es opcional para UDP.

Esta interfaz debe contener la definición de la invalidación del conjunto de filas de interfaz `IRow`, como se muestra en el ejemplo siguiente:

```c#
    [SqlUserDefinedProcessor]
     public class MyProcessor: IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
            …
         }
     }
```

**SqlUserDefinedProcessor** indica que el tipo se debe registrar como un procesador definido por el usuario. Esta clase no se puede heredar.

El atributo SqlUserDefinedProcessor es **opcional** para la definición de UDP.

Los objetos de programación principales son **input** y **output**. El objeto Input se utiliza para enumerar las columnas de entrada y Output para establecer datos de salida como resultado de la actividad del procesador.

Para la enumeración de las columnas de entrada, usamos el método `input.Get`.

```c#
    string column_name = input.Get<string>("column_name");
```

El parámetro para el método `input.Get` es una columna que se pasa como parte de la cláusula `PRODUCE` de la instrucción `PROCESS` del script base U-SQL. Es necesario usar aquí el tipo de datos correcto.

Para la salida, use el método `output.Set`.

Es importante comprender que el productor personalizado solo genera columnas y valores que se definen con la llamada al método `output.Set`.

```c#
    output.Set<string>("mycolumn", mycolumn);
```

La salida del procesador real se desencadena mediante una llamada a `return output.AsReadOnly();`.

Este es un ejemplo de procesador:

```c#
     [SqlUserDefinedProcessor]
     public class FullDescriptionProcessor : IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
             string user = input.Get<string>("user");
             string des = input.Get<string>("des");
             string full_description = user.ToUpper() + "=>" + des;
             output.Set<string>("dt", input.Get<string>("dt"));
             output.Set<string>("full_description", full_description);
             output.Set<Guid>("new_guid", Guid.NewGuid());
             output.Set<Guid>("guid", input.Get<Guid>("guid"));
             return output.AsReadOnly();
         }
     }
```

En este escenario de caso de uso, el procesador genera una nueva columna denominada "full_description" combinando las existentes (en este caso, “user” en mayúsculas y “des”). También vuelve a generar un GUID y devuelve los valores de GUID originales y nuevos.

Como puede ver en el ejemplo anterior, puede llamar a métodos de C# durante la llamada al método `output.Set`.

Este es un ejemplo de script U-SQL base que utiliza un procesador personalizado:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Uso de aplicadores definidos por el usuario
Un aplicador definido por el usuario U-SQL permite invocar una función de C# personalizada para cada fila devuelta por la expresión de tabla externa de una consulta. La entrada de la derecha se evalúa para cada fila de la entrada izquierda y las filas producidas se combinan en la salida final. La lista de las columnas producidas por el operador APPLY es la combinación del conjunto de columnas de la entrada izquierda y derecha.

El aplicador definido por el usuario se invoca como parte de la expresión USQL SELECT.

La llamada típica al aplicador definido por el usuario tiene un aspecto parecido al siguiente:

```sql
    SELECT …
    FROM …
    CROSS APPLYis used to pass parameters
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Consulte [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx) (U-SQL SELECT, selección de CROSS APPLY y OUTER APPLY) para más información sobre cómo utilizar aplicadores en una expresión SELECT.

La definición de clase base de aplicador definida por el usuario es la siguiente:

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

Para definir un aplicador definido por el usuario, es necesario crear la interfaz `IApplier` con el atributo [`SqlUserDefinedApplier`], que es opcional para la definición de este aplicador.

```c#
    [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {

        public ParserApplier()
        {
        …
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {
        …
        }
    }
```

* Se llama a Apply para cada fila de la tabla externa. Devuelve el conjunto de filas de salida `IUpdatableRow`.
* La clase Constructor se utiliza para pasar parámetros al aplicador definido por el usuario.

**SqlUserDefinedApplier** indica que el tipo se debe registrar como un aplicador definido por el usuario. Esta clase no se puede heredar.

**SqlUserDefinedApplier** es **opcional** para la definición del aplicador definido por el usuario.


Los objetos de programación principales son los siguientes:

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Los conjuntos de filas de entrada se pasan como entrada `IRow`. Las filas de salida se generan como interfaz de salida `IUpdatableRow`.

Los nombres de columna individuales se pueden determinar mediante una llamada al método Schema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Para obtener los valores de datos reales a partir de `IRow` de entrada, utilizamos el método Get() de la interfaz `IRow`.

```c#
    mycolumn = row.Get<int>("mycolumn")
```

O bien, se utiliza el nombre de columna de esquema:

```c#
    row.Get<int>(row.Schema[0].Name)
```

Los valores de salida se deben establecer con la salida `IUpdatableRow`:

```c#
    output.Set<int>("mycolumn", mycolumn)
```

Es importante comprender que los aplicadores personalizados solo darán como resultado columnas y valores definidos con la llamada al método `output.Set`.

La salida real se desencadena mediante una llamada a `yield return output.AsReadOnly();`.

Los parámetros del aplicador definido por el usuario se pueden pasar al constructor. El aplicador puede devolver un número variable de columnas que debe definirse durante la llamada al aplicador en el script base U-SQL.

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Este es el ejemplo del aplicador definido por el usuario:

```c#
   [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {
        private string parsingPart;

        public ParserApplier(string parsingPart)
        {
            if (parsingPart.ToUpper().Contains("ALL")
                || parsingPart.ToUpper().Contains("MAKE")
                || parsingPart.ToUpper().Contains("MODEL")
                || parsingPart.ToUpper().Contains("YEAR")
                || parsingPart.ToUpper().Contains("TYPE")
                || parsingPart.ToUpper().Contains("MILLAGE")
                )
            {
                this.parsingPart = parsingPart;
            }
            else
            {
                throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
            }
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {

            string[] properties = input.Get<string>("properties").Split(',');

            //  only process with correct number of properties
            if (properties.Count() == 5)
            {

                string make = properties[0];
                string model = properties[1];
                string year = properties[2];
                string type = properties[3];
                int millage = -1;

                // Only return millage if it is number, otherwise, -1
                if (!int.TryParse(properties[4], out millage))
                {
                    millage = -1;
                }

                if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
                if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
                if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
                if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
                if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
            }
            yield return output.AsReadOnly();            
        }
    }
```

Este es el script base U-SQL para este aplicador definido por el usuario:

```sql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

En este escenario de caso de uso, el aplicador definido por el usuario actúa como un analizador de valores delimitados por comas para las propiedades de flota de automóviles. Las filas del archivo de entrada tienen el siguiente aspecto:

```
103    Z1AB2CD123XY45889    Ford,Explorer,2005,SUV,152345
303    Y0AB2CD34XY458890    Shevrolet,Cruise,2010,4Dr,32455
210    X5AB2CD45XY458893    Nissan,Altima,2011,4Dr,74000
```

Es un archivo TSV delimitado por tabulaciones típico con la columna de propiedades que contiene las propiedades de los automóviles, como la marca y el modelo. Esas propiedades deben analizarse con las columnas de tabla. El aplicador proporcionado también permite generar un número dinámico de propiedades en el conjunto de filas de resultados, según el parámetro que se pasa. Puede generar todas las propiedades o solo un conjunto específico de propiedades.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Se puede llamar al aplicador definido por el usuario como una nueva instancia del objeto applier:

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

O con la invocación de un método de fábrica de contenedor:

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Uso de combinadores definidos por el usuario
El combinador definido por el usuario o UDC permite combinar filas de conjuntos de filas izquierdos y derechos, en función de una lógica personalizada. El combinador definido por el usuario se utiliza con la expresión de COMBINE.

Se invoca a un combinador con la expresión COMBINE que proporciona la información necesaria sobre los conjuntos de filas de entrada, las columnas de agrupamiento, el esquema de resultados esperado e información adicional.

Para llamar a un combinador en un script base U-SQL, se usa la sintaxis siguiente:

```sql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Para más información, vea [COMBINE Expression (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx) (Expresión COMBINE (U-SQL)).

Para definir un combinador definido por el usuario, es necesario crear la interfaz `ICombiner` con el atributo [`SqlUserDefinedCombiner`], que es opcional para la definición de dicho combinador.

Definición de clase base `ICombiner`:

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

La implementación personalizada de una interfaz `ICombiner` debe contener la definición de una invalidación Combine `IEnumerable<IRow>`.

```c#
    [SqlUserDefinedCombiner]
    public class MyCombiner : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
        …
        }
    }
```

El atributo **SqlUserDefinedCombiner** indica que el tipo se debe registrar como un combinador definido por el usuario. Esta clase no se puede heredar.

**SqlUserDefinedCombiner** se utiliza para definir la propiedad del modo de combinador. Es un atributo opcional para la definición de un combinador definido por el usuario.

Modo CombinerMode

La enumeración de CombinerMode puede tomar los siguientes valores:

* Full (0) Cada fila de salida depende potencialmente de todas las filas de entrada desde la izquierda y derecha con el mismo valor de clave.

* Left (1) Cada fila de salida depende de una sola fila de entrada de la izquierda (y potencialmente todas las filas de la derecha con el mismo valor de clave).

* Right (2) Cada fila de salida depende de una sola fila de entrada de la derecha (y potencialmente todas las filas de la izquierda con el mismo valor de clave).

* Inner (3) Cada fila de salida depende de una sola fila de entrada de la izquierda y derecha con el mismo valor.

Ejemplo:    [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Los objetos de programación principales son:

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Los conjuntos de filas de entrada se pasan como el tipo de interfaz `IRowset` **left** y **right**. Ambos conjuntos de filas deben enumerarse para su procesamiento. Solo puede enumerar una vez cada interfaz, por lo que se debe enumerar y almacenar en caché si es necesario.

Para el almacenamiento en caché, se puede crear un tipo de estructura de memoria List\<T\> como resultado de la ejecución de la consulta LINQ y, más concretamente, List<`IRow`>. El tipo de datos anónimo también se puede utilizar durante la enumeración.

Consulte [Introduction to LINQ Queries (C#)](https://msdn.microsoft.com/library/bb397906.aspx) (Introducción a las consultas de LINQ (C#)) para más información sobre las consultas LINQ e [Interfaz IEnumerable\<T\>](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) para más información sobre la interfaz IEnumerable\<T\>.

Para obtener los valores de datos reales a partir de `IRowset` de entrada, utilizamos el método Get() de la interfaz `IRow`.

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Los nombres de columna individuales se pueden determinar mediante una llamada al método Schema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

O bien, se utiliza el nombre de columna de esquema:

```
    c# row.Get<int>(row.Schema[0].Name)
```

La enumeración general con LINQ tiene el siguiente aspecto:

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Después de enumerar ambos conjuntos de filas, vamos a crear un bucle a través de todas las filas. Para cada fila del conjunto de filas izquierdo, vamos a buscar todas las filas que satisfagan la condición de nuestro combinador.

Los valores de salida se deben establecer con la salida `IUpdatableRow`.

```c#
    output.Set<int>("mycolumn", mycolumn)
```

La salida real se desencadena mediante una llamada a `yield return output.AsReadOnly();`.

Este es un ejemplo de combinador:

```c#
    [SqlUserDefinedCombiner]
    public class CombineSales : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
            var internetSales =
            (from row in left.Rows
                          select new
                          {
                              ProductKey = row.Get<int>("ProductKey"),
                              OrderDateKey = row.Get<int>("OrderDateKey"),
                              SalesAmount = row.Get<decimal>("SalesAmount"),
                              TaxAmt = row.Get<decimal>("TaxAmt")
                          }).ToList();

            var resellerSales =
            (from row in right.Rows
             select new
             {
                 ProductKey = row.Get<int>("ProductKey"),
                 OrderDateKey = row.Get<int>("OrderDateKey"),
                 SalesAmount = row.Get<decimal>("SalesAmount"),
                 TaxAmt = row.Get<decimal>("TaxAmt")
             }).ToList();

            foreach (var row_i in internetSales)
            {
                foreach (var row_r in resellerSales)
                {

                    if (
                        row_i.OrderDateKey > 0
                        && row_i.OrderDateKey < row_r.OrderDateKey
                        && row_i.OrderDateKey == 20010701
                        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
                    {
                        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
                        output.Set<int>("ProductKey", row_i.ProductKey);
                        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
                        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
                    }

                }
            }
            yield return output.AsReadOnly();
        }
    }
```

En este escenario de caso de uso, vamos a crear un informe de análisis para cada distribuidor. El objetivo es encontrar todos los productos cuyo costo es superior a 20 000 USD y que se venden a través del sitio web de Internet más rápido que mediante el distribuidor convencional dentro de un determinado período de tiempo.

Aquí está el script base U-SQL: Puede comparar la lógica entre JOIN convencional y un combinador:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Se puede llamar al combinador definido por el usuario como una nueva instancia del objeto applier:

```c#
    USING new MyNameSpace.MyCombiner();
```


O con la invocación de un método de fábrica de contenedor:

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Uso de reductores definidos por el usuario
U-SQL le permite escribir reductores de conjuntos de filas personalizados en C# mediante el marco de extensibilidad de operadores definidos por el usuario mediante la implementación de una interfaz IReducer.

El reductor definido por el usuario o UDR puede usarse para eliminar las filas innecesarias durante la extracción de datos (importar). También se puede utilizar para manipular y evaluar las filas y columnas. En función de la lógica de programación, puede definir también qué filas se deben extraer.

Para definir una clase UDR, necesitamos crear una interfaz `IReducer` con el atributo `SqlUserDefinedReducer` opcional.

Esta interfaz de clase debe contener una definición de la invalidación del conjunto de filas de interfaz `IEnumerable`.

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
            …
            }

        }
```

El atributo **SqlUserDefinedReducer** indica que el tipo se debe registrar como un reductor definido por el usuario. Esta clase no se puede heredar.
**SqlUserDefinedReducer** es un atributo opcional para la definición del reductor definido por el usuario. Se utiliza para definir la propiedad IsRecursive.

* bool     IsRecursive    
* **true**  = Indica si el reductor es idempotente

Los objetos de programación principales son **input** y **output**. El objeto input se utiliza para enumerar las filas de entrada. Output se usa para establecer filas de salida como resultado de la actividad de reducción.

Para la enumeración de filas de entrada, usamos el método `Row.Get`.

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

El parámetro para el método `Row.Get` es una columna que se pasa como parte de la clase `PRODUCE` de la instrucción `REDUCE` del script base U-SQL. También es necesario usar aquí el tipo de datos correcto.

Para la salida, use el método `output.Set`.

Es importante comprender que el reductor personalizado solo dará como resultado valores definidos con la llamada al método `output.Set`.

```c#
    output.Set<string>("mycolumn", guid);
```

La salida del reductor real se desencadena mediante una llamada a `yield return output.AsReadOnly();`.

Este es un ejemplo de reductor:

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
                string guid;
                DateTime dt;
                string user;
                string des;

                foreach (IRow row in input.Rows)
                {
                    guid = row.Get<string>("guid");
                    dt = row.Get<DateTime>("dt");
                    user = row.Get<string>("user");
                    des = row.Get<string>("des");

                    if (user.Length > 0)
                    {
                        output.Set<string>("guid", guid);
                        output.Set<DateTime>("dt", dt);
                        output.Set<string>("user", user);
                        output.Set<string>("des", des);

                        yield return output.AsReadOnly();
                    }
                }
            }

        }
```

En este escenario de caso de uso, el reductor omite filas con un nombre de usuario vacío. Para cada fila del conjunto de filas lee cada columna necesaria, después evalúa la longitud del nombre de usuario. Y genera la fila real solo si la longitud del valor del nombre de usuario es mayor que 0.

Este es un script base U-SQL que utiliza un reductor personalizado:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

