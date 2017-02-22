---
title: "Lista de versiones del esquema de configuración de Diagnósticos de Azure | Microsoft Docs"
description: "Se utiliza para configurar la recopilación de contadores de rendimiento en Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Service Fabric y Cloud Services."
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Lista de versiones de Diagnósticos de Azure
Esta página es un índice de las versiones del esquema de Diagnósticos de Azure que se incluyen como parte de Microsoft Azure SDK.  

> [!NOTE]
> Diagnósticos de Azure es el componente que se usa para recopilar contadores de rendimiento y otras estadísticas de Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Service Fabric y Cloud Services.  Esta página solo es pertinente si está usando uno de estos servicios.
>

Diagnósticos de Azure se usa junto con otros productos de diagnósticos de Microsoft, como Azure Monitor, Application Insights y Log Analytics.

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Gráfico de envío de las versiones del SDK y de Diagnósticos de Azure  

|Versión del SDK de Azure | Versión de Diagnósticos de Azure | Modelo|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | complemento|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |extensión|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  

 La versión 1.0 de Diagnósticos de Azure se incluyó por primera vez en un modelo de complemento, lo que significa que, al instalar el SDK de Azure, obtuvo la versión de Diagnósticos de Azure que se incluía con el producto.  

 A partir de SDK 2.5 (versión 1.2 de Diagnósticos), Diagnósticos de Azure pasó a un modelo de extensión. Las herramientas para usar las características nuevas solo estaban disponibles en las versiones más recientes de los SDK de Azure, pero cualquier servicio en la nube o máquina virtual que usen Diagnósticos seleccionará la versión de envío más reciente directamente desde Azure.  

 Por ejemplo, los usuarios que siguen usando SDK 2.5 podrían cargar Diagnósticos 1.5, con independencia de si están usando las características más recientes.  

## <a name="azure-diagnostics-schemas-index"></a>Índice de esquemas de Diagnósticos de Azure  
[Esquema de configuración de Diagnósticos 1.0](azure-diagnostics-schema-1dot0.md)  

[Esquema de configuración de Diagnósticos 1.2](azure-diagnostics-schema-1dot2.md)  

[Esquema de configuración de Diagnósticos 1.3. a 1.5](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


