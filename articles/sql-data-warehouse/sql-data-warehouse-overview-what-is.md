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
ms.date: 10/31/2016
ms.author: jrj;mausher;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 6241eb0e7ea091dffcb0ae770f8d89f24a19eb67
ms.openlocfilehash: ff2f688d42924edb1596cb2db474a58748f2b44c


---
# <a name="what-is-azure-sql-data-warehouse"></a>¿Qué es Almacenamiento de datos SQL de Azure?
Almacenamiento de datos SQL de Azure es una base de datos de escalado horizontal y basada en la nube capaz de procesar volúmenes masivos de datos (tanto relacionales como no relacionales). Almacenamiento de datos SQL se basa en nuestra arquitectura de procesamiento paralelo masivo (MPP) y puede controlar la carga de trabajo de la empresa.

Almacenamiento de datos SQL

* Combina la base de datos relacional de SQL Server con las funcionalidades de escalado horizontal en la nube de Azure. Puede aumentar, reducir, pausar o reanudar el proceso en minutos o segundos. Al escalar horizontalmente la CPU cuando lo necesita, ahorra costos y reduce el uso durante las horas de poca actividad.
* Aprovecha la plataforma Azure. Es fácil de implementar, su mantenimiento no genera problemas y es totalmente tolerante a errores, gracias a las copias de seguridad automáticas.
* Es el complemento perfecto del ecosistema de SQL Server. Le permite desarrollar con el familiar lenguaje Transact-SQL de SQL Server (T-SQL) y herramientas conocidas.

En este artículo se describen las características más importantes de Almacenamiento de datos SQL.

## <a name="massively-parallel-processing-architecture"></a>Arquitectura de procesamiento paralelo masivo
El Almacenamiento de datos SQL es un sistema de base de datos distribuidas de procesamiento masivo en paralelo (MPP). Al dividir los datos y la funcionalidad de procesamiento entre varios nodos, Almacenamiento de datos SQL puede ofrecer una gran escalabilidad, mucha más que un único sistema.  En segundo plano, Almacenamiento de datos SQL distribuye los datos entre muchas unidades de procesamiento y almacenamiento shared-nothing. Los datos se almacenan en almacenamiento premium con redundancia local y están vinculados a los nodos de proceso para la ejecución de consultas. Con esta arquitectura, Almacenamiento de datos SQL adopta un enfoque de "divide y vencerás" para ejecutar cargas y consultas complejas. El nodo de control recibe las solicitudes, optimizadas y pasadas a los nodos de ejecución para hacer su trabajo en paralelo.

Mediante la combinación de nuestra arquitectura de MPP y las funcionalidades de almacenamiento de Azure, Almacenamiento de datos SQL puede:

* Aumentar o reducir el almacenamiento independiente del proceso.
* Aumentar o reducir el proceso sin mover datos.
* Pausar la funcionalidad de proceso y, al mismo tiempo, mantener los datos intactos.
* Reanudar la capacidad de proceso inmediatamente tras un aviso.

En el siguiente diagrama se muestra la arquitectura con más detalle.

![Arquitectura de Almacenamiento de datos SQL][1]

**Nodo de control:** el nodo de control administra y optimiza las consultas. Es el front-end que interactúa con todas las aplicaciones y conexiones. En Almacenamiento de datos SQL, el nodo de control tiene tecnología de Base de datos SQL y, al conectarse, adopta la misma apariencia. Debajo de la superficie, el nodo de control coordina todo el movimiento de datos y los cálculos requeridos para ejecutar consultas en paralelo de los datos distribuidos. Al enviar una consulta TSQL a Almacenamiento de datos SQL, el nodo de control la transforma en consultas independientes que se ejecutarán en cada nodo de proceso en paralelo.

**Nodos de proceso:** los nodos de proceso son el motor que hay detrás de Almacenamiento de datos SQL. Son bases de datos SQL que almacenan los datos y procesan la consulta. Cuando se agregan datos, Almacenamiento de datos SQL distribuye las filas a los nodos de proceso. Los nodos de proceso son los trabajos que ejecutan las consultas en paralelo en los datos. Después del procesamiento, devuelven los resultados al nodo de control. Para finalizar la consulta, el nodo de control agrega los resultados y devuelve el resultado final.

