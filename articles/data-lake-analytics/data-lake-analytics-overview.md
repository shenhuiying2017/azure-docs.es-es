---
title: "Información general de Microsoft Azure Data Lake Analytics | Microsoft Docs"
description: "Data Lake Analytics es un servicio de macrodatos de Azure que permite usar datos para impulsar el negocio con los conocimientos adquiridos a partir de los datos en la nube, independientemente de dónde se encuentren y de su tamaño."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 8817b511d779029421491194b50120d51ec9dbad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Información general de Microsoft Azure Data Lake Analytics
## <a name="what-is-azure-data-lake-analytics"></a>¿Qué es Azure Data Lake Analytics?
Azure Data Lake Analytics es un servicio de trabajos de análisis a petición que simplifica el análisis de macrodatos. Le permite centrarse en la escritura, ejecución y administración de trabajos, en lugar de en el manejo de una infraestructura distribuida. En lugar de implementar, configurar y ajustar el hardware, escribirá consultas para transformar los datos y extraer ideas valiosas. El servicio de análisis puede administrar trabajos de cualquier escala al instante, simplemente estableciendo el ajuste adecuado. Solo tiene que pagar por su trabajo cuando se está ejecutando, lo que hace que sea una solución económica. El servicio de análisis admite Azure Active Directory, lo que permite administrar de forma sencilla el acceso y los roles, que se integran con su sistema local de identidades. También incluye U-SQL, un lenguaje que unifica las ventajas de SQL con el poder expresivo del código del usuario. El tiempo de ejecución distribuido escalable de U-SQL permite analizar de forma eficiente los datos del almacén y entre los servidores SQL Server en Azure, Azure SQL Database y Azure SQL Data Warehouse.

## <a name="key-capabilities"></a>Principales capacidades
* **Escalado dinámico**
  
    Data Lake Analytics se diseñó para lograr escalado y rendimiento en la nube.  Aprovisiona recursos de forma dinámica y permite analizar terabytes o incluso exabytes de datos. Cuando el trabajo finaliza, reduce los recursos automáticamente y usted solo paga por la capacidad de procesamiento que ha utilizado. Cuando aumente o disminuya el tamaño de los datos almacenados o la cantidad de recursos de proceso utilizados, no tendrá que reescribir código. Esto le permite centrarse exclusivamente en la lógica empresarial y no en cómo procesar y almacenar grandes conjuntos de datos.
* **Desarrollo más rápido, depuración y optimización más inteligente con herramientas que ya conoce**
  
    Data Lake Analytics está profundamente integrado con Visual Studio, por lo que puede usar herramientas muy conocidas para ejecutar, depurar y ajustar el código. Las visualizaciones de sus trabajos de U-SQL le permiten ver cómo se ejecuta el código a escala. De este modo, puede identificar fácilmente cuellos de botella en el rendimiento y optimizar los costos.
* **U-SQL: sencillo y familiar, eficiente y extensible**
  
    Análisis de Data Lake incluye U-SQL, un lenguaje de consulta que amplia la sencilla y familiar naturaleza declarativa de SQL con la capacidad expresiva de C#. El lenguaje U-SQL está basado en el mismo motor de tiempo de ejecución distribuido que sustenta los sistemas de macrodatos en Microsoft. Ahora, millones de desarrolladores de SQL y .NET pueden procesar y analizar sus datos con los conocimientos que ya tienen.
* **Se integra sin problemas con su inversión en TI**
  
    Análisis de Data Lake puede usar su inversión actual en TI para identidades, administración, seguridad y almacenamiento de datos. De esta forma se simplifica el control de los datos y se facilita la ampliación de sus aplicaciones de datos actuales. Data Lake Analytics se integra con Active Directory para la administración de usuarios y permisos, y viene con funciones de supervisión y auditoría integradas.
* **Asequible y rentable**
  
    Análisis de Data Lake es una solución muy rentable para ejecutar cargas de trabajo de macrodatos. Usted paga por trabajo cuando se procesan los datos. No se requieren licencias, hardware ni contratos de soporte específicos para el servicio. El sistema se escala o reduce verticalmente de forma automática cuando el trabajo comienza y finaliza, por lo que nunca se paga por recursos que no sean estrictamente necesarios.
* **Funciona con todos los datos de Azure**
  
    Data Lake Analytics está especialmente optimizado para trabajar con Azure Data Lake: proporciona el nivel máximo de rendimiento, capacidad de procesamiento y ejecución en paralelo para sus cargas de trabajo de macrodatos.  Data Lake Analytics también puede funcionar con Azure Blob Storage y Azure SQL Database.

## <a name="next-steps"></a>Pasos siguientes
 
  * Introducción a Data Lake Analytics mediante [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md)
  * Administración de Azure Data Lake Analytics mediante [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [SDK de Azure .NET](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
  * [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md) 
