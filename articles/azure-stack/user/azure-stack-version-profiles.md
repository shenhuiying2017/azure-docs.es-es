---
title: Administración de perfiles de la versión de la API de Azure Stack | Microsoft Docs
description: Aprenda los perfiles de la versión de API en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Administre los perfiles de la versión de API en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los perfiles de la API especifican el proveedor de recursos de Azure y la versión de la API para los puntos de conexión REST de Azure. Puede crear clientes personalizados en distintos idiomas mediante perfiles de API. Cada cliente utiliza un perfil de API para ponerse en contacto con el proveedor de recursos correcto y la versión de API para Azure Stack. 

Puede crear una aplicación para trabajar con los proveedores de recursos de Azure sin tener que escoger exactamente qué versión de cada API de proveedor de recursos es compatible con Azure Stack. Solo tiene que alinear la aplicación con un perfil; el SDK vuelve a las versiones de la API correctas.


Este tema le ayudará a:
 - Comprender los perfiles de API para Azure Stack.
 - Aprender a utilizar los perfiles de la API para desarrollar soluciones.
 - Saber dónde buscar para obtener guías específicas del código.

## <a name="summary-of-api-profiles"></a>Resumen de perfiles de API

- Los perfiles de API se utilizan para representar un conjunto de proveedores de recursos de Azure y sus versiones de API.
- Los perfiles de API se crearon para que los desarrolladores creen plantillas por varias nubes de Azure. Están diseñados para satisfacer la necesidad de una interfaz compatible y estable.
- Los perfiles se publican cuatro veces al año.
- Los tres perfiles de convenciones de nomenclatura son los siguientes:
    - **más reciente**  
        Versiones más recientes de la API publicadas en Azure.
    - **yyyy-mm-dd-hybrid**  
    Publicada en una cadencia semestral, esta versión se centra en la estabilidad y coherencia entre varias nubes.
    - **yyyy-mm-dd-profile**  
    Se sitúa entre la estabilidad óptima y las últimas características.

## <a name="azure-resource-manager-api-profiles"></a>Perfiles de la API de Azure Resource Manager

Azure Stack no utiliza la última versión de las versiones de la API que se encuentran en Azure global. Al crear su propia solución, necesita encontrar la versión de API para cada proveedor de recursos en Azure que sea compatible con Azure Stack.

En lugar de investigar cada proveedor de recursos y la versión específica admitida por Azure Stack, puede usar un perfil de API. El perfil especifica un conjunto de proveedores de recursos y las versiones de API. El SDK o una herramienta integrada con el SDK, devolverá a la versión de API de destino especificada en el perfil. Con los perfiles de API, puede especificar una versión de perfil que se aplique a una plantilla completa y, en tiempo de ejecución, Azure Resource Manager selecciona la versión correcta del recurso.

Los perfiles de API funcionan con herramientas que utilizan Azure Resource Manager, como PowerShell, CLI de Azure, código proporcionado en el SDK y Microsoft Visual Studio. Las herramientas y los SDK pueden utilizar perfiles para leer la versión de los módulos y bibliotecas que se van incluir al crear una aplicación.

Por ejemplo, si utiliza PowerShell para crear una cuenta de almacenamiento con el proveedor de recursos **Microsoft.Storage** , que admite la versión de API 2016-03-30 y una máquina virtual que utiliza el proveedor de recursos Microsoft.Compute con la versión de API 2015-12-01, deberá buscar qué módulo de PowerShell admite 2016-03-30 para Storage y qué módulo admite 2015-02-01 para Compute e instalarlos. En su lugar, puede usar un perfil. Use el cmdlet **Install-Profile *profilename *** y PowerShell carga la versión correcta de los módulos.

De forma similar, cuando usa el SDK de Python para crear una aplicación basada en Python, puede especificar el perfil. El SDK carga los módulos correctos para los proveedores de recursos que ha especificado en el script.

Como desarrollador, puede centrarse en escribir la solución. En lugar de investigar qué versiones de API, qué proveedor de recursos y qué nube funcionan juntos, utilice un perfil y conozca qué código va a funcionar en todas las nubes que admiten ese perfil.

## <a name="api-profile-code-samples"></a>Ejemplos de código de perfil de API

Puede encontrar ejemplos de código que le ayudarán a integrar la solución con el idioma que prefiera con Azure Stack mediante el uso de perfiles. Actualmente, puede encontrar guía y ejemplos para los siguientes idiomas:

- **PowerShell**  
Se puede usar el módulo **AzureRM.Bootstrapper** disponible mediante la Galería de PowerShell para obtener cmdlets de PowerShell que son necesarios para trabajar con perfiles de la versión de la API.  
Para más información, consulte el artículo sobre el [uso de perfiles de versión de API para PowerShell](azure-stack-version-profiles-powershell.md).
- **CLI de Azure 2.0**  
Puede actualizar la configuración del entorno para usar el perfil de la versión de API específico de Azure Stack.  
Para más información, consulte el artículo acerca del [uso de perfiles de versión de API para la CLI de Azure 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
En el SDK de GO, un perfil es una combinación de diferentes tipos de recursos con distintas versiones de diferentes servicios. Los perfiles están disponibles en la ruta de acceso profiles/, con la versión en formato **AAAA-MM-DD**.  
Para más información, consulte el artículo sobre el [uso de perfiles de versión de API para GO](azure-stack-version-profiles-go.md).

## <a name="next-steps"></a>Pasos siguientes
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)
* [Revise los detalles sobre las versiones de API del proveedor de recursos admitidas por los perfiles](azure-stack-profiles-azure-resource-manager-versions.md).