**Almacenamiento:** los datos se almacenan en Almacenamiento de blobs de Azure. Cuando los nodos de proceso interactúan con los datos, escribir y leen directamente del Almacenamiento de blobs. Como Azure Storage se amplía de forma transparente e ilimitada, SQL Data Warehouse puede hacer lo mismo. Puesto que el proceso y el almacenamiento son independientes, Almacenamiento de datos SQL puede escalar automáticamente el almacenamiento por separado del escalado del proceso, y viceversa. El Almacenamiento de blobs de Azure es también totalmente tolerante a errores y simplifica el proceso de copia de seguridad y restauración.

**Servicio de movimiento de datos**: el Servicio de movimiento de datos (DMS) mueve los datos entre los nodos. DMS proporciona a los nodos de proceso acceso a los datos que necesitan para la combinaciones y agregaciones. DMS no es un servicio de Azure Es un servicio de Windows que se ejecuta junto con Base de datos SQL en todos los nodos. Dado que DMS se ejecuta en segundo plano, el usuario no interactúa directamente con él. Sin embargo, al examinar los planes de consulta, observará que incluyen algunas operaciones de DMS, ya que para ejecutar cada consulta en paralelo, es preciso el movimiento de datos.

## <a name="optimized-for-data-warehouse-workloads"></a>Optimizado para cargas de trabajo de almacenamiento de datos
Al enfoque de MPP también contribuyen algunas optimizaciones del rendimiento específicas del almacenamiento de datos, entre ellas:

* Un optimizador de consultas distribuido y un conjunto de estadísticas complejas en todos los datos. Gracias a la información sobre la distribución y el tamaño de los datos, el servicio puede optimizar las consultas, para lo que evalúa del costo de las operaciones de consulta específicas.
* Técnicas y algoritmos avanzados integrados en el proceso de movimiento de datos, con el fin de mover datos de forma eficaz entre los recursos de proceso según sea necesario para realizar la consulta. Estas operaciones de movimiento de datos están integradas y todas las optimizaciones del servicio de movimiento de datos se producen de forma automática.
* Índices de **columnstore** agrupado de manera predeterminada. Mediante el uso del almacenamiento basado en columnas, Almacenamiento de datos SQL obtiene de media unas ganancias de compresión que quintuplican las del almacenamiento tradicional orientado a filas y que multiplica por 10 o más las del rendimiento de las consultas. Las consultas de Análisis que necesitan examinar un gran número de filas funcionan estupendamente en índices de almacén de columnas.

## <a name="predictable-and-scalable-performance"></a>Rendimiento predecible y escalable
Almacenamiento de datos SQL separa la ejecución y el almacenamiento, lo que permite escalar cada uno de manera independiente. Almacenamiento de datos SQL puede escalarse de manera rápida y sencilla para agregar recursos de ejecución adicionales en un instante. Como complemento, está el uso de Almacenamiento de blobs de Azure. Los blobs no solo proporcionan almacenamiento estable y replicado, sino también la infraestructura para una expansión más fluida y menos costosa. Mediante esta combinación de almacenamiento de escala en la nube y ejecución en Azure, Almacenamiento de datos SQL permite pagar por un almacenamiento y rendimiento de consultas cuando lo necesite. El cambio de la cantidad de ejecución es una operación muy simple, solo hay que mover un control deslizante del Portal de Azure hacia la derecha o izquierda; también se puede programar mediante T-SQL y PowerShell.

Junto con la capacidad de controlar totalmente la cantidad de la ejecución con independencia del almacenamiento, Almacenamiento de datos SQL permite realizar una pausa completa en el almacenamiento de datos, lo que significa que no pagará por la ejecución que no necesita. A la vez que mantiene el almacenamiento en su sitio, se lanzan todas las ejecuciones al grupo principal de Azure, lo que supone un ahorro de dinero. Cuando sea necesario, solo tendrá que reanudar el proceso y tener los datos y el proceso disponibles para su carga de trabajo.

## <a name="data-warehouse-units"></a>Unidades de almacenamiento de datos
La asignación de recursos a Almacenamiento de datos SQL se mide en Unidades de almacenamiento de datos (DWU). Las DWU son una medida de recursos subyacentes, como CPU, memoria y E/S por segundo, que se asignan a su Almacenamiento de datos SQL. El aumento del número de DWU aumenta los recursos y el rendimiento. En concreto, las DWU ofrecen las siguientes garantías:

