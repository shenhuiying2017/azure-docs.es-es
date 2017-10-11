---
title: "¿Qué es Almacenamiento de datos SQL de Azure? | Microsoft Docs"
description: "Base de datos distribuida de clase empresarial, capaz de procesar volúmenes de petabytes de datos relacionales y no relacionales. Es el primer almacenamiento de datos en la nube de la industria que aumenta, reduce y hace una pausa en segundos."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 575c49f83c8845edcea984459f3907490c62d269
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>¿Qué es Almacenamiento de datos SQL de Azure?
Azure SQL Data Warehouse es una base de datos relacional, de escalado horizontal, basada en la nube y de procesamiento paralelo masivo (MPP), capaz de procesar volúmenes masivos de datos. 

Almacenamiento de datos SQL

* Combina la base de datos relacional de SQL Server con las funcionalidades de escalado horizontal en la nube de Azure. 
* Separa el almacenamiento del proceso.
* Habilita el aumento, la disminución, la pausa o la reanudación del proceso. 
* Se integra en la plataforma Azure.
* Utiliza SQL Server Transact-SQL (T-SQL) y sus herramientas.
* Cumple con distintos requisitos de seguridad legales y empresariales como SOC e ISO.

En este artículo se describen las características más importantes de Almacenamiento de datos SQL.

## <a name="massively-parallel-processing-architecture"></a>Arquitectura de procesamiento paralelo masivo
El Almacenamiento de datos SQL es un sistema de base de datos distribuidas de procesamiento masivo en paralelo (MPP). En segundo plano, Almacenamiento de datos SQL distribuye los datos entre muchas unidades de procesamiento y almacenamiento shared-nothing. Los datos se almacenan en una capa de almacenamiento con redundancia local Premium, sobre la cual ejecutan consultas los nodos de proceso vinculados dinámicamente. SQL Data Warehouse adopta un enfoque de "divide y vencerás" para ejecutar cargas y consultas complejas. El nodo de control recibe las solicitudes, optimizadas para su distribución y pasadas después a los nodos de proceso para hacer su trabajo en paralelo.

Con el almacenamiento y el proceso separados, SQL Data Warehouse hace lo siguiente:

* Aumentar o reducir el tamaño del almacenamiento independiente del proceso.
* Aumentar o reducir la capacidad de proceso sin mover datos.
* Pausar la capacidad de proceso mientras se dejan los datos intactos, solo se paga por el almacenamiento.
* Reanudar la capacidad de proceso durante las horas operativas.

En el siguiente diagrama se muestra la arquitectura con más detalle.

![Arquitectura de Almacenamiento de datos SQL][1]

**Nodo de control:** el nodo de control administra y optimiza las consultas. Es el front-end que interactúa con todas las aplicaciones y conexiones. En Almacenamiento de datos SQL, el nodo de control tiene tecnología de Base de datos SQL y, al conectarse, adopta la misma apariencia. Debajo de la superficie, el nodo de control coordina todo el movimiento de datos y los cálculos requeridos para ejecutar consultas en paralelo de los datos distribuidos. Al enviar una consulta TSQL a Almacenamiento de datos SQL, el nodo de control la transforma en consultas independientes que se ejecutarán en cada nodo de proceso en paralelo.

**Nodos de proceso:** los nodos de proceso son el motor que hay detrás de Almacenamiento de datos SQL. Son bases de datos SQL que almacenan los datos y procesan la consulta. Cuando se agregan datos, Almacenamiento de datos SQL distribuye las filas a los nodos de proceso. Los nodos de proceso son los trabajos que ejecutan las consultas en paralelo en los datos. Después del procesamiento, devuelven los resultados al nodo de control. Para finalizar la consulta, el nodo de control agrega los resultados y devuelve el resultado final.

**Almacenamiento:** los datos se almacenan en Almacenamiento de blobs de Azure. Cuando los nodos de proceso interactúan con los datos, escribir y leen directamente del Almacenamiento de blobs. Como Azure Storage se amplía de forma transparente e ilimitada, SQL Data Warehouse puede hacer lo mismo. Puesto que el proceso y el almacenamiento son independientes, Almacenamiento de datos SQL puede escalar automáticamente el almacenamiento por separado del escalado del proceso, y viceversa. El Almacenamiento de blobs de Azure es también totalmente tolerante a errores y simplifica el proceso de copia de seguridad y restauración.

**Servicio de movimiento de datos**: el Servicio de movimiento de datos (DMS) mueve los datos entre los nodos. DMS proporciona a los nodos de proceso acceso a los datos que necesitan para la combinaciones y agregaciones. DMS no es un servicio de Azure Es un servicio de Windows que se ejecuta junto con Base de datos SQL en todos los nodos. DMS es un proceso en segundo plano con el que no se va a interactuar directamente. Sin embargo, puede examinar los planes de consulta para observar cuando ocurren algunas operaciones de DMS, ya que el movimiento de datos es preciso para ejecutar cada consulta en paralelo.

