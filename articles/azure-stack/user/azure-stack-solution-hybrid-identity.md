---
title: Configuración de la identidad de nube híbrida con aplicaciones de Azure y Azure Stack | Microsoft Docs
description: Aprenda a configurar la identidad de nube híbrida con aplicaciones de Azure y Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Tutorial: Configuración de la identidad de nube híbrida con aplicaciones de Azure y Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Tiene dos opciones para conceder acceso a las aplicaciones de Azure global y Azure Stack. Cuando Azure Stack tiene conexión continua a Internet, puede usar Azure Active Directory (Azure AD). Cuando Azure Stack se desconecta de Internet, puede usar los Servicios de federación de Active Directory (AD FS). Usará entidades de servicio para conceder acceso a las aplicaciones en Azure Stack en beneficio de la implementación o la configuración mediante Azure Resource Manager en Azure Stack. 

En este tutorial, creará un entorno de ejemplo para:

> [!div class="checklist"]
> * Establecer una identidad híbrida en Azure global y Azure Stack
> * Recuperar un token de acceso a la API de Azure Stack

Para estos pasos es necesario tener permisos de operador de Azure Stack.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Crear a una entidad de servicio para Azure AD en el portal

Si ha implementado Azure Stack con Azure AD como el almacén de identidades, puede crear entidades de servicio igual que hace para Azure. En [este documento](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) se muestra cómo realizar estos pasos a través del portal. Compruebe que tiene los [permisos de Azure AD necesarios](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) antes de comenzar.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Creación de una entidad de servicio para AD FS mediante PowerShell

Si ha implementado Azure Stack con AD FS, puede usar PowerShell para crear una entidad de servicio, asignar un rol para el acceso e iniciar sesión en Powershell con dicha identidad. En [este documento](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) se muestra cómo realizar los pasos mediante PowerShell.

## <a name="using-the-azure-stack-api"></a>Uso de la API de Azure Stack

[Este tutorial](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) le guía por el proceso de recuperar un token de acceso a la API de Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Conexión a Azure Stack con Powershell

Este es un script de ejemplo donde se usan los conceptos enseñados en este documento para conectarse a Azure Stack.

### <a name="prerequisites"></a>requisitos previos

Una instalación de Azure Stack conectada a Azure Active Directory con una suscripción a la que puede acceder. Si no tiene una instalación de Azure Stack, puede usar estas instrucciones para configurar un [Kit de desarrollo de Azure Stack](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[Este tutorial](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) le guía por los pasos necesarios para instalar Azure PowerShell y conectarse a la instalación de Azure Stack.

#### <a name="connect-to-azure-stack-using-code"></a>Conexión a Azure Stack mediante código

Para conectarse a Azure Stack mediante código, use la API de los puntos de conexión de Azure Resource Manager para obtener los puntos de conexión de autenticación y Graph para la instalación de Azure Stack y, luego, autentíquese mediante solicitudes de REST. Puede encontrar una aplicación de ejemplo [aquí](https://github.com/shriramnat/HybridARMApplication).

> [!Note]  
A menos que el SDK de Azure correspondiente al lenguaje seleccionado admita perfiles de API de Azure, puede que el SDK no funcione con Azure Stack. Para más información sobre los perfiles de API de Azure, consulte [aquí](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Pasos siguientes

 - Para más información sobre cómo se trata la identidad en Azure Stack, consulte [Arquitectura de identidad para Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Para más información sobre los patrones de nube de Azure, consulte [Patrones de diseño en la nube](https://docs.microsoft.com/azure/architecture/patterns).
