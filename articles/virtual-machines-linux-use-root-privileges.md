<properties linkid="manage-linux-common-tasks-user-root-privileges" urlDisplayName="Use root privileges" pageTitle="Use root privileges on Linux virtual machines in Azure" metaKeywords="" description="Learn how to use root privileges on a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Uso de privilegios raíz en máquinas virtuales con Linux en Azure

Los usuarios pueden ejecutar comandos con privilegios elevados en una máquina virtual con Linux con la ayuda del comando `sudo`. Sin embargo, la experiencia puede variar dependiendo de la manera en que se aprovisionó el sistema.

1.  **Clave SSH y contraseña o solo contraseña**: La máquina virtual se aprovisionó con un certificado (archivo `.CER`) además de una contraseña, o solo con un nombre de usuario y una contraseña. En este caso `sudo` solicitará la contraseña del usuario antes de ejecutar el comando.

2.  **Solo clave SSH**: La máquina virtual se aprovisionó con un certificado (archivo `.cer` o `.pem`), pero sin contraseña. En este caso `sudo` **no** solicitará la contraseña del usuario antes de ejecutar el comando.

## Clave SSH y contraseña o solo contraseña

Inicie sesión en la máquina virtual con Linux usando la autenticación de clave SSH o contraseña y, a continuación, ejecute los comandos usando `sudo`, por ejemplo:

    # sudo <command>
    [sudo] password for azureuser:

En este caso, se le solicitará al usuario una contraseña. Después de escribir la contraseña, `sudo` ejecutará el comando con los privilegios `root`.

## Solo clave SSH

Inicie sesión en la máquina virtual con Linux usando la autenticación de clave SSH y, a continuación, ejecute los comandos usando `sudo`, por ejemplo:

    # sudo <command>

En este caso, **no** se le solicitará al usuario una contraseña. Después de pulsar, `<enter>`, `sudo` ejecutará el comando con los privilegios `root`.

