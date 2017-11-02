---
title: "Implementaciones técnicas para clientes de Azure SQL Database | Microsoft Docs"
description: "Obtenga información técnica detallada sobre implementaciones de clientes de Azure SQL Database para resolver problemas empresariales"
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
ms.date: 03/03/2017
ms.author: carlrab
ms.openlocfilehash: b22307ee0cade40370b096ee49bb15dcfd2f0071
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Estudios técnicos de implementaciones para clientes de Azure SQL Database

- [Daxko](sql-database-implementation-daxko.md): Daxko y CSI Software se han enfrentado a un reto: la base de clientes de sus centros de bienestar y recreo han aumentado rápidamente gracias al éxito de su solución completa de software empresarial. Sin embargo, el hecho de tener que satisfacer las necesidades de infraestructura de TI de esa base de clientes en aumento estaba poniendo a prueba al personal de TI de la empresa. La compañía cada vez se veía cada vez más limitada por el aumento de la sobrecarga de operaciones, sobre todo las relacionadas con la administración de sus bases de datos en constante crecimiento. Y lo que es peor, esa sobrecarga de operaciones estaba impidiendo que los recursos de desarrollo llevasen a cabo nuevas iniciativas, como las nuevas características de movilidad del software de la empresa.

- [GEP](sql-database-implementation-gep.md): GEP ofrece software y servicios para que los líderes de compras de todo el mundo puedan optimizar los rendimientos financieros, las estrategias y las operaciones de sus negocios. Además de servicios de consultoría y administrados, la empresa ofrece SMART by GEP®, una completa plataforma de software de compras en la nube. Sin embargo, GEP tuvo problemas a la hora de tratar de integrar soluciones como SMART by GEP en sus propios centros de datos locales: las inversiones que tenía que realizar eran elevadas y habrían sido más cuantiosas aún debido a los requisitos normativos de otros países. Al migrar su infraestructura a la nube, GEP ha liberado recursos de TI, con lo que pueden preocuparse menos por las operaciones informáticas y centrarse más en el desarrollo de nuevas fuentes de valor para sus clientes internacionales.

- [SnelStart](sql-database-implementation-snelstart.md): SnelStart desarrolla conocidas aplicaciones de software de administración financiera y empresarial para pequeñas y medianas empresas (pymes) de los Países Bajos. Un personal compuesto por 110 empleados —entre ellos, un equipo de TI de 35 miembros— atiende a 55 000 clientes. Al migrar su infraestructura a la oferta de software como servicio (SaaS) de Azure, SnelStart pudo integrar la mayoría de los servicios, con lo que automatizó la administración mediante un entorno familiar en C#, y optimizó el rendimiento y la escalabilidad sin tener que provocar el exceso o la falta de aprovisionamiento de negocios gracias a los grupos elásticos. Azure proporciona a SnelStart fluidez para migrar clientes entre entornos locales y en la nube.

- [Umbraco](sql-database-implementation-umbraco.md): para simplificar las implementaciones de clientes, Umbraco agregó Umbraco as a Service (UaaS), una oferta SaaS que elimina la necesidad de realizar implementaciones locales, proporciona escalado integrado, y acaba con la sobrecarga de administración, ya que permite a los desarrolladores centrarse en las innovaciones de productos en lugar de en la administración de soluciones. Umbraco puede brindar todas estas ventajas empleando el modelo flexible de plataforma como servicio (PaaS) que ofrece Microsoft Azure.

- [Quorum](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database): Quorum duplica cargas de trabajo clave de las bases de datos a la vez que reduce las DTU en un 70 % con SQL Database.

- [Quest](https://customers.microsoft.com/en-US/story/quest): Quest ofrece su servicio Spotlight en SQL Server Enterprise con un objetivo en mente: proporcionar a los profesionales de bases de datos las mejores herramientas disponibles para proteger los datos, mover esos datos y supervisar las operaciones de base de datos. Con Spotlight y el uso combinado de Microsoft Azure y Azure SQL Database, los administradores de bases de datos de SQL Server pueden supervisar, detectar, diagnosticar y proporcionar una manera de resolver problemas de rendimiento en SQL Server, tanto si trabajan en una oficina o desde casa.

- [Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam): este breve caso práctico destaca los procedimientos recomendados y las lecciones aprendidas a partir de la experiencia del equipo de producto de Dynamics 365 for Operations en la migración a Azure SQL Database a fin de proporcionar a los clientes una oferta de software como servicio (SaaS) completamente administrada. Con Azure SQL Database, el equipo de Dynamics 365 for Operations pudo administrar y usar el servicio con bastante menos personal y escalarlo fácilmente gracias a las características de capacidad de administración listas para usar, como copias de seguridad automáticas de bases de datos, retención de copias de seguridad de bases de datos, alta disponibilidad y recuperación ante desastres. Esto, junto con la posibilidad de aprovisionar bases de datos con apenas automatización, llevaron a que Azure SQL Database fuera una buena plataforma para poner en marcha servicios a gran escala.

- [Microsoft OneDrive y SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states): este breve caso práctico refleja el relato detrás del traslado de Microsoft OneDrive y SharePoint Online a Azure SQL Database y se describe cómo esta migración habilitó la administración de capacidad elástica prácticamente ilimitada a la vez que se redujeron los costos operativos y la sobrecarga de infraestructura.