## <a name="optimized-for-data-warehouse-workloads"></a>Optimizado para cargas de trabajo de almacenamiento de datos
Al enfoque de MPP también contribuyen varias optimizaciones del rendimiento específicas del almacenamiento de datos, entre ellas:

* Un optimizador de consultas distribuido y un conjunto de estadísticas complejas en todos los datos. Gracias a la información sobre la distribución y el tamaño de los datos, el servicio puede optimizar las consultas, para lo que evalúa del costo de las operaciones de consulta específicas.
* Técnicas y algoritmos avanzados integrados en el proceso de movimiento de datos, con el fin de mover datos de forma eficaz entre los recursos de proceso según sea necesario para realizar la consulta. Estas operaciones de movimiento de datos están integradas y todas las optimizaciones del servicio de movimiento de datos se producen de forma automática.
* Índices de **columnstore** agrupado de manera predeterminada. Mediante el uso del almacenamiento basado en columnas, Almacenamiento de datos SQL obtiene de media unas ganancias de compresión que quintuplican las del almacenamiento tradicional orientado a filas y que multiplica por 10 o más las del rendimiento de las consultas. Las consultas de análisis que necesitan examinar un gran número de filas funcionan mejor con índices de almacén de columnas.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Rendimiento predecible y escalable con unidades de almacenamiento de datos
SQL Data Warehouse se genera con tecnologías similares a SQL Database, lo que significa que los usuarios pueden esperar un rendimiento coherente y predecible para las consultas analíticas. Los usuarios deben esperar ver el rendimiento escalar linealmente cuando suman o restan nodos de proceso. La asignación de recursos a Almacenamiento de datos SQL se mide en Unidades de almacenamiento de datos (DWU). Las DWU son una medida de recursos subyacentes, como CPU, memoria y E/S por segundo, que se asignan a su Almacenamiento de datos SQL. El aumento del número de DWU aumenta los recursos y el rendimiento. En concreto, las DWU ofrecen las siguientes garantías:

* Puede escalar el almacenamiento de datos sin preocuparse por el hardware o el software subyacentes.
* Puede predecir la mejora del rendimiento para un nivel de DWU antes de cambiar el proceso del almacenamiento de datos.
* Puede cambiar o mover el hardware y el software subyacentes de la instancia sin que esto afecte al rendimiento de su carga de trabajo.
* Microsoft puede mejorar la arquitectura subyacente del servicio sin que el rendimiento de la carga de trabajo se vea afectado.
* Microsoft puede mejorar rápidamente el rendimiento de SQL Data Warehouse de una manera que sea escalable y que afecte de manera uniforme al sistema.

Las Unidades de almacenamiento de datos proporcionan una medida de tres métricas que están muy correlacionadas con el rendimiento de la carga de trabajo del almacenamiento de datos. Las siguientes métricas de carga de trabajo clave escalan linealmente con las DWU.

**Examen y agregación:** consulta de almacenamiento de datos estándar que examina un gran número de filas y realiza después una agregación compleja. Se trata de una operación de gran consumo de E/S y CPU.

**Carga:** capacidad de ingerir datos en el servicio. Las cargas se ejecutan mejor con PolyBase de Azure Storage Blob o Azure Data Lake. Esta métrica está diseñada para resaltar los aspectos de red y CPU del servicio.

**Create Table As Select (CTAS):** CTAS mide la posibilidad de copiar una tabla. Esto implica la lectura de datos del almacenamiento, su distribución entre los nodos del dispositivo y su nueva escritura en el almacenamiento. Se trata de una operación con un uso intensivo de CPU, E/S y red.

## <a name="built-on-sql-server"></a>Creado sobre SQL Server
Almacenamiento de datos SQL se basa en el motor de base de datos relacional de SQL Server e incluye muchas de las características que se esperan de un almacenamiento de datos empresarial. Si ya conoce T-SQL, le resultará muy sencillo transferir sus conocimientos a Almacenamiento de datos SQL. Ya sea un usuario avanzado o principiante, los ejemplos que aparecen en la documentación le servirán de apoyo para comenzar. En general, puede pensar en la forma en que hemos construido los elementos de lenguaje del Almacenamiento de datos SQL del modo siguiente:

