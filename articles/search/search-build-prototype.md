<properties 
	pageTitle="Creación de una aplicación de prototipo para Búsqueda de Azure" 
	description="Cree su primer prototipo de aplicación para empezar a usar Búsqueda de Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/08/2015" 
	ms.author="heidist"/>

# Creación de una aplicación de prototipo para Búsqueda de Azure

Los desarrolladores que evalúan Búsqueda de Azure casi siempre comienzan con una aplicación de prueba preliminar que muestra el valor de agregar Búsqueda de Azure a una aplicación personalizada. En este artículo, le proporcionamos algunos bloques de creación para acelerar el proceso de creación de prototipos.
 
- Proyecto de Visual Studio C# que incluye archivos data.json y schema.json. Los datos de ejemplo le permiten compilar y ejecutar la solución inmediatamente, para confirmar que la solución funciona en su sistema antes de escribir una línea de código. 

	Si es posible, intente sustituir estos archivos de datos y de esquema desconectados e independientes por datos de su empresa. Luego, al ejecutar la aplicación, el índice que se crea se basa en el esquema, y los documentos basados en los datos de archivos estarán disponibles inmediatamente para trabajar con Búsqueda de Azure.

- Instrucciones sobre cómo estructurar los datos que va a cargar en Búsqueda de Azure. Necesita un conjunto de datos para cada índice. Búsqueda de Azure usa JSON para la serialización de datos.

- Por último, le indicamos unas cuantas características adicionales que llevarán el prototipo al siguiente nivel, incorporando características específicas de búsqueda como, por ejemplo, navegación por facetas, perfiles de puntuación para ajustar los resultados, consultas de autocompletar o de escritura anticipada, así como funcionalidad relacionada con la página de búsqueda que no está disponible en la alternativa de búsqueda estricta de texto completo.

Al final, tendrá una aplicación de prototipo, con sus datos, que muestra una sólida experiencia de búsqueda con Búsqueda de Azure.

## Preparación de los datos

Las operaciones de búsqueda se realizan en un índice de búsqueda que se crea en Búsqueda de Azure. Un índice tiene varias partes, entre las que se incluyen perfiles de puntuación y proveedores de sugerencias, pero la mayor parte de un índice se compone de campos de datos usados en operaciones de búsqueda.

El conjunto de datos que proporciona los campos debe ser un archivo de datos plano (en JSON), donde cada campo del conjunto de datos se asigna a un campo equivalente en el índice que se trata de rellenar. Otras partes del índice como, por ejemplo, un perfil de puntuación u opciones de CORS, se agregan de forma independiente, directamente en el esquema.

Los orígenes de datos que usan JSON de forma nativa, tales como el almacenamiento de tablas de Azure, proporcionarán datos que resultan más fáciles de usar que los datos relacionales; necesitará una tabla o vista única que proporcione todos los campos de un conjunto de filas.

Para cargar archivos de datos y de esquema con el código de la aplicación de ejemplo de prototipo, puede sobrescribir los archivos de datos y de esquema predeterminados insertados en la solución.

> [AZURE.NOTE]Puede cargar varios archivos de datos, quizás cargar datos en lotes, pero la estructura de los datos tiene que ser la misma para todos los archivos de datos que se cargan en un mismo índice. No se pueden combinar índices; cada índice funciona como un corpus de búsqueda independiente.

## Prueba de la solución de prototipo en el sistema

1. [Creación de un servicio Búsqueda de Azure en el portal de Azure](search-create-service-portal.md).

    Puede agregar una versión compartida (gratuita) del servicio a cualquier suscripción de Azure. El servicio compartido suele usarse para los prototipos. Tenga en cuenta que el servicio compartido proporciona un total de 50 MB de datos o de 10.000 documentos, lo que suceda primero. Además, los datos y documentos pueden repartirse en un máximo de tres índices.

    Si ejecuta la aplicación de ejemplo de prototipo con los archivos de datos de ejemplo integrados, se creará un índice denominado "musicstoreindex" que contiene 246 documentos, en 278 KB en el servicio Búsqueda de Azure. Más adelante en este tutorial, va a reemplazar este índice por otro nuevo con datos de Adventure Works que usarán hasta ## documentos y ## MB.

