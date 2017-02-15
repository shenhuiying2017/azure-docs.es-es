---
title: "Uso de privilegios raíz en máquinas virtuales Linux | Microsoft Docs"
description: "Aprenda a usar privilegios raíz en una máquina virtual de Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: a2c106a2-dceb-43a3-9dd1-50ed77685952
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1c8c5f4e622863a0961bd5e1c7a915e9511c871f


---
# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Uso de privilegios raíz en máquinas virtuales con Linux en Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

De forma predeterminada, el usuario `root` está deshabilitado en las máquinas virtuales Linux en Azure. Los usuarios pueden ejecutar comandos con privilegios elevados mediante el comando `sudo` . Sin embargo, la experiencia puede variar dependiendo de la manera en que se aprovisionó el sistema.

1. **Clave SSH y contraseña o solo contraseña**: la máquina virtual se aprovisionó con un certificado (archivo `.CER`) o una clave SSH, además de una contraseña, o solo un nombre de usuario y una contraseña. En este caso `sudo` solicitará la contraseña del usuario antes de ejecutar el comando.
2. **Solo clave SSH**: la máquina virtual se aprovisionó con un certificado (archivo `.cer`, `.pem` o `.pub`) o una clave SSH, pero sin contraseña.  En este caso `sudo` **no** solicitará la contraseña del usuario antes de ejecutar el comando.

## <a name="ssh-key-and-password-or-password-only"></a>Clave SSH y contraseña o solo contraseña
Inicie sesión en la máquina virtual Linux usando la autenticación de clave SSH o contraseña y, a continuación, ejecute los comandos usando `sudo`, por ejemplo:

    # sudo <command>
    [sudo] password for azureuser:

En este caso, se le solicitará al usuario una contraseña. Después de escribir la contraseña, `sudo` ejecutará el comando con los privilegios `root`.

También puede habilitar sudo sin contraseña, si edita el archivo `/etc/sudoers.d/waagent` , por ejemplo:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Este cambio permitirá que el usuario "azureuser" use sudo sin contraseña.

## <a name="ssh-key-only"></a>Solo clave SSH
Inicie sesión en la máquina virtual Linux usando la autenticación de clave SSH y, a continuación, ejecute los comandos usando `sudo`, por ejemplo:

    # sudo <command>

En este caso **no** se solicitará la contraseña al usuario. Después de presionar `<enter>`, `sudo` ejecutará el comando con privilegios `root`.




<!--HONumber=Nov16_HO3-->


