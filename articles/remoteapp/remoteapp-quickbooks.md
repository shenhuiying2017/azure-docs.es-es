---
title: "Implementación de QuickBooks en Azure RemoteApp | Microsoft Docs"
description: "Obtenga información acerca de cómo compartir QuickBooks con Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c5d00753-77c0-4f0d-a5df-9451b46a31d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4015aede386913607df8a9499c0f08c414ee6959


---
# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>¿Cómo se implementa QuickBooks en Azure RemoteApp?
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Use la siguiente información para compartir QuickBooks como aplicación en Azure RemoteApp.

Puede compartir QuickBooks 2015 Enterprise con Azure RemoteApp en una colección híbrida o en la nube. El archivo de empresa debe residir en una máquina virtual que está ejecutando el servidor de base de datos de QuickBooks que es independiente de los servidores de Azure RemoteApp. No almacene nunca el archivo en la imagen de Azure RemoteApp: se espera una pérdida de datos si lo hace. Solo QuickBooks Enterprise admite el hospedaje del archivo de QuickBooks en un recurso compartido externo con el servidor de base de datos de QuickBooks accesible a través de redes Windows estándar.   

> [!IMPORTANT]
> El servidor de base de datos de QuickBooks que hospeda el archivo de empresa debe residir en una máquina virtual independiente dentro de la misma red virtual que la colección de Azure RemoteApp .  
> 
> 

## <a name="steps-to-deploy-quickbooks"></a>Pasos para implementar QuickBooks
1. Cree una máquina virtual de Azure e instale QuickBooks, el servidor de base de datos de QuickBooks y coloque el archivo de empresa en una máquina virtual de Azure.  Asegúrese de configurar correctamente las reglas de firewall.
2. Instale QuickBooks en una[ imagen personalizada](remoteapp-imageoptions.md) y cree una colección de [Azure RemoteApp](remoteapp-collections.md), en la nube o híbrida, dentro de la misma red virtual donde reside la máquina virtual que hospeda el servidor de base de datos de QuickBooks con archivos de empresa. 
3. [Publique](remoteapp-publish.md) la aplicación QuickBooks para los usuarios
4. Inicie el cliente QuickBooks hospedado en Azure RemoteApp, vaya mediante redes estándar Windows a la máquina virtual que hospeda el servidor de base de datos de QuickBooks y abra el archivo de empresa. 

## <a name="documentation-references"></a>Referencias de documentación
*  [Configuraciones admitidas](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
*  [Opciones de implementación](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

También puede consultar la presentación de Ignite [Fundamentals of Microsoft Azure RemoteApp Management and Administration](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) (Fundamentos de administración y gestión de Microsoft Azure RemoteApp), con un avance rápido hasta 1:02:45 para llegar a la parte de QuickBooks.

## <a name="deployment-architecture"></a>Arquitectura de implementación
![Implementación de QuickBooks y RemoteApp de Azure](./media/remoteapp-quickbooks/ra-quickbooks.png)




<!--HONumber=Nov16_HO3-->


