---
title: "Instrucciones de instalación de las herramientas de Azure Stream Analytics para Visual Studio | Microsoft Docs"
description: "Instrucciones de instalación de las herramientas de Azure Stream Analytics para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 88abf40cefaca150c67e3a1068006fb0fa254305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Instrucciones de instalación de las herramientas de Stream Analytics para Visual Studio
Las herramientas de Stream Analytics ahora son compatibles con Visual Studio 2017, 2015 y 2013. En este documento se describe cómo instalar y desinstalar las herramientas.

[Sepa cómo usar las herramientas de Stream Analytics para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="install"></a>Instalación
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Descargue [Visual Studio 2017 (15.3 o una versión superior)](https://www.visualstudio.com/). Se admiten las ediciones Enterprise (Ultimate y Premium), Professional, Community; no se admite la edición Express. 
* Las herramientas de Stream Analytics forman parte de la carga de trabajo de almacenamiento y procesamiento de datos y desarrollo de Azure en Visual Studio 2017. Habilite cualquiera de estas dos cargas como parte de la instalación de Visual Studio.

![Instalación de las herramientas de Stream Analytics para Visual Studio 1](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)
![Instalación de las herramientas de Stream Analytics para Visual Studio 2](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Instale Visual Studio 2015, Visual Studio 2013 Update 4. Se admiten las ediciones Enterprise (Ultimate y Premium), Professional, Community; no se admite la edición Express. 
* Instale Microsoft Azure SDK para .NET versión 2.7.1 o superior con el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* Instale [Herramientas de Azure Stream Analytics para Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Actualizar

### <a name="visual-studio-2017"></a>Visual Studio 2017
El recordatorio de la versión nueva aparece en la notificación de Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Las herramientas de Stream Analytics para Visual Studio que están instaladas comprueban automáticamente si existen versiones nuevas y se pueden seguir las instrucciones de la ventana emergente para instalar la versión más reciente. 


## <a name="uninstall"></a>Desinstalación

### <a name="visual-studio-2017"></a>Visual Studio 2017
Haga doble clic en el instalador de Visual Studio y seleccione **Modificar**. Desactive la opción **Azure Data Lake y herramientas de Stream Analytics** ya sea de la carga de trabajo**Almacenamiento y procesamiento de datos** o **Desarrollo de Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Vaya al panel de control y desinstale "Microsoft Azure Data Lake y herramientas de Stream Analytics para Visual Studio".





