---
title: "Uso del escritorio remoto a una máquina virtual Linux en Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo instalar y configurar el escritorio remoto (xrdp) para conectarse a una máquina virtual Linux en Azure con herramientas gráficas"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 1aeb983730f732a021b828c658cc741f8659c487
ms.openlocfilehash: 01a19f1070c1096b41599705bba246bd0cc45d09
ms.lasthandoff: 02/27/2017


---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalación y configuración del escritorio remoto para conectarse a una máquina virtual Linux en Azure
Las máquinas virtuales de Linux (VM) en Azure normalmente se administran desde la línea de comandos mediante una conexión de shell seguro (SSH). Cuando sean nuevas en Linux, o para escenarios de solución de problemas rápidos, el uso del escritorio remoto puede ser más fácil. En este artículo se detalla cómo instalar y configurar un entorno de escritorio ([xfce](https://www.xfce.org)) y el escritorio remoto ([xrdp](http://www.xrdp)) para la máquina virtual Linux con el modelo de implementación de Resource Manager. También puede [realizar estos pasos para máquinas virtuales con el modelo de implementación clásica](virtual-machines-linux-classic-remote-desktop.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


## <a name="prerequisites"></a>Requisitos previos
Este artículo requiere una máquina virtual de Linux existente en Azure. Si necesita crear una máquina virtual, utilice uno de los métodos siguientes:

- La [CLI de Azure 2.0](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o la [CLI de Azure 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

También necesita tener instaladas la versión más reciente de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) o la [CLI de Azure 1.0](../xplat-cli-install.md) y haber iniciado sesión en una [cuenta activa de Azure](https://azure.microsoft.com/pricing/free-trial/).


## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para instalar y configurar el escritorio remoto en la máquina virtual. En el resto del documento puede encontrar información más detallada y el contexto de cada paso, comenzando [aquí](#install-graphical-environment-on-linux-vm).

En el ejemplo siguiente se instala el entorno de escritorio [xfce4](https://www.xfce.org/) ligero en una máquina virtual de Ubuntu. Otras distribuciones y entornos de escritorio varían ligeramente. Por ejemplo, utilice **yum** para realizar la instalación en Red Hat Enterprise Linux y configurar las reglas de **selinux** adecuadas o use **zypper** para instalarlo en SUSE.

SSH en la máquina virtual. Instale el entorno de escritorio xfce como se indica a continuación:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

Instale xrdp como se indica a continuación:

```bash
sudo apt-get install xrdp
```

Configure xrdp para usar xfce como el entorno de escritorio de la siguiente forma:

```bash
echo xfce4-session >~/.xsession
```

Reinicie el servicio xrdp:

```bash
sudo service xrdp restart
```

Establezca una contraseña para su cuenta de usuario si actualmente solo usa la clave SSH para la autenticación:

```bash
sudo passwd ops
```

Salga de la sesión SSH para la máquina virtual Linux. Utilice la CLI de Azure en el equipo local para crear una regla de grupo de seguridad de red para permitir el tráfico de escritorio remoto. Use [az network nsg rule create](/cli/azure/network/nsg/rule#create) con la CLI de Azure 2.0. En el ejemplo siguiente, se crea una regla denominada "`myNetworkSecurityGroupRule`" en `myNetworkSecurityGroup` para permitir el tráfico TCP en el puerto 3389:
    
```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 3389 \
    --access allow
```

O bien con la CLI de Azure 1.0:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --destination-port-range 3389 --access allow
```

Conéctese a la máquina virtual Linux con el cliente de escritorio remoto que elija.

![Conectarse a xrdp mediante el cliente de escritorio remoto](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalar un entorno de escritorio en la máquina virtual Linux
La mayoría de las máquinas virtuales Linux en Azure no tienen un entorno de escritorio instalado de forma predeterminada. Las máquinas virtuales Linux normalmente se administran mediante conexiones SSH en lugar de con un entorno de escritorio. Existen varios entornos de escritorio en Linux que puede elegir. Según la elección del entorno de escritorio, se puede utilizar de 1 a 2 GB de espacio en disco y tardar de 5 a 10 minutos en instalar y configurar todos los paquetes necesarios.

En el ejemplo siguiente se instala el entorno de escritorio [xfce4](https://www.xfce.org/) ligero en una máquina virtual de Ubuntu. Los comandos para otras distribuciones varían ligeramente (use **yum** para realizar la instalación en Red Hat Enterprise Linux y configurar las reglas de **selinux** adecuadas o use **zypper** para realizar la instalación en SUSE, por ejemplo).

Primero, SSH en la máquina virtual. En el siguiente ejemplo se conecta a la máquina virtual denominada `myvm.westus.cloudapp.azure.com` con el nombre de usuario de `ops`:

```bash
ssh ops@myvm.westus.cloudapp.azure.com ~/.ssh/id_rsa.pub
```

Si está utilizando Windows y necesita más información sobre el uso de SSH, consulte [Uso de claves SSH con Windows](virtual-machines-linux-ssh-from-windows.md).

A continuación, instale xfce con `apt` como se indica a continuación:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalar y configurar un servidor de escritorio remoto
Ahora que tiene instalado un entorno de escritorio, configure un servicio de escritorio remoto para escuchar las conexiones entrantes. [xrdp](http://xrdp.org) es un servidor de protocolo de escritorio remoto (RDP) de código abierto que está disponible en la mayoría de las distribuciones de Linux y funciona bien con xfce. Instalar xrdp en la máquina virtual de Ubuntu de la siguiente forma:

```bash
sudo apt-get install xrdp
```

Indique a xrdp qué entorno de escritorio utilizar al iniciar la sesión. Configure xrdp para usar xfce como el entorno de escritorio de la siguiente forma:

```bash
echo xfce4-session >~/.xsession
```

Reinicie el servicio xrdp para que los cambios surtan efecto de la siguiente forma:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Establecer una contraseña de cuenta de usuario
Si ha creado una contraseña para su cuenta de usuario cuando se creó la máquina virtual, omita este paso. Si solo usa la autenticación de clave SSH y no tiene una contraseña de cuenta local establecida, especifique una contraseña antes de utilizar xrdp para iniciar sesión en la máquina virtual. xrdp no puede aceptar las claves SSH para la autenticación. En el ejemplo siguiente se especifica una contraseña para la cuenta de usuario `ops`:

```bash
sudo passwd ops
```

> [!NOTE]
> Si especifica una contraseña, no se actualizará la configuración sshd para permitir los inicios de sesión de contraseña si actualmente no es así. Desde la perspectiva de seguridad, puede que desee conectarse a la máquina virtual con un túnel SSH mediante la autenticación basada en claves y, a continuación, conectarse a xrdp. Si es así, omita el paso siguiente acerca de cómo crear una regla de grupo de seguridad de red para permitir el tráfico de escritorio remoto.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Crear una regla de grupo de seguridad de red para el tráfico de escritorio remoto
Para permitir que el tráfico de escritorio remoto llegue a la máquina virtual Linux, tiene que crearse una regla del grupo de seguridad de red que permita que el TCP del puerto 3389 llegue a la máquina virtual. Para más información acerca de las reglas de grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) También puede [usar Azure Portal para crear una regla de grupos de seguridad de red](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Los ejemplos siguientes crean una regla de grupo de seguridad de red denominada `myNetworkSecurityGroupRule` para el tráfico `allow` en `tcp`, puerto `3389`.

- Use [az network nsg rule create](/cli/azure/network/nsg/rule#create) con la CLI de Azure 2.0:
    
    ```azurecli
    az network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 3389 \
        --access allow
    ```

- También puede usar la CLI de Azure 1.0:

    ```azurecli
    azure network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --destination-port-range 3389 --access allow
    ```

## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Conectar la máquina virtual Linux con un cliente de escritorio remoto
Abra el cliente de escritorio remoto local y conéctese a la dirección IP o nombre DNS de la máquina virtual Linux. Escriba el nombre de usuario y la contraseña para la cuenta de usuario en la máquina virtual como se indica a continuación:

![Conectarse a xrdp mediante el cliente de escritorio remoto](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

Tras la autenticación, el entorno de escritorio de xfce se cargará y tendrá un aspecto similar al ejemplo siguiente:

![entorno de escritorio xfce a través de xrdp](./media/virtual-machines-linux-use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Solución de problemas
Si no se puede conectar a la máquina virtual de Linux con un cliente de escritorio remoto, use `netstat` en la máquina virtual Linux para comprobar que la máquina virtual está realizando escuchas para las conexiones RDP de la siguiente forma:

```bash
sudo netstat -plnt | grep rdp
```

En el ejemplo siguiente se muestran las escuchas de la máquina virtual en el puerto TCP 3389 según lo esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Si el servicio xrdp no está escuchando, en una máquina virtual de Ubuntu, reinicie el servicio como se indica a continuación:

```bash
sudo service xrdp restart
```

Revise los registros en `/var/log` en la máquina virtual de Ubuntu para obtener indicaciones sobre el motivo por el que puede que el servicio no responda. También puede supervisar el registro del sistema durante un intento de conexión al escritorio remoto para ver los errores:

```bash
tail -f /var/log/syslog
```

Otras distribuciones de Linux como Red Hat Enterprise Linux y SUSE pueden tener diferentes maneras de reiniciar los servicios y alternar las ubicaciones de archivo de registro que revisar.

Si no recibe ninguna respuesta en el cliente de escritorio remoto y no ve todos los eventos de registro del sistema, este comportamiento indica que el tráfico de escritorio remoto no puede llegar a la máquina virtual. Revise las reglas de grupo de seguridad de red para asegurarse de que tiene una regla para permitir el TCP en el puerto 3389. Para obtener más información, consulte [Solucionar problemas de conectividad de la aplicación](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo crear y utilizar claves de SSH con máquinas virtuales Linux, consulte [Crear claves SSH para máquinas virtuales de Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obtener información sobre el uso de SSH de Windows, vea [Uso de claves SSH con Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


