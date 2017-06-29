---
title: "Lección 13 del tutorial de Analysis Services: Implementación | Microsoft Docs"
description: "Se describe cómo implementar el proyecto del tutorial en Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 8e3e1be572aa66ab46f894a2e5f395d1e6f2ea23
ms.contentlocale: es-es
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-13-deploy"></a>Lección 13: Implementación

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

En esta lección, configurará propiedades de implementación; especificará un servidor de Analysis Services en Azure o un servidor de Analysis Services local de SQL Server vNext y un nombre para el modelo. A continuación, implementará el modelo en esa instancia. Después de implementa el modelo, los usuarios pueden conectarse a él mediante una aplicación cliente de generación de informes. Para más información, consulte [Deploy to Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) (Implementación en Azure Analysis Services).  
  
Tiempo estimado para completar esta lección: **cinco minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 12: Análisis en Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

**Importante:** si instaló la base de datos de ejemplo AdventureWorksDW2014 en un servidor local de SQL y va a implementar el modelo en un servidor de Analysis Services de Azure, se requiere una [puerta de enlace de datos local](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Implementación del modelo  
  
#### <a name="to-configure-deployment-properties"></a>Para configurar las propiedades de implementación, siga estos pasos:  

  
1.  En **el Explorador de soluciones**, haga clic con el botón derecho en el proyecto **AW Internet Sales** y luego haga clic en **Propiedades**.  
  
2.  En el cuadro de diálogo **AW Internet Sales Property Pages**, en **Servidor de implementación**, en la propiedad **Servidor**, escriba el nombre de un servidor de Analysis Services en Azure o el entorno local.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
 
    > [!IMPORTANT]  
    > Debe tener permisos de administrador en la instancia remota de Analysis Services para poder implementarla.  
  
3.  En la propiedad **Base de datos**, escriba **Adventure Works Internet Sales**.  
  
4.  En la propiedad **Nombre del modelo**, escriba **Adventure Works Internet Sales Model**.  
  
5.  Compruebe sus selecciones y, a continuación, haga clic en **Aceptar**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Para implementar Adventure Works Internet Sales, siga estos pasos:
  
1.  En el **Explorador de soluciones**, haga clic con el botón derecho en el **proyecto AW Internet Sales** > **Compilar**.  

2.  Haga clic con el botón derecho en el **proyecto AW Internet Sales** > **Implementar**.

    Al realizar la implementación en Azure Analysis Services, puede que se le pida que escriba su contraseña. Escriba su cuenta profesional y la contraseña, por ejemplo nancy@adventureworks.com. Esta cuenta debe estar en Administradores en la instancia de servidor.
  
    Aparece el cuadro de diálogo Implementar y muestra el estado de implementación de los metadatos y cada tabla incluida en el modelo.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Cuando la implementación finalice correctamente, siga adelante y haga clic en **Cerrar**.  
  
## <a name="conclusion"></a>Conclusión  
¡Enhorabuena! Ha terminado de crear e implementar el primer modelo tabular de Analysis Services. Este tutorial le ha ayudado a completar las tareas más comunes en la creación de un modelo tabular. Ahora que el modelo Adventure Works Internet Sales está implementado, puede usar SQL Server Management Studio para administrarlo; cree scripts de proceso y un plan de copia de seguridad. Ahora los usuarios pueden conectarse también al modelo mediante una aplicación cliente de generación de informes, como Microsoft Excel o Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Pasos siguientes
*  [Lección complementaria: Seguridad dinámica](../tutorials/aas-supplemental-lesson-dynamic-security.md)

*  [Lección complementaria: Filas de detalles](../tutorials/aas-supplemental-lesson-detail-rows.md)

*  [Lección complementaria: Jerarquías desiguales](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)

