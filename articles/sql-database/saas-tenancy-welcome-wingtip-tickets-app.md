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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 2a36df0e45af5bcce5338d04b7e1ba44221ae964
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplicación SaaS Wingtip Tickets

La misma aplicación *Wingtip Tickets* se implementa en cada uno de los tres ejemplos. La aplicación es una simple aplicación SaaS de lista de eventos y venta de entradas centrada en pequeños locales: teatros, clubes, etc. Cada local es un inquilino de la aplicación y tiene sus propios datos: detalles del local, listas de los eventos, clientes, pedidos de entradas, etc.  La aplicación, junto con los scripts de administración y los tutoriales, muestra un escenario completo de SaaS. Esto incluye aprovisionar los inquilinos, supervisar y administrar el rendimiento, la administración de esquemas y los informes y análisis entre inquilinos.

## <a name="three-saas-application-patterns"></a>Tres patrones de aplicación SaaS

Hay tres versiones disponibles de la aplicación, donde cada una explora un patrón de inquilinato de base de datos distinto en Azure SQL Database.  La primera usa una aplicación de un único inquilino con una base de datos de un único inquilino aislada. La segunda usa una aplicación multiinquilino, con una base de datos por inquilino. El tercer ejemplo usa una aplicación multiinquilino con bases de datos multiinquilino con particiones.

![Tres patrones de inquilinato][image-three-tenancy-patterns]

 Cada ejemplo incluye scripts de administración y tutoriales que exploran una variedad de patrones de diseño y administración que puede usar en su propia aplicación.  Cada ejemplo se implementa en menos de cinco minutos.  Las tres opciones se pueden implementar en paralelo para poder comparar las diferencias de diseño y administración.

## <a name="standalone-application-pattern"></a>Patrón de aplicación independiente

El patrón de aplicación independiente usa una aplicación con un único inquilino con una base de datos de un único inquilino para cada inquilino. La aplicación de cada inquilino se implementa en un grupo de recursos de Azure independiente. Puede ser en la suscripción del proveedor de servicios o en la suscripción del inquilino, y el proveedor la administra en nombre del inquilino. Este patrón proporciona el mayor nivel de aislamiento del inquilino, pero suele ser la opción más costosa porque no se pueden compartir los recursos entre varios inquilinos.

Consulte los [tutoriales][docs-tutorials-for-wingtip-sa] y el código en GitHub  [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Patrón de base de datos por inquilino

El patrón de base de datos por inquilino es eficaz para los proveedores de servicios preocupados por el aislamiento del inquilino y que desean ejecutar un servicio centralizado que permita un uso rentable de los recursos compartidos. Se crea una base de datos para cada local, o inquilino, y todas las bases de datos se administran de manera central. Las bases de datos se pueden hospedar en grupos elásticos para proporcionar una administración del rendimiento sencilla y rentable, que aprovecha los patrones de cargas de trabajo imprevisibles de los inquilinos. Una base de datos de catálogo contiene la asignación entre los inquilinos y sus bases de datos. Esta asignación se administra mediante las características de administración del mapa de particiones de la [biblioteca de cliente de Elastic Database](sql-database-elastic-database-client-library.md), que proporciona la administración eficaz de la conexión con la aplicación.

Consulte los [tutoriales][docs-tutorials-for-wingtip-dpt] y el código en GitHub  [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Patrón de base de datos multiinquilino con particiones

Las bases de datos multiinquilino son eficaces para los proveedores de servicios que buscan un menor costo por inquilino y están de acuerdo con un aislamiento de inquilino reducido. Este patrón permite empaquetar grandes cantidades de inquilinos en una sola base de datos, con lo que disminuye el costo por inquilino. Es posible lograr una escala casi infinita mediante la creación de particiones de los inquilinos en varias bases de datos.  Nuevamente, una base de datos de catálogo asigna inquilinos a las bases de datos.  

Este patrón también permite un modelo híbrido en el que puede optimizar el costo con varios inquilinos en una base de datos, o bien optimizar el aislamiento con un solo inquilino en su propia base de datos. Puede elegir la opción según cada inquilino, ya sea cuando se aprovisiona el inquilino o después, sin afectar la aplicación de ninguna manera.

Consulte los [tutoriales][docs-tutorials-for-wingtip-mt] y el código en GitHub  [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Pasos siguientes

#### <a name="conceptual-descriptions"></a>Descripciones de conceptos

- Puede encontrar una aplicación más detallada del patrón de inquilinato de aplicación en [Patrones de inquilinato de base de datos SaaS multiinquilino][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Tutoriales y código

- Aplicación independiente:
    - [Tutoriales para la aplicación independiente][docs-tutorials-for-wingtip-sa].
    - [Código de la aplicación independiente en GitHub][github-code-for-wingtip-sa].

- Base de datos por inquilino:
    - [Tutoriales para base de datos por inquilino][docs-tutorials-for-wingtip-dpt].
    - [Código para base de datos por inquilino en GitHub][github-code-for-wingtip-dpt].

- Multiinquilino con particiones:
    - [Tutoriales para multiinquilino con particiones][docs-tutorials-for-wingtip-mt].
    - [Código para multiinquilino con particiones en GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tres patrones de inquilinato."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

