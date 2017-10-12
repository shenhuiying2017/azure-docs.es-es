---
title: "Modelos de diseño para aplicaciones SaaS multiinquilino con Azure SQL Database | Microsoft Docs"
description: "En este artículo se describen los requisitos y modelos de arquitectura de datos comunes de las aplicaciones de base de datos multiinquilino que se ejecutan en un entorno de nube. Es necesario que los tenga en cuenta, así como las distintas ventajas e inconvenientes de estos modelos. También se explica cómo ayuda Base de datos SQL de Azure con sus grupos elásticos y herramientas elásticas a afrontar estos requisitos de forma garantizada."
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-design
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: 0f6ba62a01f3211ccaae6b6c48f72e0de54aad78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>Modelos de diseño para las aplicaciones SaaS multiinquilino y Azure SQL Database
En este artículo aprenderá sobre los requisitos y modelos de arquitectura de datos comunes de aplicaciones de base de datos de software como servicio (SaaS) multiinquilino que se ejecutan en un entorno de nube. También se explican los factores que debe tener en cuenta, así como las ventajas y desventajas de los distintos modelos de diseño. Los grupos y herramientas elásticos de Base de datos SQL de Azure pueden ayudarle a satisfacer necesidades específicas sin poner en peligro otros objetivos.

A veces, los desarrolladores toman decisiones que van en contra de sus intereses a largo plazo al diseñar los modelos de arrendamiento para las capas de datos de las aplicaciones multiinquilino. Inicialmente, al menos, un desarrollador puede percibir la facilidad de desarrollo y el menor costo del proveedor de servicios en la nube como algo más importante que el aislamiento de inquilinos o la escalabilidad de una aplicación. Esta opción puede provocar problemas de insatisfacción en los clientes y generar una costosa corrección del proceso posteriormente.

Una aplicación multiinquilino es una aplicación que se hospeda en un entorno de nube y proporciona el mismo conjunto de servicios a cientos o miles de inquilinos que no comparten ni ven los datos de los demás. Un ejemplo es una aplicación SaaS que proporciona servicios a los inquilinos en un entorno hospedado en la nube.

## <a name="multi-tenant-applications"></a>Aplicaciones multiinquilino
En aplicaciones multiinquilino, se pueden particionar fácilmente los datos y la carga de trabajo. Se pueden particionar los datos y la carga de trabajo, por ejemplo, a lo largo de los límites del inquilino puesto que la mayoría de las solicitudes se refieren a un inquilino. En este artículo se analiza qué propiedad es inherente en los datos y la carga de trabajo, y favorece a los modelos de aplicación.

Los desarrolladores utilizan este tipo de aplicación en todo el espectro de aplicaciones basadas en la nube, incluidas:

* Aplicaciones de bases de datos de asociados que se están pasando a la nube como aplicaciones SaaS
* Aplicaciones SaaS elaboradas para la nube desde cero
* Aplicaciones directas y orientadas a clientes
* Aplicaciones empresariales orientadas a empleados

Las aplicaciones SaaS que están diseñadas para la nube y aquellas con raíces como aplicaciones de bases de datos de asociados suelen ser aplicaciones multiinquilino. Estas aplicaciones de SaaS ofrecen una aplicación de software especializado como servicio a sus inquilinos. Los inquilinos pueden tener acceso al servicio de la aplicación y tener la propiedad total de los datos asociados almacenados como parte de la aplicación. Pero para poder aprovechar las ventajas de SaaS, sus inquilinos deben entregar un nivel de control sobre sus propios datos. Confían en el proveedor de SaaS para que los mantenga seguros y aislados de los datos de otros inquilinos. Ejemplos de este tipo de aplicación SaaS multiinquilino son MYOB, SnelStart y Salesforce.com. Cada una de estas aplicaciones se puede particionar a lo largo de los límites del inquilino y admite modelos de diseño de aplicación que se describen en este artículo.

