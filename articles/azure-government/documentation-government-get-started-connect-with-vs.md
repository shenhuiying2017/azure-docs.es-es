---
title: "Conexión a Azure Government con Visual Studio | Microsoft Docs"
description: "Información sobre la administración de su suscripción en Azure Government mediante conexión con Visual Studio"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Conexión a través de Visual Studio
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
> Cuando haya revertido esta clave del Registro, las suscripciones de Azure Government se mostrarán, pero no serán accesibles.  Se pueden quitar de forma segura.
> 
> 

