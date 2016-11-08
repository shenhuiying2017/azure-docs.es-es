---
title: Almacenamiento y rendimiento de DocumentDB | Microsoft Docs
description: Más información sobre el almacenamiento de datos y el almacenamiento de documentos DocumentDB y cómo escalar DocumentDB para satisfacer las necesidades de capacidad de la aplicación.
keywords: almacenamiento de documentos
services: documentdb
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: mimig

---
# Obtener información acerca del almacenamiento y el aprovisionamiento de rendimiento predecible en DocumentDB
Azure DocumentDB es un servicio de bases de datos NoSQL orientado a documentos totalmente administrado y escalable para documentos JSON. Con DocumentDB, no tendrá que alquilar máquinas virtuales, implementar software ni supervisar bases de datos. Los ingenieros de Microsoft operan y supervisan de forma continua DocumentDB para ofrecer disponibilidad, rendimiento y protección de datos universales.

Para comenzar con DocumentDB, [cree una cuenta de base de datos](documentdb-create-account.md) y una [base de datos de DocumentDB](documentdb-create-database.md) mediante el [Portal de Azure](https://portal.azure.com/). Las bases de datos de DocumentDB se ofrecen en unidades de rendimiento y almacenamiento respaldado de unidad de estado sólido (SSD). Estas unidades de almacenamiento se aprovisionan mediante la [creación de colecciones de bases de datos](documentdb-create-collection.md) dentro de la cuenta de base de datos, y cada colección tiene capacidad de proceso reservada que se puede escalar o reducir verticalmente en cualquier momento para satisfacer las demandas de la aplicación.

Si la aplicación supera la capacidad de proceso reservada para una o varias colecciones, las solicitudes se limitan en función de la colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas.

En este artículo se ofrece información general sobre los recursos y métricas disponibles para administrar la capacidad y planear el almacenamiento de datos.

## Cuenta de base de datos
Como suscriptor de Azure, puede aprovisionar una o más cuentas de base de datos de DocumentDB para administrar los recursos de base de datos. Cada suscripción está asociada con una única suscripción de Azure.

Se pueden crear cuentas de DocumentDB mediante el [Portal de Azure](documentdb-create-account.md) o mediante [una plantilla de ARM o la CLI de Azure](documentdb-automation-resource-manager-cli.md).

## Bases de datos
Una única base de datos de DocumentDB puede contener una cantidad prácticamente ilimitada de almacenamiento de documentos agrupado por colecciones. Las colecciones proporcionan aislamiento del rendimiento; cada colección puede aprovisionarse con rendimiento que no se comparte con otras colecciones de la misma base de datos o cuenta. Una base de datos de DocumentDB es elástica en tamaño, comprendiendo desde unos pocos GB hasta TB de almacenamiento de documento respaldado y rendimiento aprovisionado de SSD. A diferencia de la base de datos RDBMS tradicional, una base de datos en DocumentDB no está dirigida a un único equipo y puede abarcar varios equipos o clústeres.

Con DocumentDB, a medida que escala sus aplicaciones, podrá crear más colecciones o bases de datos o ambas. Las bases de datos se pueden crear a través del [Portal de Azure](documentdb-create-database.md) o mediante cualquiera de los [SDK de DocumentDB](documentdb-dotnet-samples.md).

## Colecciones de base de datos
Cada base de datos de DocumentDB puede contener una o más colecciones. Las colecciones funcionan como particiones de datos de alta disponibilidad para el procesamiento y almacenamiento de documentos. Cada colección puede almacenar documentos con esquema heterogéneo. Las capacidades de consulta e indización automática de DocumentDB le permiten filtrar y recuperar documentos con facilidad. Una colección proporciona el ámbito del almacenamiento de documentos y la ejecución de consultas. Una colección también es un dominio de transacciones para todos los documentos contenidos en la misma. A las colecciones se les asigna capacidad de proceso en función del valor establecido en el Portal de Azure o mediante los SDK.

Las colecciones se particionan automáticamente en uno o más servidores físicos mediante DocumentDB. Cuando crea una colección, puede especificar el rendimiento aprovisionado en términos de unidades de solicitud por segundo y una propiedad de clave de partición. El valor de esta propiedad se usará en DocumentDB para distribuir documentos entre particiones y enrutar solicitudes, como las consultas. El valor de la clave de partición también actúa como límite de la transacción para los procedimientos y los desencadenadores almacenados. Cada colección tiene una cantidad reservada de capacidad de proceso específica para esa colección, que no se comparte con otras colecciones de la misma cuenta. Por lo tanto, puede escalar la aplicación horizontalmente tanto en términos de rendimiento como de almacenamiento.

Las colecciones se pueden crear a través del [Portal de Azure](documentdb-create-collection.md) o mediante cualquiera de los [SDK de DocumentDB](documentdb-sdk-dotnet.md).

## Solicitud de unidades y las operaciones de base de datos
Al crear una colección, reserva capacidad de procesoç en términos de [unidades de solicitud (RU)](documentdb-request-units.md) por segundo. En lugar de pensar en los recursos de hardware y cómo administrarlos, puede considerar una **unidad de solicitud** una medida única de los recursos necesarios para realizar varias operaciones de base de datos y dar servicio a una solicitud de la aplicación. Una lectura de un documento de 1 KB consume la misma 1 RU independientemente del número de elementos almacenados en la colección o del número de solicitudes simultáneas que se ejecuten a la vez. Todas las solicitudes de DocumentDB, incluidas operaciones complejas, como las consultas SQL, tienen un valor de RU predecible que puede determinarse en el tiempo de desarrollo. Si conoce el tamaño de los documentos y la frecuencia de cada operación (lecturas, escrituras y consultas) para respaldar la aplicación, puede aprovisionar la cantidad exacta de rendimiento para satisfacer las necesidades de su aplicación, y escalar y reducir la base de datos verticalmente a medida que cambien sus necesidades de rendimiento.

Cada colección puede reservarse con rendimiento en bloques de 100 unidades de solicitud por segundo, desde cientos hasta millones de unidades de solicitud por segundo. El rendimiento aprovisionado se puede ajustar durante la vida de una colección para adaptarse a las cambiantes necesidades de procesamiento y a los patrones de acceso de la aplicación. Para obtener más información, consulte [Niveles de rendimiento de DocumentDB](documentdb-performance-levels.md).

> [!IMPORTANT]
> Las colecciones son entidades facturables. El costo está determinado por el rendimiento aprovisionado de la colección que se mide en unidades de solicitud por segundo junto con el almacenamiento total consumido en gigabytes.
> 
> 

¿Cuántas unidades de solicitud consumirá una operación concreta como insertar, eliminar, consultar o la ejecución de un procedimiento almacenado? Una unidad de solicitud es una medida normalizada del costo de procesamiento de solicitudes. Una lectura de un documento de 1 KB es 1 RU, pero una solicitud para insertar, reemplazar o eliminar el mismo documento consumirá más procesamiento del servicio y, por tanto, más unidades de solicitud. Cada respuesta del servicio incluye un encabezado personalizado (`x-ms-request-charge`) que informa de las unidades de solicitud usadas por la solicitud. Este encabezado también es accesible a través de los [SDK](documentdb-sdk-dotnet.md). En el SDK de .NET, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) es una propiedad del objeto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx). Si desea estimar sus necesidades de capacidad de proceso antes de realizar una sola llamada, puede usar la herramienta [Capacity Planner](documentdb-request-units.md#estimating-throughput-needs) para ayudarle con este cálculo.

> [!NOTE]
> La línea de base de 1 unidad de solicitud para un documento de 1 KB corresponde a una solicitud GET sencilla del documento con [coherencia de sesión](documentdb-consistency-levels.md).
> 
> 

Hay varios factores que afectan a las unidades de solicitud usadas por una operación en una cuenta de base de datos de DocumentDB. Entre estos factores se incluyen:

* Tamaño del documento. Cuando aumenta el tamaño del documento también aumentan las unidades usadas para leer o escribir los datos.
* Recuento de propiedades. Suponiendo que la indexación predeterminada de todas las propiedades, las unidades usadas para escribir un documento aumentarán conforme aumenta el recuento de propiedades.
* Coherencia de datos Al usar los niveles de coherencia de datos Alta y De uso vinculado, se usarán unidades adicionales para leer documentos.
* Propiedades indexadas. Propiedades indexadas: Una directiva de índice en cada colección determina qué propiedades se indexan de forma predeterminada. Puede reducir el consumo de unidades de solicitud limitando el número de las propiedades indexadas.
* Indexación de documentos. De forma predeterminada, todos los documentos se indexan automáticamente, por tanto consumirá menos unidades de solicitud si no desea indexar algunos de los documentos.

Para más información, consulte [Unidades de solicitud de DocumentDB](documentdb-request-units.md).

Por ejemplo, esta es una tabla que muestra el número de unidades de solicitud que se deben aprovisionar en tres tamaños diferentes de documento (1 KB, 4 KB y 64 KB) y en dos niveles de rendimiento diferentes (500 lecturas/segundo + 100 escrituras/segundo y 500 lecturas/segundo + 500 escrituras/segundo). Se configuró la coherencia de datos en la sesión y la directiva de indexación se estableció en None.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamaño del documento</strong></p></td>
            <td valign="top"><p><strong>Lecturas/segundo</strong></p></td>
            <td valign="top"><p><strong>Escrituras/segundo</strong></p></td>
            <td valign="top"><p><strong>Unidades de solicitud</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29 000 RU/s</p></td>
        </tr>
    </tbody>
</table>

Las consultas, procedimientos almacenados y desencadenadores consumen unidades de solicitud según la complejidad de las operaciones que se están llevando a cabo. Cuando desarrolla su aplicación, inspeccione el encabezado request charge para entender mejor cómo consumen las operaciones la capacidad de unidad de solicitud.

## Opción de nivel de coherencia y rendimiento
La selección del nivel de coherencia predeterminado tiene un impacto en la capacidad de proceso y en la latencia. Puede establecer el nivel de coherencia predeterminado mediante programación y a través del portal de Azure. También puede reemplazar el nivel de coherencia en función de la solicitud. De forma predeterminada, el nivel de coherencia está establecido en **Sesión**, que proporciona lecturas/escrituras monotónicas y lee las garantías de escritura. La coherencia de sesión es mayor para aplicaciones centradas en el usuario y proporciona un equilibrio ideal de intercambios de coherencia y rendimiento.

Para obtener instrucciones sobre cómo cambiar el nivel de coherencia en el Portal de Azure, consulte [Administración de una cuenta de DocumentDB](documentdb-manage-account.md#consistency). O bien, para obtener más información sobre los niveles de coherencia, consulte [Uso de los niveles de coherencia](documentdb-consistency-levels.md).

## Sobrecarga de índice y almacenamiento de documentos aprovisionados
DocumentDB admite la creación de colecciones de una sola partición y con varias particiones. Cada partición en DocumentDB admite hasta 10 GB de almacenamiento respaldado de SSD. Los 10 GB de almacenamiento de documentos incluyen más almacenamiento para el índice. De forma predeterminada, una colección de la Base de datos de documentos se configura para que indexe automáticamente todos los documentos sin solicitar de forma explícita ningún índice o esquema secundario. En función de las aplicaciones que usan DocumentDB, la sobrecarga de índice típica es de entre el 2 y el 20 %. La tecnología de indexación que usa DocumentDB garantiza que, a pesar de los valores de las propiedades, la sobrecarga de índice no supera el 80 % del tamaño de los documentos con la configuración predeterminada.

De forma predeterminada, DocumentDB indexa todos los documentos automáticamente. Sin embargo, en caso de que desee ajustar la sobrecarga de índice, puede elegir eliminar determinados documentos de la indexación en el momento de insertar o reemplazar un documento, según lo descrito en [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md). Puede configurar una colección de la Base de datos de documentos para que excluya todos los documentos de la colección de la indexación. También puede configurar una colección de DocumentDB para indexar de manera selectiva solo ciertas propiedades o rutas de acceso con caracteres comodín de los documentos JSON, tal como se describe en [Configuración de la directiva de indexación de una colección](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Excluir propiedades o documentos también mejora la capacidad de proceso de escritura; lo que significa que consumirá menos unidades de solicitud.

## Pasos siguientes
Para continuar aprendiendo acerca del funcionamiento de DocumentDB, consulte [Partición y escalado en Azure DocumentDB](documentdb-partition-data.md).

Para obtener instrucciones sobre cómo supervisar los niveles de rendimiento en el Portal de Azure, consulte [Supervisión de una cuenta de DocumentDB](documentdb-monitor-accounts.md). Para obtener más información sobre la elección de los niveles de rendimiento para las colecciones, consulte [Niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).

<!---HONumber=AcomDC_0824_2016-->