* Almacenamiento de datos SQL emplea sintaxis de T-SQL para muchas operaciones. También admite un amplio conjunto de construcciones SQL tradicionales, como procedimientos almacenados, funciones definidas por el usuario, partición de tabla, índices e intercalaciones.
* SQL Data Warehouse también contiene varias características recientes de SQL Server, como los índices **columnstore** agrupados, la integración de PolyBase y la auditoría de datos (junto con la evaluación de amenazas).
* Puede que determinados elementos del lenguaje T-SQL, que son menos comunes para las cargas de trabajo de almacenamiento de datos, o que son más recientes para SQL Server, no se encuentren actualmente disponibles. Para más información, consulte la [documentación de migración][Migration documentation].

Con la uniformidad de Transact-SQL y las características comunes existentes entre SQL Server, Almacenamiento de datos SQL, Base de datos SQL y Sistema de plataforma de análisis, es posible desarrollar una solución que se ajuste a las necesidades de los datos. Puede decidir dónde guardar los datos, basándose en los requisitos de escala, seguridad y rendimiento y, a continuación, transferir los datos según sea necesario entre distintos sistemas.

## <a name="data-protection"></a>Protección de datos
Almacenamiento de datos SQL almacena todos los datos en Azure Premium mediante el almacenamiento con redundancia local. Se mantienen varias copias sincrónicas de los datos en el centro de datos local para garantizar la protección transparente de los datos frente a errores localizados. Además, SQL Data Warehouse realiza automáticamente una copia de seguridad de las bases de datos activas (no en pausa) a intervalos periódicos mediante instantáneas de Azure Storage. Para más información acerca de cómo usar las copias de seguridad y la restauración, consulte la [información general sobre la copia de seguridad y la restauración][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Integrado con herramientas de Microsoft
Almacenamiento de datos SQL también integra muchas de las herramientas con las que es posible que los usuarios de SQL Server ya estén familiarizados. Estas herramientas son:

**Herramientas tradicionales de SQL Server:** Almacenamiento de datos SQL se integra totalmente con SQL Server Analysis Services, Integration Services y Reporting Services.

**Herramientas basadas en la nube:** SQL Data Warehouse se puede integrar con varios servicios de Azure, como Data Factory, Stream Analytics, Machine Learning y Power BI. Para ver una lista completa, consulte la [información general sobre las herramientas integradas][Integrated tools overview].

**Herramientas de terceros:** muchos proveedores de herramientas de terceros ha certificado la integración de sus herramientas con SQL Data Warehouse. Para ver una lista completa, consulte los [asociados de soluciones de SQL Data Warehouse][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Escenarios de orígenes de datos híbridos
Polybase le permite aprovechar datos de orígenes diferentes con los mismos comandos conocidos de T-SQL. Polybase permite consultar los datos no relacionales que se mantienen en Almacenamiento de blobs de Azure como en una tabla normal. Utilice Polybase para consultar los datos no relacionales o para importar datos no relacionales en Almacenamiento de datos SQL.

* PolyBase utiliza tablas externas para tener acceso a datos no relacionales. Las definiciones de tabla se almacenan en Almacenamiento de datos SQL y se puede acceder a ellas mediante SQL y herramientas con las que normalmente accedería a datos relacionales normales.
* Polybase es independiente en su integración. Presenta las mismas características y funcionalidad para todos los orígenes compatibles. Los datos leídos por Polybase pueden estar en diversos formatos, incluidos archivos delimitados o ORC.
* PolyBase puede utilizarse para acceder al almacenamiento de blobs que también se emplea como almacenamiento para un clúster de HDInsight. Esto le otorga acceso a los mismos datos con herramientas relacionales y no relacionales.

## <a name="sla"></a>Contrato de nivel de servicio
SQL Data Warehouse ofrece un acuerdo de nivel de servicio (SLA) de nivel de producto como parte del SLA de Microsoft Online Services. Para más información, visite [SLA para SQL Data Warehouse][SLA for SQL Data Warehouse]. Para más información sobre el SLA de todos los demás productos, puede visitar la página de Azure [Acuerdos de nivel de servicio] o descargarlos en la página [Licencias por volumen][Volume Licensing]. 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya conoce un poco SQL Data Warehouse, aprenda a [crear una instancia de SQL Data Warehouse][create a SQL Data Warehouse] y [cargar datos de ejemplo de manera rápida][load sample data]. Si no está familiarizado con Azure, el [Glosario de Azure][Azure glossary] le puede resultar útil para consultar la nueva terminología que se encuentre. O bien, examine algunos de estos otros recursos de SQL Data Warehouse.  

* [Casos de éxito de clientes]
* [Blogs]
* [Solicitud de función]
* [Vídeos]
* [Blogs de Customer Advisory Team]
* [Creación de una incidencia de soporte técnico]
* [Foro de MSDN]
* [Foro Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creación de una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casos de éxito de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Acuerdos de nivel de servicio]: https://azure.microsoft.com/en-us/support/legal/sla/
