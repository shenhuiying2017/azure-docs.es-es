---
title: "Introducción a las herramientas de Elastic Database | Microsoft Docs"
description: "Explicación básica de la característica herramientas de Elastic Database de Azure SQL Database, incluida una aplicación de ejemplo de ejecución sencilla."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
ms.openlocfilehash: 637463399593f4bc9ff5bfcbf67bf93b816efc7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Introducción a las herramientas de Elastic Database
Este documento le presenta la experiencia de desarrollador mediante la ejecución de la aplicación de ejemplo. El ejemplo crea una aplicación particionada sencilla y explora las capacidades clave de las herramientas de Elastic Database. El ejemplo muestra funciones de la [biblioteca de cliente de Elastic Database](sql-database-elastic-database-client-library.md).

Para instalar la biblioteca, vaya a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La biblioteca se instala con la aplicación de ejemplo que se describe en la siguiente sección.

## <a name="prerequisites"></a>Requisitos previos
* Visual Studio 2012 o posterior con C#. Descargue una versión gratuita desde [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 o posterior. Para obtener la versión más reciente, vea [Installing NuGet (Instalación de NuGet)](http://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="download-and-run-the-sample-app"></a>Descarga y ejecución de la aplicación de ejemplo
La aplicación de ejemplo **Elastic DB Tools for Azure SQL - Getting Started** ilustra los aspectos más importantes de la experiencia de desarrollo de aplicaciones con particiones que usan las herramientas de Elastic Database. Se centra en casos de uso claves para la [administración de asignación de particiones](sql-database-elastic-scale-shard-map-management.md), el [enrutamiento dependiente de datos](sql-database-elastic-scale-data-dependent-routing.md) y las [consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md). Para descargar y ejecutar el ejemplo, siga estos pasos: 

1. Descargue el [ejemplo Elastic DB Tools for Azure SQL - Getting Started](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) (Herramientas de Elastic DB Tools para Azure SQL: Introducción) de MSDN. Descomprima el ejemplo en una ubicación de su elección.

2. Para crear un proyecto, abra la solución **ElasticScaleStarterKit.sln** desde el directorio **C#**.

3. En la solución del proyecto de ejemplo, abra el archivo **app.config**. Luego siga las instrucciones del archivo para agregar el nombre de servidor de Azure SQL Database y la información de inicio de sesión (nombre de usuario y contraseña).

4. Compile y ejecute la aplicación. Cuando se le pida, permita que Visual Studio restaure los paquetes NuGet de la solución. Esto descarga la versión más reciente de la biblioteca de cliente de Elastic Database desde NuGet.

5. Experimente con las diferentes opciones para obtener más información sobre las capacidades de la biblioteca de cliente. Anote los pasos que la aplicación lleva a cabo en la salida de la consola y explore el código que hay detrás a su antojo.
   
    ![Progreso][4]

Enhorabuena, ha creado y ejecutado correctamente su primera aplicación con particiones mediante las herramientas de Elastic Database en SQL Database. Use Visual Studio o SQL Server Management Studio para conectar con SQL Database y eche un vistazo rápido a las particiones creadas por el ejemplo. Observará nuevas bases de datos de particiones de ejemplo y una base de datos de administrador de mapas de particiones que ha creado el ejemplo.

> [!IMPORTANT]
> Se recomienda usar siempre la versión más reciente de Management Studio para poder estar al día de las actualizaciones de Azure y SQL Database. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="key-pieces-of-the-code-sample"></a>Piezas clave del ejemplo de código
* **Administración de particiones y mapas de particiones**: el código ilustra cómo trabajar con particiones, intervalos y asignaciones en el archivo **ShardManagementUtils.cs**. Para más información, vea [Scale out databases with the shard map manager](http://go.microsoft.com/?linkid=9862595) (Escalado horizontal de las bases de datos mediante Shard Map Manager).  

* **Enrutamiento dependiente de los datos**: el enrutamiento de transacciones a la partición correcta se muestra en el archivo **DataDependentRoutingSample.cs**. Para más información, vea [Enrutamiento dependiente de los datos](http://go.microsoft.com/?linkid=9862596). 

* **Consultas a través de particiones múltiples**: las consultas a través de particiones se ilustran en el archivo **MultiShardQuerySample.cs**. Para más información, vea [Consultas a través de particiones múltiples](http://go.microsoft.com/?linkid=9862597).

* **Incorporación de particiones vacías**: la incorporación iterativa de nuevas particiones vacías se realiza mediante el código del archivo **CreateShardSample.cs**. Para más información, vea [Scale out databases with the shard map manager](http://go.microsoft.com/?linkid=9862595) (Escalado horizontal de las bases de datos mediante Shard Map Manager).

### <a name="other-elastic-scale-operations"></a>Otras operaciones de escalado elástico
* **División de una partición existente**: la capacidad de dividir particiones se proporciona mediante la **herramienta de división y combinación**. Para más información, vea [Mover datos entre bases de datos en la nube escaladas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md).

* **Combinación de particiones existentes**: las combinaciones de particiones también se realizan mediante la **herramienta de división y combinación**. Para más información, vea [Mover datos entre bases de datos en la nube escaladas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Coste
Las herramientas de Elastic Database son gratuitas. Si usa las herramientas de Elastic Database no tendrá ningún cargo adicional al costo del uso de Azure. 

Por ejemplo, la aplicación de ejemplo crea nuevas bases de datos. El costo depende de la edición de SQL Database que elija y del uso de Azure de la aplicación.

Para obtener información sobre los precios, vea [SQL Database Precios](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las herramientas de Elastic Database, vea las siguientes páginas:

* Ejemplos de código: 
  * [Elastic DB Tools for Azure SQL - Getting Started](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
  * [Elastic DB Tools for Azure SQL - Entity Framework Integration](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Shard Elasticity on Script Center (Elasticidad de particiones en el Centro de scripts)](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Elastic Scale announcement](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy: [implementación del particionamiento de uso de escalado horizontal con el vídeo de biblioteca de cliente de Elastic Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Canal 9: [vídeo de introducción al escalado elástico](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Foro de discusión: [foro de Azure SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Para medir el rendimiento: [Creación de bases de datos escalables en la nube](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

