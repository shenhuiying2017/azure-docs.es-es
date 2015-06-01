<properties
   pageTitle="Análisis y procesamiento de documentos JSON con Hive en HDInsight | Microsoft Azure"
   description="Aprenda a utilizar documentos JSON y analizarlos mediante Hive en HDInsight"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Aprenda a procesar y analizar documentos JSON en Hive

JSON es uno de los formatos más usados en la web. Este tutorial le ayudará a entender una de las cuestiones comunes a las que se enfrentan los usuarios de Hive: cómo usar documentos JSON en Hive y luego ejecutar un análisis en ellos.

##Ejemplo JSON

Veamos un ejemplo. Supongamos que tenemos el documento JSON que se muestra a continuación. Nuestro objetivo es ser capaces de analizar este documento JSON y luego poder ejecutar consultas en este documento como valor de búsqueda en una clave, agregación, etc.

	  {
        "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
        "StudentDetails": [
          {
            "FirstName": "Peggy",
            "LastName": "Williams",
            "YearJoined": 2012
          }
        ],
        "StudentClassCollection": [
          {
            "ClassId": "89084343",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "High",
            "Score": 93,
            "PerformedActivity": false
          },
          {
            "ClassId": "78547522",
            "ClassParticipation": "NotSatisfied",
            "ClassParticipationRank": "None",
            "Score": 74,
            "PerformedActivity": false
          },
          {
            "ClassId": "78675563",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "Low",
            "Score": 83,
            "PerformedActivity": true
          }
        ] 
      }

##Acoplamiento de un documento JSON (paso necesario solo si tiene bastante JSON)

Antes de utilizar cualquier operador de Hive para realizar análisis, debemos procesar previamente el documento JSON para que esté listo para usarse en Hive.

En todas las opciones enumeradas en la siguiente sección se supone que el documento JSON está en una sola fila. En otras palabras, debemos acoplar el documento JSON a una cadena para que se pueda usar con operadores de Hive.

**Si su documento JSON ya está acoplado y el documento entero se encuentra en una sola línea, puede omitir este paso e ir directamente a la sección siguiente sobre Análisis de los datos JSON.**

