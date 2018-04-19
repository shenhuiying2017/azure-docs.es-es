---
title: Configuración de herramientas de Azure Stream Analytics para Visual Studio
description: En este artículo se describen los requisitos de instalación de las herramientas de Azure Stream Analytics para Visual Studio y cómo configurarlas.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalación de las herramientas de Azure Stream Analytics para Visual Studio
Las herramientas de Azure Stream Analytics ahora son compatibles con Visual Studio 2017, 2015 y 2013. En este documento se describe cómo instalar y desinstalar las herramientas.

Para más información sobre el uso de las herramientas, consulte [Uso de herramientas de Azure Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalación
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Descargue [Visual Studio 2017 (15.3 o una versión superior)](https://www.visualstudio.com/). Se admiten las ediciones Enterprise (Ultimate y Premium), Professional y Community. No se admite la edición Express. 
* Las herramientas de Stream Analytics forman parte de las cargas de trabajo de **almacenamiento y procesamiento de datos** y **desarrollo de Azure** en Visual Studio 2017. Habilite cualquiera de estas dos cargas como parte de la instalación de Visual Studio.

Habilite la carga de trabajo de **procesamiento y almacenamiento de datos** como se muestra:

![Carga de trabajo de procesamiento y almacenamiento de datos](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Habilite la carga de trabajo de **desarrollo de Azure** como se muestra:

![Carga de trabajo de desarrollo de Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Instale Visual Studio 2015 o Visual Studio 2013 Update 4. Se admiten las ediciones Enterprise (Ultimate y Premium), Professional y Community. No se admite la edición Express. 
* Instale Microsoft Azure SDK para .NET versión 2.7.1 o superior con el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* Instale [Herramientas de Azure Stream Analytics para Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Actualizar

### <a name="visual-studio-2017"></a>Visual Studio 2017
El recordatorio de la versión nueva aparece en la notificación de Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Las herramientas instaladas de Stream Analytics para Visual Studio buscan automáticamente nuevas versiones. Siga las instrucciones en la ventana emergente para instalar la última versión. 


## <a name="uninstall"></a>Desinstalación

### <a name="visual-studio-2017"></a>Visual Studio 2017
Haga doble clic en el instalador de Visual Studio y seleccione **Modificar**. Desactive la casilla **Herramientas de Azure Data Lake y Stream Analytics** en la carga de trabajo de **procesamiento y almacenamiento de datos** o de **desarrollo de Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Vaya al Panel de control y desinstale **Herramientas de Microsoft Azure Data Lake y Stream Analytics**.





