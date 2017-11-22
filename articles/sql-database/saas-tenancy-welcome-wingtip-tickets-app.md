---
title: "Aplicación Wingtips: Azure SQL Database | Microsoft Docs"
description: "Conozca los modelos de arrendamiento de base de datos y la aplicación de SaaS de ejemplo, Wingtips, para Azure SQL Database en el entorno de nube."
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Aplicación de SaaS de ejemplo Wingtip Tickets para inquilinos de Azure SQL Database

Aplicación de SaaS de ejemplo Wingtip Tickets para inquilinos de Azure SQL Database y sus tutoriales. El arrendamiento de base de datos hace referencia al modo de aislamiento de datos que proporciona la aplicación a los clientes que pagan por hospedarse en ella. Por el momento, para simplificar, o bien cada cliente tiene una base de datos entera para él solo o comparte una base de datos con otro cliente.

## <a name="wingtip-tickets-app"></a>Aplicación Wingtip Tickets

La aplicación de ejemplo Wingtip Tickets ilustra el efecto de los diferentes modelos de arrendamiento de base de datos en el diseño y la administración de aplicaciones de SaaS multiinquilino. Los tutoriales complementarios describen directamente esos mismos efectos. Wingtip Tickets está integrada en Azure SQL Database.

Wingtip Tickets está diseñada para gestionar diversos escenarios de diseño y administración que usan los clientes de SaaS reales. Los patrones de uso que surgen se tienen en cuenta en Wingtip Tickets.

Esta aplicación se puede instalar en su propia suscripción de Azure en cinco minutos. La instalación incluye la inserción de datos de ejemplo de varios inquilinos. Puede instalar la aplicación y los scripts de administración de forma segura en todos los modelos, dado que las instalaciones no interactúan ni interfieren entre sí.

#### <a name="code-in-github"></a>Código en Github

El código de la aplicación y los scripts de administración, están todos disponibles en GitHub:

- Modelo de **aplicación independiente**: [repositorio WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp).
- Modelo de **base de datos por inquilino**: [repositorio WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- Modelo **multiinquilino con particiones**: [repositorio WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

La misma base de código de la aplicación Wingtip Tickets se reutiliza en todos los modelos anteriores enumerados. Puede usar el código de Github para iniciar sus propios proyectos de SaaS.



## <a name="major-database-tenancy-models"></a>Principales modelos de arrendamiento de base de datos

Wingtip Tickets es una aplicación de SaaS de listado de eventos y control de billetes. Wingtip proporciona servicios que son necesarios en los lugares de eventos. Todos los elementos siguientes se aplican a cada lugar:

- Pagan por hospedarse en su aplicación.
- Son un *inquilino* en Wingtip.
- Hospedan eventos. Se incluyen los siguientes eventos:
    - Precios de entradas.
    - Ventas de billetes.
    - Clientes que compran billetes.

La aplicación, junto con los scripts de administración y los tutoriales, muestra un escenario completo de SaaS. El escenario incluye las siguientes actividades:

- Aprovisionamiento de inquilinos
- Supervisión y administración del rendimiento
- Administración de esquemas
- Informes y análisis entre inquilinos

Todas las actividades se proporcionan en la escala necesaria.



## <a name="code-samples-for-each-tenancy-model"></a>Ejemplos de código para cada modelo de arrendamiento

Se pone el énfasis en un conjunto de modelos de aplicación. Sin embargo, otras implementaciones podrían combinar elementos de dos o más modelos.

#### <a name="standalone-app-model"></a>Modelo de aplicación independiente

![Modelo de aplicación independiente][standalone-app-model-62s]

Este modelo usa una aplicación de un único inquilino. Por lo tanto, este modelo solo necesita una base de datos y almacena los datos de ese único inquilino. El inquilino disfruta de un aislamiento completo de otros inquilinos de la base de datos.

Puede usar este modelo si vende instancias de la aplicación a muchos clientes diferentes, así cada uno la puede ejecutar por sí mismo. El cliente es entonces el único inquilino. Si bien la base de datos almacena los datos de un solo cliente, la base de datos almacena datos de muchos clientes del cliente.

#### <a name="database-per-tenant"></a>Base de datos por inquilino

![Modelo de base de datos por inquilino][database-per-tenant-model-35d]

Este modelo tiene varios inquilinos en la instancia de la aplicación. Además para cada nuevo inquilino, se asigna otra base de datos para que solo la use el nuevo inquilino.

Este modelo proporciona aislamiento completo de la base de datos para cada inquilino. El servicio Azure SQL Database cuenta con la sofisticación para convertir este modelo en plausible.

- En [Introduction to a SQL Database multi-tenant SaaS app example][saas-dbpertenant-wingtip-app-overview-15d] (Introducción a una aplicación de ejemplo de SaaS multiinquilino para SQL Database) se puede encontrar más información sobre este modelo.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>Bases de datos multiinquilino con particiones: el modelo híbrido

![Modelo de base de datos multiinquilino con particiones: el híbrido][sharded-multitenantdb-model-hybrid-79m]

Este modelo tiene varios inquilinos en la instancia de la aplicación. Este modelo también tiene varios inquilinos en algunas o todas las bases de datos. Es adecuado para ofrecer distintos niveles de servicio para que los clientes pueden pagar más si valoran el aislamiento completo de la base de datos.

El esquema de cada base de datos incluye un identificador de inquilino. El identificador de inquilino está incluso en esas bases de datos que almacenan solo un inquilino.

- [Introduction to a SQL Database multi-tenant SaaS app example][saas-multitenantdb-get-started-deploy-89i] (Introducción al ejemplo de una aplicación de SaaS multiinquilino de SQL Database)



## <a name="tutorials-for-each-tenancy-model"></a>Tutoriales para cada modelo de inquilino

Cada modelo de arrendamiento se documenta mediante los elementos siguientes:

- Un conjunto de artículos de tutorial
- Código fuente almacenado en un repositorio de Github que se dedica al modelo:
    - El código de la aplicación Wingtip Tickets
    - El código de script para escenarios de administración

#### <a name="tutorials-for-management-scenarios"></a>Tutoriales para escenarios de administración

Los artículos de tutorial para cada modelo abarcan los siguientes escenarios de administración:

- Aprovisionamiento de inquilinos
- Administración y supervisión del rendimiento
- Administración de esquemas
- Informes y análisis entre inquilinos
- Restauración de un inquilino a un momento anterior en el tiempo
- Recuperación ante desastres



## <a name="next-steps"></a>Pasos siguientes

- En [Introduction to a SQL Database multi-tenant SaaS app example][saas-dbpertenant-wingtip-app-overview-15d] (Introducción a una aplicación de ejemplo de SaaS multiinquilino para SQL Database) se puede encontrar más información sobre este modelo.

- [Multi-tenant SaaS database tenancy patterns][multi-tenant-saas-database-tenancy-patterns-60p] (Patrones de arrendamiento de base de datos SaaS multiinquilino)



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Modelo de aplicación independiente"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Modelo de base de datos por inquilino"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Modelo de base de datos multiinquilino con particiones: el híbrido"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


