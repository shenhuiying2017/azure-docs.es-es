---
title: "Información general de Microsoft Azure Data Lake Analytics | Microsoft Docs"
description: "Data Lake Analytics es un servicio de macrodatos de Azure que permite usar datos para impulsar el negocio con los conocimientos adquiridos a partir de los datos en la nube, independientemente de dónde se encuentren y de su tamaño."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 5137ccfd2c809fe17cc7fdf06941ebd797288d81
ms.openlocfilehash: 7311588adcdeb11d0972daa68e832c4f95e60a5a


---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Información general de Análisis de Microsoft Azure Data Lake
## <a name="what-is-azure-data-lake-analytics"></a>¿Qué es Análisis de Azure Data Lake?
Azure Data Lake Analytics es un nuevo servicio, creado para facilitar el análisis de macrodatos. Este servicio le permite centrarse en escribir, ejecutar y administrar trabajos, en lugar de administrar la infraestructura distribuida. En lugar de implementar, configurar y ajustar el hardware, escribirá consultas para transformar los datos y extraer ideas valiosas. El servicio de análisis puede administrar trabajos de cualquier escala al instante, simplemente estableciendo el ajuste adecuado. Solo tiene que pagar por su trabajo cuando se está ejecutando, lo que da lugar a una solución económica. El servicio de análisis admite Azure Active Directory, lo que permite administrar de forma sencilla el acceso y los roles, que se integran con su sistema local de identidades. También incluye U-SQL, un lenguaje que unifica las ventajas de SQL con el poder expresivo del código del usuario. El tiempo de ejecución distribuido escalable de U-SQL permite analizar de forma eficiente los datos del almacén y entre los servidores SQL Server en Azure, Azure SQL Database y Azure SQL Data Warehouse.

## <a name="key-capabilities"></a>Principales capacidades
* **Escalado dinámico**
  
    Data Lake Analytics se diseñó para lograr escalado y rendimiento en la nube.  Aprovisiona recursos de forma dinámica y permite analizar terabytes o incluso exabytes de datos. Cuando el trabajo finaliza, reduce los recursos automáticamente y usted solo paga por la capacidad de procesamiento que ha utilizado. Conforme aumenta o disminuye el tamaño de los datos almacenados o la cantidad de proceso utilizado, no tiene que reescribir código. Esto le permite centrarse exclusivamente en la lógica empresarial y no en cómo procesar y almacenar grandes conjuntos de datos.
* **Desarrollo más rápido, depuración y optimización más inteligente con herramientas que ya conoce**
  
    Análisis de Data Lake está profundamente integrado con Visual Studio, de forma que puede usar herramientas familiares para ejecutar, depurar y ajustar su código. Las visualizaciones de sus trabajos de U-SQL le permiten ver cómo se ejecuta el código a escala. De este modo, puede identificar fácilmente cuellos de botella en el rendimiento y optimizar los costos.
* **U-SQL: sencillo y familiar, eficiente y extensible**
  
    Análisis de Data Lake incluye U-SQL, un lenguaje de consulta que amplia la sencilla y familiar naturaleza declarativa de SQL con la capacidad expresiva de C#. El lenguaje U-SQL está basado en el mismo motor de tiempo de ejecución distribuido que sustenta los sistemas de macrodatos en Microsoft. Ahora, millones de desarrolladores de SQL y .NET pueden procesar y analizar sus datos con los conocimientos que ya tienen.
* **Se integra sin problemas con su inversión en TI**
  
    Análisis de Data Lake puede usar su inversión actual en TI para identidades, administración, seguridad y almacenamiento de datos. Esto simplifica el control de los datos y facilita la ampliación de sus aplicaciones de datos actuales. Data Lake Analytics se integra con Active Directory para la administración de usuarios y permisos, y viene con funciones de supervisión y auditoría integradas.
* **Asequible y rentable**
  
    Análisis de Data Lake es una solución muy rentable para ejecutar cargas de trabajo de macrodatos. Usted paga por trabajo cuando se procesan los datos. No se requieren licencias, hardware ni contratos de soporte específicos para el servicio. El sistema se escala o reduce verticalmente de forma automática cuando el trabajo comienza y finaliza, es decir, nunca paga por más recursos de los que necesita.
* **Funciona con todos los datos de Azure**
  
    Data Lake Analytics está especialmente optimizado para trabajar con Azure Data Lake: proporciona el nivel máximo de rendimiento, capacidad de procesamiento y ejecución en paralelo para sus cargas de trabajo de macrodatos.  Data Lake Analytics también puede trabajar con Azure Blob Storage Azure y Azure SQL Database.

## <a name="see-also"></a>Consulte también
* Primeros pasos
  
  * [Introducción a Data Lake Analytics mediante el portal de Azure](data-lake-analytics-get-started-portal.md)
  * [Introducción a Data Lake Analytics mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Introducción a Análisis de Data Lake mediante el SDK de .NET de Azure](data-lake-analytics-get-started-net-sdk.md)
  * [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake.](data-lake-analytics-u-sql-get-started.md)
* U-SQL y desarrollo
  
  * [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)
  * [Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* Administración
  
  * [Administración de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-manage-use-portal.md)
  * [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-manage-use-powershell.md)
  * [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
  * [Accessing Diagnostics logs for Azure Data Lake Analytics (Acceso a los registros de diagnóstico de Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Tutorial integral
  
  * [Uso de tutoriales interactivos de Análisis de Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
  * [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md)
* Díganos su opinión
  
  <!-- Fixing broken links for Azure content migration from ACOM to DOCS. I can't find a suitable substitute for what appears to be a link that is no longer available. I am commenting out for now. The author can investigate in the future. Hyperlink text: Comment on our documentation backlog. Referenced file: data-lake-analytics-documentation-backlog.md -->
  * [Envíe una solicitud de característica](http://aka.ms/adlafeedback)
  * [Obtenga ayuda en los foros](http://aka.ms/adlaforums)




<!--HONumber=Dec16_HO2-->


