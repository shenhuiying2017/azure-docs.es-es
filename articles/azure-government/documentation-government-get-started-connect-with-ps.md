---
title: "Conexión de Azure Government con PowerShell | Microsoft Docs"
description: "Información sobre la conexión de su suscripción a Azure Government con PowerShell"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>Conexión de Azure Government con PowerShell
Para usar la CLI de Azure, necesita conectarse con Azure Government en lugar de Azure Public. La CLI de Azure puede usarse para administrar una suscripción de gran tamaño a través del script o para tener acceso a características que no están actualmente disponibles en Azure Portal. Si ha usado PowerShell en Azure público, básicamente es lo mismo.  Las diferencias en Azure Government son:

* Conexión a la cuenta
* Nombres de región

> [!NOTE]
> Si todavía no ha usado PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Al iniciar PowerShell, tendrá que indicar a Azure PowerShell que se conecte a Azure Government mediante la especificación de un parámetro de entorno.  El parámetro garantiza que PowerShell se conecta a los puntos de conexión correctos.  La colección de puntos de conexión se determina cuando inicia la sesión en su cuenta.  Diferentes API requieren versiones diferentes del conmutador de entorno:

| Tipo de conexión | Comando |
| --- | --- |
| Comandos de [Azure Active Directory (modelo de implementación clásica)](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Comandos de [Administración de recursos](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Comandos de [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| Comandos de [Azure Active Directory (modelo de implementación Resource Manager)](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Línea de comandos de la CLI de Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

También puede usar el conmutador de entorno al conectarse a una cuenta de almacenamiento mediante New-AzureStorageContext y especificar AzureUSGovernment.

### <a name="determining-region"></a>Determinación de la región
Cuando está conectado, hay una diferencia adicional: las regiones usadas como destino de un servicio.  Cada nube de Azure tiene diferentes regiones.  Puede verlas en la página de disponibilidad del servicio.  Normalmente, se usa la región en el parámetro Location de un comando.

Hay una instrucción catch.  Las regiones de Azure Government tienen un formato diferente al de sus nombres comunes:

| Nombre común | Comando |
| --- | --- |
| Gobierno de EE. UU. - Virginia |USGov Virginia |
| Gobierno de EE. UU. - Iowa |USGov Iowa |

> [!NOTE]
> No hay ningún espacio entre EE. UU. y Gobierno al usar el parámetro Location.
> 
> 

Si desea validar las regiones disponibles en Azure Government, puede ejecutar los siguientes comandos e imprimir la lista actual:

    Get-AzureLocation

Si siente curiosidad sobre los entornos disponibles en Azure, puede ejecutar:

    Get-AzureEnvironment

