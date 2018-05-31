---
title: Instalación de Herramientas de Azure Data Lake para Visual Studio | Microsoft Docs
description: Aprenda a instalar Azure Data Lake Tools para Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2018
ms.author: saveenr, yanacai
ms.openlocfilehash: 37b01c404006bbba79b185049aea6c7f77ce3c68
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887355"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalación de Data Lake Tools para Visual Studio

Aprenda a usar Visual Studio para crear cuentas de Azure Data Lake Analytics, definir trabajos en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos al servicio Data Lake Analytics. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>requisitos previos

* **Visual Studio**: se admiten todas las ediciones, excepto Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK para .NET** versión 2.7.1, o posterior.  Instálelo con el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* Una cuenta de **Data Lake Analytics**. Para crear una cuenta, consulte [Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Instalación de Herramientas de Azure Data Lake para Visual Studio 2017

Herramientas de Azure Data Lake para Visual Studio se puede usar en Visual Studio 2017 15.3 o versiones posteriores. Las herramientas forman parte de las cargas de trabajo **Almacenamiento y procesamiento de datos** y **Desarrollo de Azure** en Instalador de Visual Studio. Habilite cualquiera de estas dos cargas como parte de la instalación de Visual Studio.  

Habilite la carga de trabajo **Almacenamiento y procesamiento de datos** tal y como se muestra: ![Habilitar la carga de trabajo de almacenamiento y procesamiento de datos](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Habilite la carga de trabajo **Desarrollo de Azure** tal y como se muestra: ![Habilitar la carga de trabajo Desarrollo de Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalación de Herramientas de Azure Data Lake para Visual Studio 2013 y 2015

Descargue Azure Data Lake Tools para Visual Studio [desde el Centro de descarga](http://aka.ms/adltoolsvs) e instálelo. Después de la instalación tenga en cuenta que:
* El nodo de **Explorador de servidores** > **Azure** contiene un nodo de **Data Lake Analytics**. 
* El menú **Herramientas** tiene un elemento de **Data Lake**.


## <a name="next-steps"></a>Pasos siguientes
* Para registrar información de diagnóstico, consulte [Accessing Diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para usar la vista de ejecución de vértices, consulte [Uso de la vista de ejecución de vértices de Data Lake Tools para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
