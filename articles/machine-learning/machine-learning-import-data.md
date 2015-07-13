<properties
	pageTitle="Importación de datos en Estudio de aprendizaje automático | Microsoft Azure"
	description="Cómo importar datos de entrenamiento a Estudio de aprendizaje automático de Azure desde varios orígenes de datos Obtenga información sobre qué tipos de datos y formatos de datos son compatibles."
	keywords="import data,data format,data types,data sources,training data"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/19/2015"
	ms.author="garye" />


#Importación de datos de entrenamiento en Estudio de aprendizaje automático de Azure desde varios orígenes de datos

Cuando desarrolla una solución de análisis predictivo en Estudio de aprendizaje automático de Azure, entrena su modelo con datos representativos de su espacio de problemas. En este tutorial se muestra cómo importar datos desde diversos orígenes de datos para entrenar un modelo en Estudio de aprendizaje automático. También aprenderá qué formatos de datos son compatibles.

Existe una gran variedad de conjuntos de datos de ejemplo disponibles en Estudio de aprendizaje automático que puede usar con este fin (consulte [Uso de conjuntos de datos de ejemplo en Estudio de aprendizaje automático de Azure](machine-learning-use-sample-datasets.md)). Pero también puede importar sus propios datos en Estudio de aprendizaje automático para usarlos en sus experimentos.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para usar sus propios datos en Estudio de aprendizaje automático, puede cargar un archivo de datos por adelantado desde el disco duro local para crear un módulo de conjunto de datos en el área de trabajo. O bien, puede acceder a los datos desde cualquiera de los orígenes de datos en línea mientras su experimento se ejecuta con el módulo [Lector][reader]:

- Almacenamiento de blobs de Azure, tabla o base de datos SQL
- Hadoop con HiveQL
- Una dirección URL web con HTTP
- Un proveedor de fuente de distribución de datos

Estudio de aprendizaje automático está diseñado para trabajar con datos rectangulares o tabulares, como datos de texto delimitados o datos estructurados de una base de datos, aunque en algunas circunstancias, es posible usar datos no rectangulares.

Se recomienda que los datos estén relativamente limpios. Es decir, es aconsejable que se ocupe de problemas como las cadenas sin comillas antes de cargar los datos en el experimento.

Sin embargo, en Estudio de aprendizaje automático hay módulos que le permitirán manipular levemente los datos en el experimento. Dependiendo de los algoritmos de aprendizaje automático que usará, es posible que deba decidir cómo controlar los problemas estructurales de los datos, como valores que faltan y datos esparcidos y existen módulos que pueden ayudar en esto. Observe la sección **Transformación de datos** de la paleta de módulos para los módulos que realizan estas funciones.

En cualquier momento del experimento puede ver o descargar los datos que genera un módulo con un clic con el botón derecho en el puerto de salida. Dependiendo del módulo, es posible que haya distintas opciones de descarga disponibles, o bien que se puedan ver los datos dentro del explorador web en Estudio de aprendizaje automático.


## Formatos de datos

Puede importar diversos tipos de datos al experimento, dependiendo del mecanismo que usa para importar los datos y de dónde provienen estos:

