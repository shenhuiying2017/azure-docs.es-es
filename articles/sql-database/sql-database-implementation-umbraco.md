---
title: "Caso práctico de Azure SQL Database: Umbraco | Microsoft Docs"
description: "Sepa cómo Umbraco utiliza SQL Database para aprovisionar y escalar rápidamente servicios para miles de inquilinos en la nube."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 6e9c56874bf4bda7f4248a44e274532ed2555153
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco utiliza Azure SQL Database para aprovisionar y escalar rápidamente servicios para miles de inquilinos en la nube
![Logotipo de Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco es un famoso sistema de administración de contenido de código abierto (CMS) que puede ejecutar todo tipo de recursos —desde sitios para folletos o campañas de poca envergadura hasta aplicaciones complejas— para sitios web multimedia internacionales y empresas de la lista Fortune 500. 

> "Tenemos una comunidad de desarrolladores bastante grande que usan el sistema: más de 100 000 programadores en nuestros foros y más de 350 000 sitios activos que ejecutan Umbraco".
> 
> Morten Christensen, director técnico de Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Para simplificar las implementaciones de clientes, Umbraco agregó Umbraco as a Service (UaaS), una oferta SaaS que elimina la necesidad de realizar implementaciones locales, proporciona escalado integrado, y acaba con la sobrecarga de administración, ya que permite a los desarrolladores centrarse en las innovaciones de productos en lugar de en la administración de soluciones. Umbraco puede brindar todas estas ventajas empleando el modelo flexible de plataforma como servicio (PaaS) que ofrece Microsoft Azure.

UaaS permite a los clientes de SaaS utilizar las funcionalidades CMS de Umbraco que anteriormente estaban fuera de su alcance. Estos clientes se aprovisionan con un entorno CMS de trabajo que incluye una base de datos de producción. Asimismo, pueden agregar hasta dos bases de datos más para los entornos de ensayo y desarrollo, en función de sus requisitos. Cuando se solicita un nuevo entorno, un proceso automatizado asigna automáticamente ese cliente a una base de datos. La nueva base de datos está lista en cuestión de segundos debido a que Umbraco la aprovisionó previamente desde un grupo elástico de Azure de bases de datos disponibles (consulte la figura 1).

![Ciclo de vida de aprovisionamiento de Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Aprovisionamiento del ciclo de vida de Umbraco as a Service (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>La automatización y los grupos elásticos de Azure simplifican las implementaciones
Con Azure SQL Database y otros servicios de Azure, los clientes de Umbraco pueden aprovisionar automáticamente sus entornos. Además, Umbraco puede supervisar y administrar con facilidad las bases de datos como parte de un flujo de trabajo intuitivo:

1. Aprovisionamiento
   
   Umbraco mantiene una capacidad de 200 bases de datos aprovisionadas previamente de grupos elásticos. Cuando un nuevo cliente se suscribe a UaaS, Umbraco le proporciona un nuevo entorno CMS en tiempo casi real asignándole una base de datos del grupo de disponibilidad.
   
   Cuando un grupo de disponibilidad alcanza su umbral, se crea un nuevo grupo elástico y se aprovisionan previamente nuevas bases de datos que se asignan a los clientes según sea necesario.
   
   La implementación se automatiza por completo mediante el uso de bibliotecas de administración en C# y colas de Azure Service Bus.
2. Uso
   
   Los clientes usan entre uno y tres entornos (producción, ensayo o desarrollo), cada uno de ellos con su propia base de datos. Las bases de datos de clientes se encuentran en grupos elásticos, lo que permite a Umbraco proporcionar una escalabilidad eficaz sin necesidad de aprovisionar en exceso.
   
   ![Información general del proyecto de Umbraco](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Detalles del proyecto de Umbraco](./media/sql-database-implementation-umbraco/figure3.png)
   
   Ilustración 2. Sitio web de cliente de Umbraco as a Service (UaaS), que muestra los detalles y un resumen del proyecto
   
   Azure SQL Database utiliza unidades de transacción de bases de datos (DTU) para representar la potencia relativa necesaria para las transacciones de la base de datos real. En cuanto a los clientes de UaaS, las bases de datos suelen utilizar aproximadamente 10 DTU, pero cada una de ellas tiene la elasticidad necesaria para poder escalarse cuando se precise. Es decir, UaaS puede garantizar que los clientes siempre tengan los recursos necesarios, incluso durante las horas pico. Por ejemplo, hace poco, durante un acontecimiento deportivo un domingo por la noche, un cliente de UaaS experimentó picos en las bases de datos de hasta 100 DTU durante el transcurso del partido. Gracias a los grupos elásticos de Azure, Umbraco pudo responder a esa alta demanda sin que el rendimiento se viese menoscabado.
3. Supervisión
   
   Umbraco supervisa la actividad de las bases de datos en Azure Portal, además de las alertas de correo electrónico personalizadas, mediante el uso de paneles.
4. Recuperación ante desastres
   
   Azure ofrece dos opciones de recuperación ante desastres (DR): la replicación geográfica activa y la restauración geográfica. La opción de recuperación ante desastres que debe elegir una empresa depende de sus [objetivos de continuidad empresarial](sql-database-business-continuity.md).
   
   La replicación geográfica activa proporciona el nivel más rápido de respuesta en caso de que se produzcan tiempos de inactividad. Con la replicación geográfica activa, puede crear hasta cuatro elementos secundarios legibles en servidores de diferentes regiones y, después, iniciar la conmutación por error en cualquiera de los elementos secundarios en caso de errores.
   
   Umbraco no necesita la replicación geográfica, pero sí que utiliza la restauración geográfica de Azure para ayudar a garantizar que los tiempos de inactividad sean mínimos en caso de una interrupción. La restauración geográfica se basa en copias de seguridad de bases de datos en el almacenamiento de Azure con redundancia geográfica, que permite a los usuarios restaurar datos a partir de una copia de seguridad cuando hay una interrupción en la región primaria.
5. Desaprovisionamiento
   
   Cuando se elimina un entorno de proyecto, se quitan las bases de datos asociadas (desarrollo, ensayo o producción) durante la limpieza de colas de Azure Service Bus. Este proceso automatizado restaura las bases de datos sin utilizar al grupo de disponibilidad de bases de datos elásticas de Umbraco, con lo que quedan disponibles para realizar futuros aprovisionamientos al mismo tiempo que se mantiene un nivel de uso máximo.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Los grupos elásticos permiten a UaaS realizar escalados con facilidad
Al aprovechar las ventajas que ofrecen los grupos elásticos de Azure, Umbraco puede optimizar el rendimiento para sus clientes sin necesidad de provocar el exceso o la falta de aprovisionamiento. Actualmente, Umbraco tiene casi 3000 bases de datos en 19 grupos elásticos, y puede escalarlas fácilmente según sea necesario para satisfacer las necesidades de sus 325 000 clientes existentes o nuevos que están listos para implementar un CMS en la nube.

De hecho, según Morten Christensen, el responsable técnico de Umbraco: "UaaS está experimentando ahora un crecimiento de, prácticamente, 30 nuevos clientes al día. Nuestros clientes están encantados con la comodidad de poder aprovisionar nuevos proyectos en segundos, publicar al instante actualizaciones en sus sitios activos desde un entorno de desarrollo mediante el modelo de implementación de un solo clic, y realizar cambios a gran velocidad si encuentran errores".

Si un cliente no vuelve a necesitar un segundo o tercero entorno, puede eliminarlos. De este modo, se liberan recursos, que pueden emplearse para otros clientes como parte del grupo de disponibilidad de bases de datos elásticas de Umbraco.

![Arquitectura de implementación de Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Arquitectura de implementación de UaaS en Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>La migración del centro de datos a la nube
Al principio, cuando los desarrolladores de Umbraco decidieron realizar la migración a un modelo SaaS, sabían que necesitarían una forma rentable y escalable de crear el servicio.

> "Los grupos elásticos son ideales para nuestra oferta de SaaS, ya que podemos ampliar y reducir la capacidad según sea necesario. El aprovisionamiento se realiza de forma sencilla, y con nuestra configuración, podemos mantener un nivel de uso máximo".
> 
> Morten Christensen, director técnico de Umbraco
> 
> 

"Queríamos dedicar nuestro tiempo a solucionar los problemas de nuestros clientes, no en administrar la infraestructura. Nuestro deseo era que nuestros clientes sacasen el máximo partido de nuestra solución", afirma Niels Hartvig, fundador de Umbraco. "Al principio, consideramos la posibilidad de hospedar nosotros mismos los servidores, pero planear la capacidad habría sido una pesadilla". Casualmente, Umbraco no emplea administradores de base de datos, lo que destaca una propuesta de valor importante para utilizar UaaS.

Un objetivo clave para los desarrolladores de Umbraco consistió en ofrecer a los clientes de UaaS una manera de aprovisionar clientes de manera rápida y sin limitaciones de capacidad. Sin embargo, para proporcionar un servicio hospedado específico en los centros de datos de Umbraco, se habría necesitado una gran cantidad de capacidad para controlar los picos de procesamiento. Es decir, tendrían que haber agregado una infraestructura de proceso considerable que se habría infrautilizado constantemente.

Además, el equipo de desarrollo de Umbraco buscaba una solución que les permitiera reutilizar la mayor cantidad posible del código existente. Como desarrollador de Umbraco, Mikkel Madsen explica: "Estábamos satisfechos y familiarizados con las herramientas de desarrollo de Microsoft, como Microsoft SQL Server, Microsoft Azure SQL Database, ASP.net e Internet Information Services (IIS). Antes de invertir en una solución en la nube IaaS o PaaS, queríamos asegurarnos de que sería compatible con nuestras herramientas y plataformas de Microsoft, ya que así no tendríamos que realizar cambios masivos en nuestro código base".

Para cumplir todos los criterios, Umbraco buscó un asociado de soluciones en la nube que cumplirse los siguientes recursos:

* Capacidad y confiabilidad suficientes
* Compatibilidad con las herramientas de desarrollo de Microsoft, para que los ingenieros de Umbraco no se vieran forzados a reinventar por completo su entorno de desarrollo
* Presencia en todos los mercados geográficos en los que UaaS compite (las empresas necesitan asegurarse de que pueden acceder a sus datos rápidamente y se almacenen en una ubicación que cumpla sus requisitos normativos regionales)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Por qué Umbraco eligió Azure para UaaS
Según Morten Christensen: "Después de considerar todas nuestras opciones, hemos seleccionado Azure porque cumplió todos nuestros criterios, desde la capacidad de administración y escalabilidad hasta la familiaridad y rentabilidad. Configuramos los entornos de máquinas virtuales de Azure y cada uno de ellos tiene su propia instancia de Azure SQL Database; todas las instancias se encuentran en grupos elásticos. Al separar las bases de datos entre los entornos de desarrollo, ensayo y producción, podemos ofrecer a nuestros clientes un aislamiento de rendimiento sólido que puede escalarse, lo que supone para nosotros una grandísima ventaja".

Morten continúa: "Antes, teníamos que aprovisionar servidores de bases de datos web manualmente. Ahora, no tenemos que pensar en ello. Todo está automatizado, desde el aprovisionamiento hasta las tareas de limpieza".

Morten también está satisfecho con las funcionalidades de escalado que proporciona Azure. "Los grupos elásticos son ideales para nuestra oferta de SaaS, ya que podemos ampliar y reducir la capacidad según sea necesario. El aprovisionamiento se realiza de forma sencilla, y con nuestra configuración, podemos mantener un nivel de uso máximo". Morten afirma: "La sencillez de los grupos elásticos, junto con la garantía de las DTU basadas en niveles de servicio, nos proporciona la capacidad de aprovisionar nuevos grupos de recursos a petición. Hace poco, uno de nuestros clientes más importantes alcanzó un pico de 100 DTU en su entorno de producción. Al utilizar Azure, nuestros grupos elásticos proporcionan a las bases de datos de clientes los recursos que necesitan en tiempo real sin tener que predecir los requisitos de DTU. En resumen, nuestros clientes obtienen el tiempo de respuesta que esperan y podemos cumplir nuestros acuerdos de nivel de servicio de rendimiento".

Tal y como resume Mikkel Madsen: "Hemos adoptado el eficaz algoritmo de Azure que conecta un escenario SaaS común (incorporando a escala nuevos clientes en tiempo real) a nuestro patrón de aplicaciones (aprovisionando previamente bases de datos de los entornos de desarrollo y producción) en la tecnología subyacente (mediante las colas de Azure Service Bus y Azure SQL Database)".

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Con Azure, UaaS supera las expectativas de los clientes
Desde que Umbraco eligió Azure como asociado de soluciones en la nube, ha podido brindar a los clientes de UaaS un rendimiento optimizado de administración de contenido, sin invertir en los recursos de TI que se necesitaría con una solución autohospedada. Como Morten explica: "Nos encanta la mayor escalabilidad y comodidad de desarrollo que nos ofrece Azure, y nuestros clientes están entusiasmados con las características y la confiabilidad que brinda. En líneas generales, nos ha aportado un sinfín de ventajas".

## <a name="more-information"></a>Más información
* Para obtener más información sobre los grupos elásticos de Azure, consulte [este artículo](sql-database-elastic-pool.md).
* Si necesita más información sobre Azure Service Bus, lea [este artículo](https://azure.microsoft.com/services/service-bus/).
* Para obtener más detalles sobre los roles web y de trabajo, consulte [este artículo](../fundamentals-introduction-to-azure.md#compute).    
* Para aprender más sobre las redes virtuales en Azure, revise [este artículo](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Para conocer más detalles sobre la copia de seguridad y recuperación, vea [este artículo](sql-database-business-continuity.md).    
* Si quiere obtener más información sobre los grupos de supervisión, consulte [este artículo](sql-database-elastic-pool-manage-portal.md).    
* Si necesita más detalles sobre Umbraco as a Service, lea [este artículo](https://umbraco.com/cloud).