2. [Descarga de la solución del generador de prototipos](http://go.microsoft.com/fwlink/p/?LinkId=536479). Se trata de una solución de Visual Studio 2013 en C# que crea una aplicación de consola que sirve para crear, cargar y consultar un índice. Si no tiene Visual Studio, puede obtener gratuitamente la versión [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx).

3. Edite app.config para agregar valores de configuración que se dirijan al servicio y la clave de API de Búsqueda.

	Puede obtener la dirección URL y la clave de API de administración en el [panel de servicios del portal](search-create-service-portal.md). Para la dirección URL, escriba la ruta de acceso completa del nombre del servicio, incluido el prefijo https y el dominio `search.windows.net`.

4. Compile la solución para asegurarse de que no hay errores de compilación. Puede que tenga que actualizar los paquetes para resolver errores de compilación. Haga clic con el botón derecho en **Administrar paquetes de NuGet** en la solución para actualizar los paquetes.

5. Ejecute la solución con el esquema y los archivos de datos integrados. Este paso es opcional, pero confirma que la solución funciona antes de dedicar tiempo a agregar sus propios datos. La consola genera los siguientes mensajes:

	- Índice eliminado (solo se produce si existe un índice de nombre "musicstoreindex")
	- Índice creado (se crea un índice denominado "musicstoreindex" en el servicio)
	- Documentos publicados (un mensaje para cada archivo JSON)
	- Esperando 5 segundos (permite que la indexación finalice antes de emitir la primera consulta)
	- Resultados de búsqueda para el término de búsqueda "mejor" sin ningún aumento (se ejecuta una consulta simple que demuestra que los datos se cargan en el índice)

6. Detenga la aplicación y elimine el índice para dejar espacio para el suyo.

    > [AZURE.TIP]Puede usar [el portal](https://portal.azure.com) para eliminar el índice. Haga clic en el nombre del índice para abrir la hoja de índice y use el comando **Eliminar**.

## Sustitución de los archivos JSON de datos y de esquema por sus datos

En la solución de prototipo, hay un archivo de esquema y tres archivos de datos: data1.json, data2.json y data3.json. El archivo de esquema (schema.json) debe sustituirse por un esquema que describa sus datos.

De forma predeterminada, estos archivos se encuentran en la carpeta de soluciones:

![][1]

Si puede incluir los datos en archivos JSON, puede sobrescribir los archivos existentes con sus datos y luego ejecutar la aplicación para crear y cargar un índice. La distribución de datos en varios archivos ayuda a comprobar cómo se realiza una operación de carga por lotes.

Entre otros métodos para cargar datos se incluye el uso de un indexador (requiere un origen de datos de Azure DocumentDB o un origen de datos de Base de datos SQL de Azure). Se puede encontrar código de ejemplo que muestra formas adicionales de cargar los datos en [Búsqueda de Azure: lista de tutoriales y vídeos](search-video-demo-tutorial-list.md) en MSDN.

### Edición de la consulta

La consulta predeterminada se crea en los campos y las estructuras de referencia de la solución a partir de los archivos de datos integrados. Una vez sobrescritos los archivos de datos con sus datos, puede convertir el código de consulta en comentario o modificarlo para que use campos de su esquema. Para obtener más información sobre la sintaxis de consulta de búsqueda, vea [Búsqueda de documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx).

### Compilación y ejecución de la aplicación

Presione **F5** para ejecutar la aplicación. Como antes, verá mensajes que indican que el índice se creó, se cargó y se puede consultar.

Ahora tiene un índice operativo que sirve como base para una investigación más detallada.

En este punto, conviene cambiar a [Fiddler](search-fiddler.md) o [Chrome Postman](search-chrome-postman.md) para ejecutar consultas, modificar cualquiera de las aplicaciones de ejemplo para que use el servicio de búsqueda y el índice o agregar código personalizado que proporcione la visualización de datos.

## Incorporación de un perfil de puntuación

Se recomienda que experimente con [perfiles de puntuación](search-get-started-scoring-profiles.md) bien a través del portal o mediante código. Los perfiles de puntuación aumentan la importancia de un término de búsqueda que se encuentre en un campo concreto. Por ejemplo, si el término de búsqueda es un nombre de ciudad, sería de esperar que los documentos que incluyan *Seattle* en el campo de ciudad aparezcan en una posición más alta en los resultados que los documentos que tengan *Seattle* en un campo de descripción.

Al agregar un perfil de puntuación se cambia el índice; tendrá que compilar y cargar el índice de nuevo siempre que modifique el esquema. Por este motivo, considere la posibilidad de agregar segmentos de código de perfil de puntuación al prototipo (o modificar los ejemplos de perfil de puntuación para que usen su código de indexación).

Vea [Incorporación de un perfil de puntuación](https://msdn.microsoft.com/library/dn798928.aspx) para obtener la documentación de referencia sobre perfiles de puntuación.

## Incorporación de proveedores de sugerencias

El término proveedores de sugerencias se refiere a la conocida característica de búsqueda que proyecta una lista de términos de búsqueda posibles según las entradas de texto del usuario (al escribir "temp" se ofrece una lista de términos de búsqueda completados automáticamente como "temporal", "temprano", "tempestad", etc.).

Para agregar proveedores de sugerencias, agregue una sección al esquema del índice que especifique los campos que se usan para generar consultas de autocompletar o de escritura anticipada. Los campos que contienen nombres o cadenas más cortas con valores no repetitivos suelen dar mejores resultados. Vea [Creación de un índice](https://msdn.microsoft.com/library/dn798928.aspx) para obtener más información.

## Prueba con un analizador de idioma

Los analizadores de idioma proporcionan las reglas lingüísticas para distinguir entre palabras esenciales y no esenciales, formas raíz e incluso sinónimos. De forma predeterminada, Búsqueda de Azure usa el analizador de idioma Lucene para inglés. Puede especificar diferentes analizadores como atributo de índice en campos específicos, lo que le permite crear esquemas y documentos que incluyan campos con distintos analizadores (por ejemplo, una aplicación multilingüe podría combinar campos en francés e inglés en paralelo en el mismo documento). Vea [Compatibilidad de idiomas](https://msdn.microsoft.com/library/dn879793.aspx) para obtener más información.

## Pasos siguientes

En las secciones anteriores, se modificó el índice para agregar más funcionalidad al prototipo. En este momento, los cambios de índice están prácticamente completos (no tratamos la habilitación de CORS, que es el único cambio de esquema restante que podría realizar).

La creación adicional de prototipos posiblemente le lleve ahora en dos direcciones distintas: un enfoque externo basado en la interfaz de usuario, quizás agregando exploración con facetas u otros filtros que ayuden a acotar la búsqueda, o bien, una exploración más a fondo sobre los aspectos de la sincronización de datos de la solución. Muchas soluciones tienen datos volátiles. Para muchos desarrolladores, la sincronización de las actualizaciones de datos entre sistemas de bases de datos transaccionales y un índice de Búsqueda de Azure es la prioridad siguiente una vez comprobados los comportamientos de búsqueda básica.

Visite estos vínculos para obtener más información:

- [Flujos de trabajo típicos en proyectos de desarrollo con Búsqueda de Azure](search-workflow.md)

- [Personalización del indexador de Búsqueda de Azure](search-indexers-customization.md)

- [Navegación por facetas en Búsqueda de Azure](search-faceted-navigation.md)

- [Paginación de los resultados de la búsqueda en Búsqueda de Azure](search-pagination-page-layout.md)


<!--Image references-->
[1]: ./media/search-build-prototype/azsearch-datafiles.png
 

<!---HONumber=Sept15_HO2-->