- Texto sin formato (.txt)
- Valores separados por coma (CSV) con un encabezado (.csv) o sin encabezado (.nh.csv)
- Valores separados con tabulaciones (TSV) con un encabezado (.tsv) o sin encabezado (.nh.tsv)
- Tabla de Hive
- Tabla de Base de datos SQL
- Valores de OData
- Datos SVMLight (.svmlight) (consulte la [definición de SVMLight](http://svmlight.joachims.org/) para obtener más información sobre el formato)
- Datos de formato de archivo con relación de atributo (ARFF) (.arff) (consulte la [definición de ARFF](http://weka.wikispaces.com/ARFF) si desea ver información sobre el formato)
- Archivo ZIP (.zip)
- Archivo de área de trabajo u objeto de R (.RData)

Si importa datos en un formato distinto de ARFF que incluyan metadatos, Estudio de aprendizaje automático usa estos metadatos para definir el tipo de datos y encabezado de cada columna. Si importa datos en formato TSV o CSV que no incluyan estos metadatos, Estudio de aprendizaje automático infiere el tipo de datos de cada columna tomando una muestra de los mismos. Si los datos no tienen encabezados de columna, Estudio de aprendizaje automático proporciona nombres predeterminados. Puede especificar o cambiar explícitamente los encabezados y los tipos de datos de las columnas usando el [editor de metadatos][metadata-editor].

Estudio de aprendizaje automático reconoce los siguientes tipos de datos:

- String
- Entero
- Doble
- Booleano
- DateTime
- TimeSpan

Estudio de aprendizaje automático usa un tipo de datos interno llamado *Tabla de datos* para pasar datos entre los módulos. Puede convertir explícitamente sus datos en formato de Tabla de datos con el módulo [Convertir al conjunto de datos][convert-to-dataset]. Todo módulo que acepta formatos distintos de Tabla de datos convertirá los datos a Tabla de datos de manera silenciosa antes de pasarlos al módulo siguiente. En caso de ser necesario, puede convertir el formato Tabla de datos de vuelta al formato CSV, TSV, ARFF o SVMLight mediante el uso de otros módulos de conversión. Consulte la sección **Conversiones de formatos de datos** de la paleta de módulos para ver los módulos que realizan estas funciones.


## Importación de datos desde un archivo local

Para importar datos desde un disco duro local, siga estos pasos:

1. Haga clic en **+NUEVO** en la parte inferior de la ventana de Estudio de aprendizaje automático.
2. Seleccione **CONJUNTO DE DATOS** y **DESDE ARCHIVO LOCAL**.
3. En el cuadro de diálogo **Cargar un nuevo conjunto de datos**, vaya al archivo que desea cargar.
4. Escriba un nombre, identifique el tipo de datos y, si lo desea, escriba una descripción. Se recomienda incluir una descripción: le permite registrar cualquier característica acerca de los datos que querrá recordar cuando use los datos en el futuro.
5. La casilla **Esta es la versión nueva de un conjunto de datos existente** le permite actualizar una base de datos existente con datos nuevos. Haga clic en esta casilla y, a continuación, escriba el nombre de un conjunto de datos existente.

Durante la carga, verá un mensaje que le indica que se está cargando el archivo. El tiempo de la carga dependerá del tamaño de los datos y de la velocidad de conexión con el servicio. Si sabe que el archivo tardará mucho tiempo en cargarse, puede realizar otras operaciones en Estudio de aprendizaje automático mientras espera. Sin embargo, si cierra el explorador, la carga de los datos generará un error.

Una vez que los datos estén cargados, se almacenan en un módulo de conjunto de datos y se encontrarán disponibles para cualquier experimento que se realice en su área de trabajo. Puede encontrar la base de datos, junto con todos los conjuntos de datos de muestra cargados previamente, en la lista de **conjuntos de datos guardados** en la paleta de módulos cuando edita un experimento.


## Acceso a datos en línea con el módulo Lector

Mediante el uso del módulo [Lector][reader] en el experimento, puede tener acceso a datos provenientes de varias fuentes en línea mientras se ejecuta su experimento. Dado que se accede a estos datos mientras el experimento está en ejecución, los datos solo están disponibles en un experimento (en contraposición a los módulos de conjuntos de datos, los que se encuentran disponibles para todos los experimentos del área de trabajo).

Después de agregar el módulo [Lector][reader] al experimento, selecciona el **origen de los datos** y, a continuación, proporciona información de acceso con parámetros de módulo. Por ejemplo, si selecciona **Dirección URL web vía HTTP**, proporciona la dirección URL de origen y el formato de los datos. Si accede a los datos desde Almacenamiento de Azure o HDInsight (mediante una consulta de Hive), especifique la información de cuenta adecuada y la ubicación de los datos.

> [AZURE.NOTE]Este artículo proporciona información general acerca del módulo [Lector][reader]. Para obtener información más detallada acerca de los tipos de datos a los que puede tener acceso, formatos, parámetros y respuestas a las preguntas más comunes, consulte el tema de referencia de módulo correspondiente al módulo [Lector][reader].


### Obtención de datos desde Azure

La importación se puede realizar desde tres orígenes de datos de Azure:

- **Almacenamiento de blobs de Azure**: si usa el formato ARFF para al almacenamiento, las columnas se asignan mediante el uso de los metadatos de los encabezados. Si usa formatos TSV o CSV, las asignaciones se infieren mediante el muestreo de datos de columna.
- **Almacenamiento de tablas de Azure**: el módulo [Lector][reader] examina sus datos para identificar tipos de datos de columna. Si los datos son lo suficientemente homogéneos y predecibles, es posible limitar la cantidad de filas que se examinan.
- **Base de datos SQL de Azure**: el módulo [Lector][reader] usa la API de cliente de SQL Azure Transact para importar datos mediante una consulta de base de datos que usted proporciona.

Para el almacenamiento de tablas y el almacenamiento de blobs, suministre un URI de firma de acceso compartido (URI de SAS) o la información de la cuenta de Almacenamiento de Azure para proporcionar acceso a los datos. En el caso de una Base de datos SQL de Azure, se suministra su información de cuenta y base de datos, más una consulta de base de datos que identifica los datos que desea importar.

### Obtención de datos de la web

Puede usar el módulo [Lector][reader] para leer datos de un sitio web o un sitio FTP. Deberá proporcionar:

- Una dirección URL HTTP completa de un archivo.
- El formato de datos del archivo (CSV, TSV, ARFF o SvmLight).
- En el caso de los archivos CSV o TSV, indique si la primera línea del archivo es un encabezado.

### Obtención de datos desde Hadoop

Puede usar el módulo [Lector][reader] para leer datos de un almacenamiento distribuido mediante el lenguaje de consultas HiveQL. Deberá especificar la consulta de base de datos de Hive y proporcionar información de acceso del usuario en el servidor HCatalog. También deberá especificar si los datos se almacenan en un sistema de archivos distribuido de Hadoop (HDFS) o en Azure y, en caso de tratarse de Azure, la información de cuenta de Azure.

### Obtención de datos desde un proveedor de fuentes de datos

Especifique una dirección URL de OData para leer directamente desde un proveedor de fuente de distribución de datos. Deberá proporcionar la dirección URL de origen y el tipo de contenido de los datos.


## Datos guardados desde el experimento


Habrá ocasiones en las que querrá tomar un resultado intermedio de un experimento y usarlo como parte de otro experimento. Para ello, siga estos pasos:

1. Haga clic con el botón secundario en la salida del módulo que desea guardar como conjunto de datos.

2. Haga clic en **Guardar como conjunto de datos**.

3. Cuando se le solicite, escriba un nombre y una descripción que le permitan identificar fácilmente el conjunto de datos.

4. Haga clic en la marca de verificación **Aceptar**.

Cuando termine de guardar, el conjunto de datos estará disponible para usarlo dentro de cualquier experimento de su área de trabajo. Puede encontrarlo en la lista **Conjuntos de datos guardados** de la paleta de módulos.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=62-->