Las aplicaciones que proporcionan un servicio directo a los clientes o empleados dentro de una organización (a menudo denominados usuarios, en lugar de inquilinos) constituyen otra categoría en el espectro de la aplicación multiinquilino. Los clientes se suscriben al servicio y no poseen los datos que el proveedor de servicios recopila y almacena. Los proveedores de servicios tienen requisitos menos estrictos para mantener los datos de sus clientes aislados entre sí, aparte de los impuestos por el gobierno sobre normas de privacidad. Ejemplos de este tipo de aplicación multiinquilino orientada al cliente son proveedores de contenido multimedia como Netflix, Spotify y Xbox LIVE. Otros ejemplos de aplicaciones en las que se pueden crear particiones fácilmente son aquellas para Internet orientadas al cliente o las aplicaciones de Internet de las cosas (IoT), en las que cada cliente o dispositivo sirve de partición. Los límites de partición pueden separar a los usuarios y los dispositivos.

No todas las aplicaciones se particionan fácilmente a lo largo de una sola propiedad como inquilino, cliente, usuario o dispositivo. Una aplicación de planeación de recursos empresariales (ERP) compleja, por ejemplo, tiene productos, pedidos y clientes. Suele tener una estructura compleja con miles de tablas muy interconectadas.

Ninguna estrategia de partición única se puede aplicar a todas las tablas ni puede funcionar con la carga de trabajo de una aplicación. Este artículo se centra en las aplicaciones multiinquilino que tienen cargas de trabajo y datos que se pueden particionar fácilmente.

## <a name="multi-tenant-application-design-trade-offs"></a>Ventajas y desventajas del diseño de las aplicaciones multiinquilino
El modelo de diseño que suele elegir un desarrollador de aplicaciones multiinquilino debe tener en cuenta los siguientes factores:

* **Aislamiento de inquilinos**. El desarrollador debe garantizar que ningún inquilino obtenga un acceso no deseado a los datos de otros inquilinos. Este requisito de aislamiento se extiende a otras propiedades como protección contra vecinos ruidosos, capacidad de restaurar los datos de un inquilino, implementación de personalizaciones específicas de inquilino, etc.
* **Costo de los recursos en la nube**. Una aplicación SaaS debe ser rentable. Es posible que un desarrollador de aplicaciones multiinquilino desee obtener el costo más bajo en el uso de recursos en la nube, como los costos de almacenamiento y de proceso.
* **Facilidad de DevOps**. Un desarrollador de aplicaciones multiinquilino debe incorporar protección de aislamiento, mantener y supervisar el estado del esquema de aplicaciones y de bases de datos y resolver problemas de inquilino. La complejidad del desarrollo y del funcionamiento de las aplicaciones se traduce directamente en un aumento del costo y una menor satisfacción del inquilino.
* **Escalabilidad**. La capacidad de agregar gradualmente más inquilinos y la capacidad para los inquilinos que la necesiten son imperativas para el correcto funcionamiento de SaaS.

Cada uno de estos factores presenta ventajas y desventajas en comparación con los demás. Es posible que la opción de nube de menor costo no ofrezca la experiencia de desarrollo más práctica. Es importante para un desarrollador tomar decisiones meditadas sobre estas opciones, así como sus ventajas y desventajas, durante el proceso de diseño de aplicaciones.

Un modelo de desarrollo conocido es empaquetar varios inquilinos en una o varias bases de datos. Las ventajas de este enfoque son un costo más bajo, porque se paga por un pequeño número de bases de datos, y la sencillez relativa de trabajar con un número limitado de bases de datos. Pero con el tiempo, un desarrollador de aplicaciones multiinquilino SaaS se dará cuenta de que esta opción presenta grandes inconvenientes en lo relativo a aislamiento de inquilinos y escalabilidad. Si el aislamiento de inquilinos es importante, se requiere un mayor esfuerzo para proteger los datos de inquilinos en el almacenamiento compartido contra el acceso no autorizado o vecinos ruidosos. Este esfuerzo adicional puede aumentar de forma significativa los esfuerzos de desarrollo y los costos de mantenimiento del aislamiento. De igual forma, si es necesario agregar inquilinos, este modelo de diseño suele requerir conocimientos para redistribuir los datos de inquilinos en todas las bases de datos correctamente a fin de escalar la capa de datos de una aplicación.  