* Puede escalar de forma sencilla el almacenamiento de datos sin preocuparse por el hardware o el software subyacentes.
* Puede predecir la mejora del rendimiento para un nivel de DWU antes de cambiar el tamaño del almacenamiento de datos.
* Puede cambiar o mover el hardware y el software subyacentes de la instancia sin que esto afecte al rendimiento de su carga de trabajo.
* Microsoft puede hacer ajustes en la arquitectura subyacente del servicio sin que el rendimiento de la carga de trabajo se vea afectado.
* Microsoft puede mejorar rápidamente el rendimiento de SQL Data Warehouse de una manera que sea escalable y que afecte de manera uniforme al sistema.

Las Unidades de almacenamiento de datos proporcionan una medida de tres métricas precisas que están muy correlacionadas con el rendimiento de la carga de trabajo del almacenamiento de datos. El objetivo es que las siguientes métricas clave de carga de trabajo aumenten linealmente con las DWU que ha elegido para el almacenamiento de datos.

**Examen y agregación:** esta métrica de carga de trabajo toma una consulta de almacenamiento de datos estándar que examina un gran número de filas y realiza después una agregación compleja. Se trata de una operación de gran consumo de E/S y CPU.

**Carga:** esta métrica mide la capacidad de insertar datos en el servicio. Las cargas se completan con PolyBase que carga un conjunto de datos representativo del Almacenamiento de blobs de Azure. Esta métrica está diseñada para resaltar los aspectos de red y CPU del servicio.

**Create Table As Select (CTAS):** CTAS mide la posibilidad de copiar una tabla. Esto implica la lectura de datos del almacenamiento, su distribución entre los nodos del dispositivo y su nueva escritura en el almacenamiento. Se trata de una operación con un uso intensivo de CPU, E/S y red.

## <a name="pause-and-scale-on-demand"></a>Pausa y escalado a petición
Cuando necesite resultados más rápidos, aumente el número de DWU y pague por un mayor rendimiento. Cuando necesite menos capacidad de proceso, reduzca el número de DWU y pague solo por lo que necesita. Se podría plantear cambiar las DWU en estos escenarios:

* Cuando no sea necesario ejecutar consultas, quizás por la noche o los fines de semana, desactive las consultas. Luego, pause los recursos de proceso para evitar pagar por las DWU cuando no las necesite.
* Cuando el sistema tenga poca demanda, considere la posibilidad de reducir las DWU a un tamaño pequeño. Seguirá teniendo acceso a los datos, pero con un considerable ahorro en los costos.
* Al realizar una operación de carga o de transformación de gran cantidad de datos, quizá desee escalar verticalmente para que los datos estén disponibles con mayor rapidez.

Para entender el valor ideal de su DWU, pruebe a escalar y reducir verticalmente y a ejecutar algunas consultas después de cargar los datos. Dado que el escalado se realiza rápidamente, puede intentar un número de distintos niveles de rendimiento en una hora o menos.  Tenga en cuenta que Almacenamiento de datos SQL está diseñado para procesar grandes cantidades de datos y para ver sus verdaderas funcionalidades de escala, especialmente en las escalas más grandes que ofrecemos, es conveniente utilizar un conjunto grande de datos que se aproxime o supere 1 TB.

## <a name="built-on-sql-server"></a>Creado sobre SQL Server
Almacenamiento de datos SQL se basa en el motor de base de datos relacional de SQL Server e incluye muchas de las características que se esperan de un almacenamiento de datos empresarial. Si ya conoce T-SQL, le resultará muy sencillo transferir sus conocimientos a Almacenamiento de datos SQL. Ya sea un usuario avanzado o principiante, los ejemplos que aparecen en la documentación le servirán de apoyo para comenzar. En general, puede pensar en la forma en que hemos construido los elementos de lenguaje del Almacenamiento de datos SQL del modo siguiente:

* Almacenamiento de datos SQL emplea sintaxis de T-SQL para muchas operaciones. También admite un amplio conjunto de construcciones SQL tradicionales, como procedimientos almacenados, funciones definidas por el usuario, partición de tabla, índices e intercalaciones.
* Almacenamiento de datos SQL también contiene varias características recientes de SQL Server, como los índices **columnstore** agrupados, la integración de PolyBase y la auditoría de datos (junto con la evaluación de amenazas).
* Puede que determinados elementos del lenguaje T-SQL, que son menos comunes para las cargas de trabajo de almacenamiento de datos, o que son más recientes para SQL Server, no se encuentren actualmente disponibles. Para más información, consulte la [documentación de migración][Migration documentation].

