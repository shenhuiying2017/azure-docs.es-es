---
title: Suscripciones de Azure Government | Microsoft Docs
description: "Información sobre la administración de su suscripción en Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/21/2016
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Administración y conexión a su suscripción en Azure Government
Azure Government presenta direcciones URL y puntos de conexión únicos para administrar su entorno. Es importante usar las conexiones adecuadas para administrar el entorno mediante el portal o PowerShell. Después de conectarse al entorno de Azure Government, las operaciones normales para administrar un servicio funcionan si el componente se ha implementado.

## <a name="connecting-via-the-portal"></a>Conexión mediante el portal
El portal es la principal manera que tiene la mayoría de la gente de conectarse a Azure Government.  Para conectarse, vaya al portal en [https://portal.azure.us](https://portal.azure.us).  La versión heredada de Azure Portal puede obtenerse mediante [https://manage.windowsazure.us](https://manage.windowsazure.us).

Se pueden crear suscripciones para la cuenta mediante la conexión a [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Conexión mediante PowerShell
Si va a usar Azure PowerShell para administrar una suscripción grande mediante un script o acceder a características que no están actualmente disponibles en Azure Portal, deberá conectarse a Azure Government, en lugar de a Azure público.  Si ha usado PowerShell en Azure público, básicamente es lo mismo.  Las diferencias en Azure Government son:

* Conexión a la cuenta
* Nombres de región

> [!NOTE]
> Si todavía no ha usado PowerShell, consulte [Introducción a Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

Al iniciar PowerShell, tendrá que indicar a Azure PowerShell que se conecte a Azure Government mediante la especificación de un parámetro de entorno.  El parámetro garantiza que PowerShell se conecta a los puntos de conexión correctos.  La colección de puntos de conexión se determina cuando inicia la sesión en su cuenta.  Diferentes API requieren versiones diferentes del conmutador de entorno:

| Tipo de conexión | Comando |
| --- | --- |
| Comandos de [Administración de servicios](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Comandos de [Administración de recursos](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Comandos de [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Comando v2 de Azure Active Directory](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Línea de comandos de la CLI de Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

También puede usar el conmutador de entorno al conectarse a una cuenta de almacenamiento mediante New-AzureStorageContext y especificar AzureUSGovernment.

### <a name="determining-region"></a>Determinación de la región
Cuando está conectado, hay una diferencia adicional: las regiones usadas como destino de un servicio.  Cada nube de Azure tiene diferentes regiones.  Puede verlas en la página de disponibilidad del servicio.  Normalmente, se usa la región en el parámetro Location de un comando.

Hay una instrucción catch.  Las regiones de Azure Government tienen que tener un formato diferente de sus nombres comunes:

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

## <a name="connecting-via-visual-studio"></a>Conexión a través de Visual Studio
Los desarrolladores usan Visual Studio para administrar fácilmente sus suscripciones de Azure y crear soluciones.  Visual Studio no le permite actualmente configurar una conexión a Azure Government en la interfaz de usuario.  

### <a name="updating-visual-studio-for-azure-government"></a>Actualización de Visual Studio para Azure Government
Para permitir que Visual Studio se conecte a Azure Government, debe actualizar el Registro.

1. Cierre Visual Studio.
2. Cree un archivo de texto llamado **VisualStudioForAzureGov.reg**
3. Copie y pegue el siguiente texto en **VisualStudioForAzureGov.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Guarde y luego ejecute el archivo haciendo doble clic en él.  Se le pedirá que combine el archivo en el Registro.
5. Inicie Visual Studio y comience a usar [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md).

> [!NOTE]
> Cuando esta clave del Registro está establecida, solo las suscripciones de Azure Government son accesibles.  Seguirá viendo las suscripciones que configuró anteriormente, pero no funcionarán porque Visual Studio está ahora conectado a Azure Government en lugar de a Azure Public.  Consulte la sección siguiente para conocer los pasos para revertir los cambios.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Reversión de la conexión de Visual Studio a Azure Government
Para permitir que Visual Studio se conecte a Azure Public, debe quitar la configuración del Registro que permite la conexión a Azure Government.

1. Cierre Visual Studio.
2. Cree un archivo de texto llamado **VisualStudioForAzureGov_Remove.reg**.
3. Copie y pegue el siguiente texto en **VisualStudioForAzureGov_Remove.reg**:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Guarde y luego ejecute el archivo haciendo doble clic en él.  Se le pedirá que combine el archivo en el Registro.
5. Inicio de Visual Studio

> [!NOTE]
> Cuando haya revertido esta clave del Registro, las suscripciones de Azure Government se mostrarán pero no serán accesibles.  Se pueden quitar de forma segura.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Si busca más información, puede consultar:

* [Documentos de PowerShell en GitHub](https://github.com/Azure/azure-powershell)
* [Instrucciones paso a paso sobre cómo conectarse a Administración de recursos](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [Documentos de Azure PowerShell en MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Para información complementaria y actualizaciones, suscríbase al [blog de Microsoft Azure Government. ](https://blogs.msdn.microsoft.com/azuregov/).




<!--HONumber=Dec16_HO2-->


