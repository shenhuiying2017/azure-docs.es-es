---
title: Uso de claves SSH con Windows para máquinas virtuales Linux | Microsoft Docs
description: Obtenga información acerca de cómo generar y utilizar claves SSH en un equipo de Windows para conectarse a una máquina virtual con Linux en Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Uso de claves SSH con Windows en Azure

En este artículo se presentan maneras de generar y utilizar claves de Secure Shell (SSH) en un equipo Windows para crear y conectarse a una máquina virtual (VM) Linux en Azure. Para usar las claves SSH desde un cliente Linux o macOS, consulte la guía [rápida](mac-create-ssh-keys.md) o [detallada](create-ssh-keys-detailed.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Paquetes de Windows y los clientes SSH
Para conectarse a máquinas virtuales Linux de Azure y administraras, se usa un cliente *ssh*. Los equipos que ejecutan Linux o macOS suelen tener un conjunto de comandos SSH para generar y administrar claves SSH, y para establecer conexiones SSH. 

Los equipos Windows no siempre tienen comandos SSH comparables instalados. Las versiones Windows 10 que incluyen el [subsistema de Windows para Linux](https://docs.microsoft.com/windows/wsl/about) permiten ejecutar utilidades y acceder a ellas, como un cliente SSH de forma nativa dentro del shell de Bash. 

Si desea usar algo distinto de Bash para Windows, en los paquetes siguientes se incluyen clientes comunes de Windows SSH que puede instalar localmente:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git para Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Otra opción consiste en usar las utilidades SSH disponibles en Bash en [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Acceda a Cloud Shell en el explorador web en [ https://shell.azure.com ](https://shell.azure.com) o en [Azure Portal](https://portal.azure.com). 
* Acceda a Cloud Shell como terminal desde Visual Studio Code mediante la instalación de la [extensión Cuenta de Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Creación de un par de claves SSH
En esta sección se muestran dos opciones para crear un par de claves SSH en Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Crear claves SSH con ssh-keygen

Si puede ejecutar un shell de comandos, como Bash para Windows o GitBash (o Bash en Azure Cloud Shell), cree un par de claves SSH mediante el comando `ssh-keygen`. Escribe el siguiente comando y responda a los mensajes. Si existe un par de claves SSH en la ubicación actual, esos archivos se sobrescribirán. 

```bash
ssh-keygen -t rsa -b 2048
```

Para ver otras consideraciones y obtener información, vea los pasos [rápidos](mac-create-ssh-keys.md) o [detallados](create-ssh-keys-detailed.md) para crear claves con `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Crear claves SSH con PuTTYgen

Si prefiere usar una herramienta basada en GUI para crear claves SSH, puede usar el generador de claves PuTTYgen, incluido con el [paquete de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Para crear un par de claves SSH RSA con PuTTYgen:

1. Inicie PuTTYgen.

2. Haga clic en **Generar**. De forma predeterminada, PuTTYgen genera una clave SSH-2 RSA de 2048 bits.

4. Mueva el mouse sobre el área en blanco para generar algo de aleatoriedad para la clave.

5. Una vez generada la clave pública, tiene la opción de escribir y confirmar una frase de contraseña. Se le pedirá la frase de contraseña cuando se autentique en la máquina virtual con la clave SSH. Sin una frase de contraseña, si alguien obtiene su clave privada, podrá iniciar sesión en cualquier máquina virtual o servicio que utilice dicha clave. Por consiguiente, se recomienda crear una frase de contraseña. Sin embargo, si se le olvida la frase de contraseña, no hay forma de recuperarla.

6. La clave pública se muestra en la parte superior de la ventana. Cuando cree una máquina virtual Linux, copie y pegue esta clave pública de una línea en Azure Portal o en una plantilla de Azure Resource Manager. También puede hacer clic en **Save public key** (Guardar clave pública) para guardar una copia en el equipo:

    ![Guardar archivo de clave pública de PuTTY](./media/ssh-from-windows/save-public-key.png)

7. De manera opcional, para guardar la clave privada con el formato de clave privada de PuTTy (archivo .ppk), haga clic en **Save private key** (Guardar clave privada). Necesita el archivo .ppk si quiere usar PuTTY más adelante para establecer una conexión SSH a la máquina virtual.

    ![Guardar el archivo de clave privada de PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Si quiere guardar la clave privada con el formato de OpenSSH (formato de clave privada que usan muchos clientes de SSH), haga clic en **Conversiones** > **Export OpenSSH key** (Exportar clave OpenSSH).

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Especificación de la clave pública SSH al implementar una máquina virtual

Para crear una VM Linux que use claves SSH para la autenticación, proporcione la clave pública SSH al crear la máquina virtual mediante Azure Portal u otros métodos.

En el siguiente ejemplo se muestra cómo se copia y pega esta clave pública en Azure Portal al crear una máquina virtual Linux. La clave pública se suele almacenar en `~/.ssh/authorized_keys` en la máquina virtual nueva.

   ![Usar la clave pública al crear una máquina virtual en Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Conexión a la máquina virtual

Una manera de establecer una conexión SSH a la máquina virtual Linux desde Windows es usar un cliente SSH. Este es el método preferido si tiene un cliente SSH instalado en el sistema Windows o si usa herramientas SSH en Bash en Azure Cloud Shell. Si prefiere una herramienta basada en GUI, puede conectarse con PuTTY.  

### <a name="use-an-ssh-client"></a>Uso de un cliente SSH
Con la clave pública implementada en la VM de Azure y la clave privada en el sistema local, ejecute SSH en la máquina virtual con la dirección IP o el nombre DNS de la máquina virtual. Reemplace *azureuser* y *myvm.westus.cloudapp.azure.com* en el siguiente comando por el nombre de usuario del administrador y el nombre de dominio completo (o la dirección IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si configuró una frase de contraseña cuando creó el par de claves, escríbala cuando se le solicite durante el proceso de inicio de sesión.

### <a name="connect-with-putty"></a>Conexión con PuTTY

Si instaló el [paquete de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) y ya había generado previamente una clave privada de PuTTY (archivo .ppk), puede conectarse a la máquina virtual Linux con PuTTY.

1. Inicie PuTTY.

2. Rellene el nombre de host o la dirección IP de la máquina virtual en Azure Portal:

    ![Abrir conexión nueva de PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Antes de seleccionar **Open** (Abrir), haga clic en la pestaña **Connection** (Conexión) > **SSH** > **Auth** (Autorización). Busque y seleccione la clave privada de PuTTY (archivo .ppk):

    ![Seleccionar la clave privada de PuTTY para la autenticación](./media/ssh-from-windows/putty-auth-dialog.png)

4. Haga clic en **Open** (Abrir) para conectarse a la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener pasos detallados, opciones y ejemplos avanzados del uso de claves SSH, consulte los [pasos detallados para crear pares de claves SSH](create-ssh-keys-detailed.md).

* También puede usar Powershell en Azure Cloud Shell para generar claves SSH y establecer conexiones SSH a máquinas virtuales Linux. Consulte [Inicio rápido de PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Si tiene problemas con el uso de SSH para conectarse a máquinas virtuales Linux, consulte [Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