Supongamos que el documento JSON acoplado que abarca varias filas está presente en el almacén de blobs de Azure en la carpeta */json/input/* bajo el contenedor predeterminado. El código siguiente crea una tabla jsonexample que señala al documento original JSON no acoplado.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

A continuación, vamos a crear una nueva tabla llamada one_line_json que apuntará al documento JSON acoplado y que se almacenará en el almacén de blobs de Azure en la carpeta */json/temp/* bajo el contenedor predeterminado.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

Por último, se rellenará esta tabla con los datos de JSON acoplados.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

En este momento, si observa la tabla one_line_json recién creada, podrá ver que contiene el documento JSON en una sola línea

    select * from one_line_json;

Este es el resultado de esta consulta:

![Acoplamiento del documento JSON.][image-hdi-hivejson-flatten]

##Opciones para analizar documentos JSON en Hive

Ahora que tenemos el documento JSON en una tabla con una sola columna, estamos listos para utilizar Hive para ejecutar consultas en estos datos. Hive proporciona tres mecanismos distintos para ejecutar consultas en documentos JSON:

1.	Mediante la función definida por el usuario (UDF): get_json_object
2.	Mediante la UDF: json_tuple
3.	Mediante el SerDe personalizado

Vamos a examinar cada uno de ellos en detalle.

##UDF get_json_object
Hive proporciona una UDF integrada llamada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que puede realizar consultas JSON en tiempo de ejecución. Este método toma dos argumentos: el nombre de la tabla y el nombre del método que tiene el documento JSON acoplado y el campo JSON que debe analizarse. Veamos un ejemplo para ver cómo funciona esta UDF.

Obtener el nombre y el apellido de cada estudiante

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

Este es el resultado cuando se ejecuta esta consulta en la ventana de la consola.

![UDF get_json_object][image-hdi-hivejson-getjsonobject]

Existen algunas limitaciones de esta UDF.


- Una de las principales es que no es eficaz puesto que cada campo de la consulta requiere volver a analizar la consulta, lo que afecta al rendimiento.
- En segundo lugar, get_json_object() devuelve la representación de cadena de una matriz. Así que para convertir esto en una matriz de Hive, tendrá que utilizar expresiones regulares para reemplazar los corchetes ’[' y ']' y, a continuación, llamar también a split para obtener la matriz.


Este es el motivo de que el sitio wiki de Hive recomiende el uso de json_tuple, que explicaremos a continuación.


##UDF json_tuple

La otra UDF proporcionada por Hive se denomina [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) y es más eficaz que [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método toma un conjunto de claves y una cadena JSON y devuelve una tupla de valores mediante una función. Veamos un ejemplo para ver cómo funciona esta UDF.

Tomar el Id. y el curso del estudiante del documento JSON

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

Usamos la sintaxis [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) de Hive que permite que json_tuple cree una tabla virtual aplicando la función UDT a cada fila de la tabla original.

Veamos la salida de este script en la consola de Hive:

![UDF json_tuple][image-hdi-hivejson-jsontuple]

Una de las principales desventajas de esta UDF es que documentos JSON complejos se vuelven demasiado difíciles de manejar debido al uso repetido de LATERAL VIEW. De hecho, esta UDF no puede controlar JSON anidados.

##Mediante el SerDe personalizado

Para analizar documentos JSON anidados, SerDe es la **mejor opción** dado que es posible definir el esquema JSON, lo que hace que sea muy fácil ejecutar consultas en él.

Analizaremos cómo utilizar uno de los SerDe más conocidos que ha sido desarrollado por [rcongiu](https://github.com/rcongiu).

Paso 1: Asegúrese de que [el Kit de desarrollo de Java SE 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) esté instalado (Nota: si tiene instalado JDK 1.8, no funcionará con este SerDe).


- Si va a usar la implementación de Windows de HDInsight, elija la versión Windows x 64 del JDK.
- Una vez completada la instalación, vaya al Panel de Control--> Agregar variables de entorno y agregue una nueva variable de entorno JAVA_HOME que apunte a C:\\Program Files\\Java\\jdk1.7.0_55 o a donde esté instalado el JDK. En las siguientes capturas de pantalla se muestra cómo puede definir la variable de entorno.

![Configuración de los valores correctos para JDK][image-hdi-hivejson-jdk]

Paso 2: Vaya a [este](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) vínculo y descargue Maven 3.3.1. Descomprima el archivo donde desea almacenar los archivos binarios. En mi caso, lo descomprimiré en C:\\Program Files\\Maven. Agregue la carpeta Bin a la ruta de acceso; para ello, vaya a Panel de control--> Editar las variables del sistema para su cuenta Variables de entorno. La captura de pantalla siguiente muestra cómo hacerlo.

![Configuración de Maven][image-hdi-hivejson-maven]

Paso 3: Clone el proyecto del sitio de github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Para ello, haga clic en el botón "Download Zip" (Descargar archivo comprimido) como se muestra en la captura de pantalla siguiente.

![Clonación del proyecto][image-hdi-hivejson-serde]

Paso 4: Vaya a la carpeta donde ha descargado este paquete y escriba "mvn.package". Esta acción debe crear los archivos jar necesarios que luego puede copiar en el clúster.

Paso 5: A continuación, vaya a la carpeta de destino bajo la carpeta raíz donde descargó el paquete. Cargue el archivo json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar en el nodo principal del clúster. Yo lo coloco normalmente bajo la carpeta de archivos binarios de Hive: C:\\apps\\dist\\hive-0.13.0.2.1.11.0-2316\\bin o algo similar.
 
Paso 6: En el símbolo del sistema de Hive, escriba "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Como en mi caso el archivo jar está en la carpeta C:\\apps\\dist\\hive-0.13.x\\bin, puedo agregarlo directamente con el nombre tal como se muestra a continuación:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![Agregar JAR a su proyecto][image-hdi-hivejson-addjar]

Ahora, estamos listos para usar el SerDe para ejecutar consultas en el documento JSON.

Vamos a crear primero la tabla con el esquema del documento JSON. Tenga en cuenta que podemos administrar muchos más tipos, como map, matrices y structs. Escriba el código siguiente en la consola Hive para que Hive cree una tabla denominada json_table que puede leer el documento JSON según el esquema siguiente.

    drop table json_table;
    create external table json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

Ahora que hemos definido el esquema, vamos a ejecutar algunos ejemplos para ver cómo podemos consultar el documento JSON mediante Hive:

a) Mostrar el nombre y el apellido del alumno

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Este es el resultado de la consola de Hive.

![Consulta SerDe 1][image-hdi-hivejson-serde_query1]

b) Esta consulta calcula la suma de puntuaciones del documento JSON

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
La consulta anterior utiliza la UDF [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) para expandir la matriz de puntuaciones de manera que se puedan sumar.

Este es el resultado de la consola de Hive.

![Consulta SerDe 2][image-hdi-hivejson-serde_query2]

c) Buscar en qué materias un alumno determinado ha obtenido más de 80, select jt.StudentClassCollection.ClassId from json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
La consulta anterior devuelve una matriz de Hive a diferencia de get_json_object que devuelve una cadena.

![Consulta SerDe 3][image-hdi-hivejson-serde_query3]

Si desea eliminar documentos JSON con formato incorrecto, como se explica en la [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de este SerDe, puede hacerlo escribiendo el siguiente código:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##Otras opciones
Además de las opciones mostradas a continuación, también pueden escribir su propio código personalizado usando Python u otros lenguajes específicos para su escenario. Una vez que tenga listo su script de Python, consulte [este artículo][hdinsight-python] acerca de cómo ejecutar su propio código de Python con Hive.

##Resumen
En conclusión, el tipo de operador JSON en Hive que elija depende de su escenario. Si tiene un documento JSON sencillo y solo tiene un campo por el que buscar, puede elegir usar la UDF de Hive get_json_object. Si tiene varias claves por las que buscar, puede utilizar json_tuple. Si tiene un documento anidado, debe utilizar el SerDe de JSON.

En HDInsight estamos trabajando para que cada vez le resulte más fácil el uso de más formatos nativos en Hive sin que tenga que trabajar mucho para ello. Actualizaremos este tutorial con más detalles una vez que tengamos algo que compartir.

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

<!--HONumber=52-->