Con la uniformidad de Transact-SQL y las características comunes existentes entre SQL Server, Almacenamiento de datos SQL, Base de datos SQL y Sistema de plataforma de análisis, es posible desarrollar una solución que se ajuste a las necesidades de los datos. Puede decidir dónde guardar los datos, basándose en los requisitos de escala, seguridad y rendimiento y, a continuación, transferir los datos según sea necesario entre distintos sistemas.

## <a name="data-protection"></a>Protección de datos
Almacenamiento de datos SQL almacena todos los datos en Azure Premium mediante el almacenamiento con redundancia local. Se mantienen varias copias sincrónicas de los datos en el centro de datos local para garantizar la protección transparente de los datos en caso de errores localizados. Además, Almacenamiento de datos SQL realiza automáticamente una copia de seguridad de las bases de datos activas (no en pausa) a intervalos periódicos mediante instantáneas de Almacenamiento de Azure. Para más información acerca de cómo usar las copias de seguridad y la restauración, consulte la [información general sobre la copia de seguridad y la restauración][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Integrado con herramientas de Microsoft
Almacenamiento de datos SQL también integra muchas de las herramientas con las que es posible que los usuarios de SQL Server ya estén familiarizados. Entre ellas se incluyen las siguientes:

**Herramientas tradicionales de SQL Server:** Almacenamiento de datos SQL se integra totalmente con SQL Server Analysis Services, Integration Services y Reporting Services.

**Herramientas basadas en la nube:** Almacenamiento de datos SQL se puede usar con varias herramientas nuevas en Azure, como Data Factory, Análisis de transmisiones, Aprendizaje automático y Power BI. Para ver una lista completa, consulte la [información general sobre las herramientas integradas][Integrated tools overview].

**Herramientas de terceros:** un gran número de proveedores de herramientas de terceros ha certificado la integración de sus herramientas con Almacenamiento de datos SQL. Para ver una lista completa, consulte los [asociados de soluciones de SQL Data Warehouse][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Escenarios de orígenes de datos híbridos
El uso de SQL Data Warehouse con PolyBase ofrece a los usuarios la posibilidad única de trasladar los datos en su ecosistema y, con ello, de configurar escenarios híbridos avanzados con orígenes de datos no relacionales y locales.

Polybase le permite aprovechar datos de orígenes diferentes con los mismos comandos conocidos de T-SQL. Polybase permite consultar los datos no relacionales que se mantienen en Almacenamiento de blobs de Azure como en una tabla normal. Utilice Polybase para consultar los datos no relacionales o para importar datos no relacionales en Almacenamiento de datos SQL.

* PolyBase utiliza tablas externas para tener acceso a datos no relacionales. Las definiciones de tabla se almacenan en Almacenamiento de datos SQL y se puede acceder a ellas mediante SQL y herramientas con las que normalmente accedería a datos relacionales normales.
* Polybase es independiente en su integración. Presenta las mismas características y funcionalidad para todos los orígenes compatibles. Los datos leídos por Polybase pueden estar en una variedad de formatos, incluidos archivos delimitados o ORC.
* PolyBase puede utilizarse para acceder al almacenamiento de blobs que también se emplea como almacenamiento para un clúster de HDInsight. Esto le otorga acceso a los mismos datos con herramientas relacionales y no relacionales.

## <a name="sla"></a>Contrato de nivel de servicio
SQL Data Warehouse ofrece un acuerdo de nivel de servicio (SLA) de nivel de producto como parte del SLA de Microsoft Online Services. Para más información, visite [SLA para SQL Data Warehouse][SLA for SQL Data Warehouse]. Para más información sobre el SLA de todos los demás productos, puede visitar la página de Azure [Acuerdos de nivel de servicio] o descargarlos en la página [Licencias por volumen][Volume Licensing]. 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya conoce un poco SQL Data Warehouse, aprenda a [crear una instancia de SQL Data Warehouse][create a SQL Data Warehouse] y [cargar datos de ejemplo de manera rápida][load sample data]. Si no está familiarizado con Azure, el [Glosario de Azure][Azure glossary] le puede resultar útil para consultar la nueva terminología que se encuentre. O bien, examine algunos de estos otros recursos de Almacenamiento de datos de SQL.  

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
[Casos de éxito de clientes]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
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



<!--HONumber=Jan17_HO3-->