A menudo el aislamiento de inquilinos es un requisito fundamental en las aplicaciones multiinquilino SaaS dirigidas a empresas y organizaciones. A un desarrollador pueden seducirle más las ventajas percibidas con respecto a la sencillez y el costo que el aislamiento de inquilinos y la escalabilidad. Esta opción resulta compleja y costosa, según crece el servicio y los requisitos de aislamiento de inquilinos se vuelven más importantes y necesitan administrarse a nivel de aplicación. Sin embargo, para las aplicaciones multiinquilino que proporcionan un servicio orientado al consumidor directo para los clientes, el aislamiento de inquilinos puede perder prioridad frente a la optimización del costo de los recursos en la nube.

## <a name="multi-tenant-data-models"></a>Modelos de datos multiinquilino
Las prácticas de diseño comunes para colocar los datos del inquilino siguen tres modelos distintos que se muestran en la Figura 1.

![Modelos de datos de aplicaciones multiinquilino](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

Figura 1: Prácticas de diseño comunes para los modelos de datos multiinquilino

* **Base de datos por inquilino**. Cada inquilino tiene su propia base de datos. Todos los datos específicos del inquilino se limitan a su base de datos, y están aislados de otros inquilinos y sus datos.
* **Bases de datos divididas compartidas**. Varios inquilinos comparten una de varias bases de datos. Se asigna un conjunto distinto de inquilinos a cada base de datos mediante una estrategia de partición como particiones hash, por intervalos o listas. Esta estrategia de distribución de datos a menudo se conoce como particionamiento.
* **Única base de datos compartida**. Una única base de datos, a veces grande, contiene datos de todos los inquilinos, cuya ambigüedad se elimina con una columna de identificador de inquilino.

> [!NOTE]
> Un desarrollador de aplicaciones puede optar por colocar a varios inquilinos en esquemas de bases de datos diferentes y utilizar luego el nombre del esquema para eliminar la ambigüedad de los distintos inquilinos. No es un enfoque recomendado, ya que normalmente requiere el uso de SQL dinámico y no puede ser eficaz en el almacenamiento en caché de plan. El resto de este artículo se centra en el enfoque de tabla compartida para esta categoría de aplicación multiinquilino.
> 
> 

## <a name="popular-multi-tenant-data-models"></a>Modelos de datos multiinquilino más conocidos
Es importante evaluar los distintos tipos de modelos de datos multiinquilino en cuanto a ventajas y desventajas del diseño de la aplicación que ya se han identificado. Estos factores ayudan a caracterizar los tres modelos de datos multiinquilino más comunes descritos anteriormente y su uso de la base de datos tal como se muestra en la Figura 2.

* **Aislamiento**. El grado de aislamiento entre los inquilinos puede ser una medida de cuánto aislamiento logra un modelo de datos.
* **Costo de los recursos en la nube**. La cantidad de recursos compartidos entre los inquilinos puede optimizar el costo de los recursos en la nube. Un recurso puede definirse como el costo de proceso y de almacenamiento.
* **Costo de DevOps**. La facilidad de desarrollo de aplicaciones, la implementación y la administración reducen el costo operativo global de SaaS.  

En la Figura 2, el eje Y muestra el nivel de aislamiento de los inquilinos. El eje X muestra el nivel de uso compartido de recursos. La flecha diagonal gris en medio indica la dirección de los costos de DevOps que tienden a aumentar o disminuir.

![Modelos de diseño de aplicaciones multiinquilino más conocidos](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

Figure 2: Modelos de datos multiinquilino más conocidos

El cuadrante inferior derecho en la Figura 2 muestra un modelo de aplicación que utiliza una única base de datos compartida potencialmente grande y el enfoque de tabla compartida (o esquema independiente). Se recomienda para el uso compartido de recursos porque todos los inquilinos utilizan los mismos recursos de base de datos (CPU, memoria, entrada y salida) en una única base de datos. Sin embargo, el aislamiento de inquilinos es limitado. Tendrá que realizar otros pasos para proteger los inquilinos unos de otros en la capa de aplicación. Estos pasos adicionales pueden aumentar significativamente el costo de las operaciones de DevOps de desarrollo y administración de la aplicación. La escalabilidad está limitada por la escala del hardware que hospeda la base de datos.

El cuadrante inferior izquierdo en la Figura 2 ilustra varios inquilinos particionados en varias bases de datos (las unidades de escalado de hardware suelen diferir). Cada base de datos hospeda un subconjunto de inquilinos, que soluciona el problema de escalabilidad de otros modelos. Si se requiere más capacidad para un número mayor de inquilinos, puede colocarlos fácilmente en nuevas bases de datos asignadas a nuevas unidades de escalado de hardware. No obstante, se reduce la cantidad de recursos compartidos. Solo comparten recursos los inquilinos colocados en las mismas unidades de escala. Este enfoque supone pocas mejoras de aislamiento de inquilinos, ya que muchos inquilinos todavía siguen colocando sin estar protegidos automáticamente de las acciones de los demás. La complejidad de la aplicación permanece alta.

El cuadrante superior izquierdo de la Figura 2 es el tercer enfoque. Coloca los datos de cada inquilino en su propia base de datos. Este enfoque tiene unas propiedades de aislamiento de inquilinos excelentes pero limita el uso compartido de recursos cuando cada base de datos tiene sus propios recursos específicos. Es un buen enfoque si todos los inquilinos tienen cargas de trabajo predecibles. Si las cargas de trabajo de inquilinos son cada vez menos predecibles, el proveedor no puede optimizar el uso compartido de recursos. La imprevisibilidad es habitual entre las aplicaciones SaaS. El proveedor debe aprovisionar en exceso para satisfacer las demandas o reducir los recursos. Ambas acciones provocan unos costos mayores o una menor satisfacción de los inquilinos. Es preferible un mayor nivel de uso compartido entre los inquilinos para que la solución sea más rentable. El aumento del número de bases de datos también aumenta el costo de DevOps de implementación y mantenimiento de la aplicación. A pesar de estas cuestiones, este método proporciona el mejor aislamiento y el más fácil para los inquilinos.

Estos factores también influyen en el modelo de diseño que elige un cliente:

* **Propiedad de los datos de los inquilinos**. Una aplicación en la que los inquilinos conservan la propiedad de sus propios datos favorece el modelo de una base de datos única por inquilino.
* **Escala**. Una aplicación destinada a cientos de miles o millones de inquilinos favorece los enfoques de uso compartido de bases de datos como el particionamiento. Los requisitos de aislamiento pueden seguir planteando dificultades.
* **Modelo y valor empresarial**. Si los ingresos por inquilino de una aplicación son reducidos (menos de un dólar), los requisitos de aislamiento son menos críticos y tiene sentido compartir la base de datos. Si los ingresos por inquilino son algunos dólares o más, es más factible usar un modelo de base de datos por inquilino. Puede resultar útil reducir los costos de desarrollo.

Dadas las ventajas y desventajas del diseño que se muestran en la Figura 2, un modelo multiinquilino ideal debe incorporar unas buenas propiedades de aislamiento de inquilinos con un uso compartido óptimo de recursos entre inquilinos. Se trata de un modelo que se ajusta a la categoría descrita en el cuadrante superior derecho de la Figura 2.

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Compatibilidad con la arquitectura multiinquilino en Azure SQL Database
Azure SQL Database es compatible con todos los modelos de aplicaciones multiinquilino descritos en la Figura 2. Con los grupos elásticos, también admite un modelo de aplicación que combina un uso compartido excelente de los recursos y las ventajas de aislamiento del enfoque de base de datos por inquilino (consulte el cuadrante superior derecho en la Figura 3). Las herramientas de bases de datos elásticas y las funcionalidades de Base de datos SQL pueden ayudar a reducir el costo de desarrollo y funcionamiento de una aplicación que tiene numerosas bases de datos (mostrada en el área sombreada de la Figura 3). Estas herramientas pueden ayudarle a crear y administrar aplicaciones que usan cualquiera de los modelos de bases de datos multiinquilino.

![Modelos de Base de datos SQL de Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

Figura 3: Modelos de aplicaciones multiinquilino en Azure SQL Database

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modelo de base de datos por inquilino con grupos elásticos y herramientas
Los grupos elásticos de Base de datos SQL combinan el aislamiento de inquilinos con recursos compartidos entre las bases de datos de los inquilinos para mejorar la compatibilidad con el enfoque de base de datos por inquilino. SQL Database es una solución de capa de datos para los proveedores de SaaS que crean aplicaciones multiinquilino. La carga del uso compartido de recursos entre inquilinos se desplaza desde la capa de la aplicación a la capa de servicio de base de datos. La complejidad de administración y de consulta a escala entre bases de datos se simplifica con trabajos, consultas y transacciones elásticos, y la biblioteca de cliente de base de datos elástica.

| Requisitos de la aplicación | Funcionalidades de Base de datos SQL |
| --- | --- |
| Aislamiento de inquilinos y uso compartido de recursos |[Grupos elásticos](sql-database-elastic-pool.md): asigna un grupo de recursos de Base de datos SQL y los comparte entre varias bases de datos. Además, las bases de datos individuales pueden sacar todos los recursos necesarios del grupo para acomodar los picos de demanda de capacidad debido a cambios en las cargas de trabajo de los inquilinos. El grupo elástico sí se puede escalar hacia arriba o hacia abajo según sea necesario. Los grupos elásticos también facilitan la administración, la supervisión y la solución de problemas a nivel de grupo. |
| Facilidad de DevOps entre bases de datos |[Grupos elásticos](sql-database-elastic-pool.md): como se indica anteriormente. |
| | [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md): consulta entre bases de datos para informes o análisis entre inquilinos. |
| | [Trabajos elásticos](sql-database-elastic-jobs-overview.md): empaqueta e implementa de forma fiable las operaciones de mantenimiento de bases de datos o los cambios de esquema de base de datos en varias bases de datos. |
| | [Transacciones elásticas](sql-database-elastic-transactions-overview.md): procesa los cambios en varias bases de datos de forma atómica y aislada. Las transacciones elásticas son necesarias cuando las aplicaciones necesitan garantías de "todo o nada" para varias operaciones de base de datos. |
| | [Biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md): administra distribuciones de datos y asigna inquilinos a bases de datos. |

## <a name="shared-models"></a>Modelos compartidos
Como se describió anteriormente, para la mayoría proveedores de SaaS, el enfoque de modelo compartido puede plantear problemas con el aislamiento de los inquilinos, así como complejidades con el desarrollo y mantenimiento de la aplicación. Sin embargo, para las aplicaciones multiinquilino que proporcionan un servicio directamente a los consumidores, los requisitos de aislamiento de inquilinos pueden no tener tanta prioridad como la reducción de los costos. Pueden empaquetar los inquilinos de una o más bases de datos a alta densidad para reducir los costos. Los modelos de base de datos compartida con una o varias bases de datos particionadas pueden agregar eficacia al uso compartido de recursos y reducir el costo general. La Base de datos SQL de Azure proporciona algunas características que ayudan a los clientes a crear aislamiento para la seguridad y la administración mejoradas a escala en la capa de datos.

| Requisitos de la aplicación | Funcionalidades de Base de datos SQL |
| --- | --- |
| Características de aislamiento de seguridad |[Seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131.aspx) |
| [Crear un esquema de la base de datos](https://msdn.microsoft.com/library/dd207005.aspx) | |
| Facilidad de DevOps entre bases de datos |[Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| | [Trabajos elásticos](sql-database-elastic-jobs-overview.md) |
| | [Transacciones elásticas](sql-database-elastic-transactions-overview.md) |
| | [Biblioteca de cliente de base de datos elástica](sql-database-elastic-database-client-library.md) |
| | [Dividir y combinar bases de datos elásticas](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Resumen
Los requisitos de aislamiento de inquilinos son importantes para la mayoría de las aplicaciones SaaS multiinquilino. La mejor opción para proporcionar aislamiento se basa principalmente en el enfoque de base de datos por inquilino. Los otros dos enfoques requieren inversiones en capas de aplicación complejas que requieren personal de desarrollo especializado para proporcionar aislamiento, lo cual aumenta significativamente el costo y el riesgo. Si los requisitos de aislamiento no se tienen en cuenta temprano en el desarrollo de los servicios, puede ser incluso más costoso en los dos primeros modelos ajustarlos con carácter retroactivo. Los principales inconvenientes asociados con el modelo de base de datos por inquilino están relacionados con los mayores costos de los recursos en la nube debidos al reducido uso compartido y el mantenimiento y la administración de gran cantidad de bases de datos. Los desarrolladores de aplicaciones SaaS tienen a menudo problemas con estos aspectos.

Aunque estos aspectos pueden ser obstáculos importantes para la mayoría de proveedores de servicios de bases de datos en la nube, la Base de datos SQL de Azure elimina las barreras con sus funcionalidades de bases de datos elásticas y grupos elásticos. Los desarrolladores de SaaS pueden combinar las características de aislamiento del modelo de base de datos por inquilino y optimizar el uso compartido de recursos y las mejoras de administración de un gran número de bases de datos mediante grupos elásticos y herramientas asociadas.

Los proveedores de aplicaciones multiinquilino sin requisitos de aislamiento de inquilinos y que pueden empaquetar los inquilinos de una base de datos a alta densidad, podrían encontrarse con que los modelos de datos compartidos proporcionan eficacia adicional en el uso compartido de recursos y reducen el costo total. Las herramientas de bases de datos elásticas de Azure SQL Database, las bibliotecas de particionamiento y las características de seguridad ayudan a los proveedores de SaaS a crear y administrar las aplicaciones multiinquilino.

## <a name="next-steps"></a>Pasos siguientes
[Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md) para ver una aplicación de ejemplo que demuestra la biblioteca de cliente.

Cree un [panel personalizado de grupo elástico para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) con una aplicación de ejemplo que use grupos elásticos para una solución de base de datos escalable y rentable.

Use las herramientas de Base de datos SQL de Azure para [migrar bases de datos existentes de escalado horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).

Para crear y administrar un grupo elástico mediante Azure Portal, consulte [cómo crear un grupo elástico](sql-database-elastic-pool-manage-portal.md).  

Aprenda a [supervisar y administrar un grupo elástico](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Implementación y exploración de una aplicación SaaS multiinquilino que usa Azure SQL Database](sql-database-saas-tutorial.md)
* [¿Qué es un grupo elástico de Azure?](sql-database-elastic-pool.md)
* [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md)
* [Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Autenticación en aplicaciones multiinquilino mediante Azure Active Directory y OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Aplicación Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>Preguntas y solicitudes de características

Si tiene preguntas, nos encontrará en el [foro de Base de datos SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Agregue una solicitud de característica en el [foro de comentarios de Base de datos SQL](https://feedback.azure.com/forums/217321-sql-database/).

