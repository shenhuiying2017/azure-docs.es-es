---
title: "Puertos más allá de 1433 para SQL Database | Microsoft Docs"
description: "Las conexiones de cliente de ADO.NET a Azure SQL Database omiten el proxy e interactúan directamente con la base de datos. Los puertos que no sean 1433 se convierten en puertos importantes."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 3f17106a-92fd-4aa4-b6a9-1daa29421f64
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
ms.openlocfilehash: d47ee8c794d1e231507dae6bb4aa88bf19ce6418
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Puertos más allá de 1433 para ADO.NET 4.5
En este tema se describe el comportamiento de conexión de Azure SQL Database para clientes que usan ADO.NET 4.5 o una versión posterior. 

> [!IMPORTANT]
> Para obtener información acerca de la arquitectura de conectividad, consulte [Arquitectura de conectividad de Azure SQL Database](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Fuera o dentro
Para las conexiones a Azure SQL Database, debemos preguntar si el programa cliente se ejecuta *fuera* o *dentro* del límite de la nube de Azure. En las subsecciones se describen dos escenarios comunes.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fuera:* el cliente se ejecuta en el equipo de escritorio
El puerto 1433 es el único puerto que debe estar abierto en su equipo de escritorio que hospeda su aplicación de cliente de la Base de datos SQL.

#### <a name="inside-client-runs-on-azure"></a>*Dentro:* el cliente se ejecuta en Azure
Cuando el cliente se ejecuta dentro del límite de la nube de Azure, usa lo que podemos llamar una *ruta directa* para interactuar con el servidor de Base de datos SQL. Cuando se ha establecido una conexión, las interacciones posteriores entre el cliente y la base de datos no implican ningún proxy de middleware.

La secuencia es la siguiente:

1. ADO.NET 4.5 (o posterior) inicia una breve interacción con la nube de Azure y recibe un número de puerto identificado dinámicamente.
   
   * El número de puerto identificado dinámicamente se encuentra en el intervalo de 11000-11999 o 14000-14999.
2. Luego, ADO.NET se conecta al servidor de Base de datos SQL directamente, sin ningún middleware entre ellos.
3. Las consultas se envían directamente a la base de datos y los resultados se devuelven directamente al cliente.

Asegúrese de que los intervalos de puertos de 11000 a 11999 y de 14000 a 14999 en el equipo cliente de Azure queden disponibles para las interacciones de cliente de ADO.NET 4.5 con SQL Database.

* En concreto, los puertos del intervalo deben estar libres de cualquier otro bloqueador de salida.
* En la máquina virtual de Azure, **Firewall de Windows con seguridad avanzada** controla la configuración de puertos.
  
  * Puede usar la [interfaz de usuario del firewall](http://msdn.microsoft.com/library/cc646023.aspx) para agregar una regla para la que se especifique el protocolo **TCP** junto con un intervalo de puertos con una sintaxis similar a **11000-11999**.

## <a name="version-clarifications"></a>Aclaraciones de versiones
En esta sección se explican los monikers que hacen referencia a las versiones de producto. También se muestran algunos emparejamientos de versiones entre productos.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 admite el protocolo TDS 7.3, pero no 7.4.
* ADO.NET 4.5 y versiones posteriores admite el protocolo TDS 7.4.

## <a name="related-links"></a>Vínculos relacionados
* ADO.NET 4.6 se publicó el 20 de julio de 2015. Hay un anuncio de blog del equipo de .NET disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 se publicó el 15 de julio de 2012. Hay un anuncio de blog del equipo de .NET disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Hay una entrada de blog sobre ADO.NET 4.5.1 disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [Lista de versiones del protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md)
* [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md)

