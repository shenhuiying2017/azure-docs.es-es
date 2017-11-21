---
title: "Caso práctico de Azure SQL Database: Daxko y CSI | Microsoft Docs"
description: "Sepa cómo Daxko y CSI utilizan SQL Database para acelerar su ciclo de desarrollo, aumentar su rendimiento y mejorar sus servicios de atención al cliente."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 7a05836be4a0879fa7103d070c683f45c06cd741
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko y CSI utilizan Azure para acelerar su ciclo de desarrollo, aumentar su rendimiento y mejorar sus servicios de atención al cliente
![Logotipo de Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko y CSI Software se han enfrentado a un reto: la base de clientes de sus centros de bienestar y recreo han aumentado rápidamente gracias al éxito de su solución completa de software empresarial. Sin embargo, el hecho de tener que satisfacer las necesidades de infraestructura de TI de esa base de clientes en aumento estaba poniendo a prueba al personal de TI de la empresa. La compañía cada vez se veía cada vez más limitada por el aumento de la sobrecarga de operaciones, sobre todo las relacionadas con la administración de sus bases de datos en constante crecimiento. Y lo que es peor, esa sobrecarga de operaciones estaba impidiendo que los recursos de desarrollo llevasen a cabo nuevas iniciativas, como las nuevas características de movilidad del software de la empresa.

Según David Molina, el director de desarrollo de productos de Daxko y CSI, Azure proporcionó a CSI Software el modelo de plataforma como servicio (PaaS) que necesitaba para simplificar la administración de las bases de datos, aumentar la escalabilidad y liberar recursos para centrarse en el software en lugar de en las operaciones. "Azure SQL Database resultó una excelente opción para nosotros. Lo ideal era no tener que preocuparnos por mantener un servidor SQL Server, un clúster de conmutación por error y el resto de las necesidades de una infraestructura".

Desde que CSI Software realizó la migración a Azure, solo necesita un personal de operaciones de 2 personas para administrar más de 600 bases de datos de cliente. La empresa utiliza grupos elásticos de Azure SQL Database para mover las bases de datos de cliente en función del tamaño y la necesidad.

"Nuestros clientes percibieron el cambio inmediatamente. Antes de implantar los grupos elásticos, solían encontrarse con tiempos de espera y otros problemas durante los periodos de ráfaga. Con los grupos elásticos de Azure, pueden usar las ráfagas según necesiten y utilizar el software sin problemas", explica Molina.

Además de mejorar el rendimiento para los clientes, los grupos elásticos de Azure liberaron recursos de CSI Software para centrarse en desarrollar nuevos servicios y características, en lugar de lidiar con las operaciones y las tareas de administración. Esos recursos de TI ayudaron a CSI Software a mejorar su oferta de software empresarial, SpectrumNG, para ayudar a interactuar con los miembros de los gimnasios, a mejorar la eficiencia del personal, y a conceder acceso desde dispositivos móviles al personal y a los miembros con el objetivo de realizar tareas interactivas y recibir notificaciones en tiempo real.

Azure también ha ayudado a CSI Software a acelerar y mejorar el ciclo de desarrollo y control de calidad (QA) habilitando opciones de automatización. Con la implementación de Azure en la empresa, los administradores de compilaciones pueden empaquetar componentes con tan solo hacer clic en un botón. Tal y como escribe Molina: "Como parte del ciclo de lanzamiento, la fase de QA ahora puede implementarse en un entorno de prueba de Azure, lo que refleja de forma más fiel nuestra pila de producción. Podemos implementar inmediatamente compilaciones en nuestro entorno de desarrollo para investigar los cambios, lo que supone una gran ventaja para nosotros, ya que, hasta ahora, no habíamos tenido paridad en las pruebas".

## <a name="offloading-to-the-cloud"></a>Descarga en la nube
Antes de migrar la infraestructura a la nube, CSI Software había creado con éxito su propia infraestructura multiempresa en un centro de datos local de Houston (Estados Unidos). Cuando la empresa se expandió, se enfrentó a cada vez más quebraderos de cabeza por tener que comprar, aprovisionar y mantener todo el hardware y software necesarios para prestar servicios de asistencia a sus clientes. El personal de TI encargado de controlar las operaciones se convirtió en otro cuello de botella que provocó una ralentización en el aprovisionamiento de nuevos recursos y la implementación de nuevos servicios para los clientes.

CSI Software analizó opciones de soluciones en la nube para eliminar esa sobrecarga y poder centrarse en el código en lugar de en las operaciones. La empresa descubrió que muchos de los principales proveedores de tecnologías de nube solo ofrecían soluciones de infraestructura como servicio (IaaS) que precisaban un elevado número de empleados de TI para administrar la pila de IaaS. Al final, CSI Software determinó que la solución PaaS de Azure era la opción que mejor se ajustaba a sus necesidades. Molina explica: "Azure no necesita hardware ni software del sistema, por lo que podemos centrarnos en nuestras ofertas de software y reducir al mismo tiempo la sobrecarga de TI".

## <a name="making-the-transition-to-azure"></a>Realización de la transición a Azure
Cuando CSI Software eligió Azure por solución PaaS, empezó a migrar sus bases de datos e infraestructura de back-end a la nube. Antes de Azure, los clientes de SpectrumNG necesitaban instalar una aplicación cliente que se comunicara con un servicio de Windows Communication Foundation (WCF) en el back-end. Según Molina: "Aunque algunos clientes hospedaron todos los recursos en sus propios centros de datos, conseguimos lograr que el producto fuese multicliente. Alojamos todo en un centro de datos de Houston usando SQL Server como almacén de datos".

"Nuestra oferta de productos también incluía un portal web para los miembros (creado en ASP.net), que se diseñó para que los clientes pudiesen adaptarlo a su marca y, por tanto, ajustarse a su presencia en Internet. También integraba una API de SOAP para poder emplear páginas en línea y realizar integraciones con cualquier tercero".

La migración de la arquitectura a la nube no llevó mucho tiempo. Según Molina, "La mayoría del trabajo realizado tuvo como fin modificar la forma en que se lee la información de los archivos de configuración, editar la cadena de conexión centralizada, y automatizar el empaquetado, la carga y la implementación de nuestras versiones".

Para desarrollar la automatización de compilaciones, los ingenieros de CSI Software usan Azure PowerShell y la API de REST para crear paquetes y cargarlos en un entorno de ensayo, donde cada noche se envían a la fase de lanzamiento.
En general, la transición a un modelo de implementación basado en la nube de Azure se realizó rápidamente y sin que el equipo de TI de CSI Software tuviese problemas. Molina explica: "En resumen, tuvimos un entorno beta en la nube a las tres o cuatro semanas de asumir el proyecto, lo que supuso una sorprendente ventaja para nosotros".

Después de configurar y probar el entorno, CSI Software empezó a migrar los clientes. Con los clientes que ya estaban usando el hospedaje de CSI Software, la transición fue casi perfecta. Para aquellos que estaban realizando la migración desde una implementación local, el proceso fue más lento, pero, ni los clientes ni CSI Software tuvieron problemas.

En lo que respecta a los nuevos clientes, el personal de TI de CSI Software empleó el siguiente proceso para migrarlos a Azure:

1. Los scripts de Azure PowerShell se utilizaron para poner en marcha una nueva base de datos para el cliente; todos los clientes empiezan en un nivel Premium para garantizar que obtengan el rendimiento inicial suficiente como para respaldar la transición.
2. Cuando es posible, CSI Software usa Azure SQL Database Migration Wizard con el fin de mover los datos existentes a una instancia de Azure SQL Database.
3. Finalmente, se utilizó Microsoft SQL Server Integration Services (SSIS) para resolver cualquier discrepancia en los datos o realizar todas las tareas de limpieza de datos que fuesen necesarios.

En la actualidad, el 99 % de los clientes de CSI Software están hospedados en cuatro centros de datos regionales de Azure (centro-norte, centro-sur, este y oeste de EE. UU.). Al tener los centros de datos en la región geográfica de cada cliente, la latencia es mínima.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Los grupos elásticos de Azure liberan recursos de TI
Varias características de Azure han ayudado a CSI Software a pasar de centrarse en la infraestructura y las operaciones a hacerlo en las características y las tareas de desarrollo. Probablemente, los grupos elásticos son la principal ventaja que han obtenido.

En estos momentos, CSI Software proporciona unas 550 bases de datos a los clientes. Antes de los grupos elásticos, era difícil administrar ese número tan elevado de bases de datos dentro de una estructura de niveles. Los administradores de operaciones tenían que asignar niveles de rendimiento según las necesidades de ráfagas de los clientes, lo que exigía una importante sobrecarga de recursos de TI. Gracias a los grupos elásticos, los administradores pueden asignar inquilinos a un grupo estándar o Premium, según corresponda y, después, transferir los clientes en función del tamaño y las necesidades. Los clientes percibieron los efectos de los grupos elásticos casi de inmediato; antes de implantarlos, los clientes se encontraban con tiempos de espera y otros problemas durante los periodos de uso de ráfagas. Con los grupos de bases de datos elásticas, pueden utilizar ráfagas de actividad según necesiten; además, pueden usar SpectrumNG sin problemas.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>La replicación geográfica activa de Azure acelera la generación de informes
Varios clientes de CSI Software también están aprovechando las ventajas de la replicación geográfica activa de Azure. Esta característica permite replicar geográficamente hasta cuatro bases de datos secundarias legibles en las mismas regiones de los centros de datos o en otras. CSI Software utiliza la replicación geográfica activa de dos maneras: en primer lugar, las bases de datos secundarias están disponibles en caso de que se produzca una interrupción en el centro de datos o no pueda conectarse con la base de datos principal; en segundo lugar, las bases de datos secundarias son legibles y pueden usarse para descargar cargas de trabajo de solo lectura, por ejemplo, tareas de generación de informes. Algunos clientes de CSI Software utilizan esta ventaja para acelerar los flujos de trabajo de generación de informes.

## <a name="csi-software-application-logic-and-architecture"></a>Arquitectura y lógica de aplicación de CSI Software
SpectrumNG utiliza roles web. Como la aplicación es multiempresa, se utiliza un servicio WCF para atender la solicitud de conexión inicial de los clientes. Tal y como indica Molina: "La solicitud identifica a cada cliente, lo que, posteriormente, nos permite crear una cadena de conexión a sus bases de datos para realizar todo lo que debemos hacer".

Para el nivel web de su servicio, CSI Software aprovecha el escalado automático de Azure en función del día y la hora. Los recursos disponibles aumentan automáticamente para admitir mayores cotas de uso durante el horario laboral según la zona horaria de cada centro de datos regional. Además, se establecen para reducirse verticalmente en los fines de semana, cuando las necesidades del cliente son menores.

![Arquitectura de Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Un rol de trabajo de servicios en la nube extrae los datos estructurados de Azure SQL Database y los semiestructurados de Table Storage. Los usuarios de SpectrumNG interactúan con los datos a través de un rol web de servicios en la nube.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Uso de aplicaciones web y un nivel de plan web para las aplicaciones móviles
Al utilizar Azure SQL Database, se liberaron recursos de CSI Software para poder llevar a cabo nuevas iniciativas, como una completa plataforma móvil basada en una API personalizada hospedada en aplicaciones web de Azure. Esta permite usar a los miembros de los gimnasios y al personal dispositivos móviles para comprobar las programaciones, reservar clases y recibir mensajes.

La plataforma utiliza una arquitectura orientada a servicios (SOA) para trasladar sobre la marcha un solo componente —como un sistema de punto de venta (POS) o de ventas— a otro plan de web. Después, se inicia un servicio para admitir ese componente. De este modo, el resto de los componentes se mantienen en el plan web original. Esta funcionalidad proporciona a CSI Software una flexibilidad extraordinaria y ayuda a reducir los costos.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure permite a los desarrolladores de CSI Software centrarse en las aplicaciones y los servicios
Azure SQL Database no solamente es una ventaja para los clientes de SpectrumNG, que disfrutan de un servicio rápido y confiable, sino que también lo es para los desarrolladores y el personal de TI de CSI Software. Al descargar las operaciones a la nube de Azure, CSI Software redujo la sobrecarga de recursos y la infraestructura, aceleró considerablemente sus ciclos de desarrollo, y no volvió a necesitar microadministrar las bases de datos con el fin de optimizar el rendimiento para sus inquilinos.

## <a name="more-information"></a>Más información
* Para obtener más información sobre los grupos elásticos de Azure, consulte [este artículo](sql-database-elastic-pool.md).
* Si necesita obtener más detalles sobre las herramientas para bases de datos y el escalado elástico, lea [este artículo](sql-database-elastic-scale-get-started.md).
* Para obtener más información cómo migrar una base de datos de SQL Server, vea [Migración de una base de datos de SQL Server a una Base de datos SQL en la nube](sql-database-cloud-migrate.md).
* Para obtener más información sobre la replicación geográfica activa, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
* Para obtener más detalles sobre los roles web y de trabajo, consulte [este artículo](../fundamentals-introduction-to-azure.md#compute).    
* Si necesita más información sobre Azure Service Bus, lea [este artículo](https://azure.microsoft.com/services/service-bus/).
* Si quiere saber más sobre el escalado automático, revise [este artículo](../cloud-services/cloud-services-how-to-scale-portal.md).

