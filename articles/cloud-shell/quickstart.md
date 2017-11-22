---
title: "Guía de inicio rápido de Bash en Azure Cloud Shell | Microsoft Docs"
description: "Guía de inicio rápido para Bash en Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 4ed83b28fe5f0f1e9626115390f8697dfb2c8ead
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Guía de inicio rápido para Bash en Azure Cloud Shell

En este documento se detalla cómo usar Bash en Azure Cloud Shell en [Azure Portal](https://ms.portal.azure.com/).

> [!NOTE]
> También hay disponible una guía de inicio rápido de [PowerShell en Azure Cloud Shell](quickstart-powershell.md).

## <a name="start-cloud-shell"></a>Inicio de Cloud Shell
1. Inicie **Cloud Shell** en la navegación superior de Azure Portal <br>
![](media/quickstart/shell-icon.png)
2. Seleccione una suscripción para crear una cuenta de almacenamiento y un recurso compartido de Microsoft Azure Files
3. Seleccione "Create storage" (Creación de almacenamiento)

> [!TIP]
> Usted se autentica automáticamente para la CLI de Azure 2.0 en todas las sesiones.

### <a name="select-the-bash-environment"></a>Selección del entorno Bash
1. Seleccione el menú desplegable de entornos que se encuentra al lado izquierdo de la ventana del shell <br>
![](media/quickstart/env-selector.png)
2. Seleccione Bash

### <a name="set-your-subscription"></a>Establecimiento de la suscripción
1. Enumere las suscripciones a las que tiene acceso: <br>
`az account list`
2. Establezca su suscripción preferida: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> La suscripción se recordará para sesiones futuras mediante `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un nuevo grupo de recursos en la región oeste de EE. UU. llamado "MyRG": <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Creación de una máquina virtual Linux
Cree una máquina virtual con Ubuntu en su nuevo grupo de recursos. La CLI de Azure 2.0 creará claves SSH y configurará la máquina virtual con ellas. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> La CLI de Azure 2.0 coloca las claves públicas y privadas usadas para autenticar la máquina virtual en `/User/.ssh/id_rsa` y `/User/.ssh/id_rsa.pub` de forma predeterminada. La carpeta .ssh se conserva en la imagen de 5 GB adjunta del recurso compartido de Azure Files.

Su nombre de usuario en esta máquina virtual será el nombre de usuario utilizado en Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Conexión SSH con la máquina virtual Linux
1. Busque el nombre de la máquina virtual en la barra de búsqueda de Azure Portal
2. Haga clic en "Conectar" y ejecute: `ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

Al establecer la conexión SSH, debería ver el aviso de bienvenida de Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpiar 
Elimine el grupo de recursos y cualquier recurso dentro del mismo: <br>
Ejecute `az group delete -n MyRG`

## <a name="next-steps"></a>Pasos siguientes
[Información sobre la persistencia de los archivos para Bash en Cloud Shell](persisting-shell-storage.md) <br>
[Más información sobre la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Información sobre el almacenamiento de Azure Files](../storage/files/storage-files-introduction.md) <br>