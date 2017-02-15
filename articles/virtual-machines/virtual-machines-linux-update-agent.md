---
title: "Actualización del agente Linux de Azure desde Github | Microsoft Docs"
description: "Obtenga información acerca de cómo actualizar el agente Linux de Azure para la máquina virtual de Linux en Azure a la versión más reciente de Github"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: e5baf0c89325abbef33bbabf1cfe29b878079e44


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Actualización del Agente de Linux de Azure en una máquina virtual a la última versión desde Github
Para actualizar el [Agente de Linux de Azure](https://github.com/Azure/WALinuxAgent) en una máquina virtual Linux, debe:

1. Tener en Azure una VM que ejecuta Linux.
2. Estar conectado a esa VM de Linux mediante SSH

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Si va a realizar esta tarea desde un equipo con Windows, utilice PuTTY para SSH en la máquina con Linux. Para más información, consulte [Inicio de sesión en una máquina virtual Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Las distribuciones de Linux aprobadas en Azure han colocado el paquete del agente Linux de Azure en sus repositorios, así que, si es posible, compruebe e instale primero la versión más reciente de ese repositorio de distribuciones.  

Para Ubuntu, basta con escribir:

```bash
sudo apt-get install walinuxagent
```

En CentOS, escriba:

```bash
sudo yum install waagent
```

Para Oracle Linux, asegúrese de que el repositorio `Addons` está habilitado. Elija editar el archivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) u `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) y cambie la línea `enabled=0` a `enabled=1` en **[ol6_addons]** o **[ol7_addons]** en este archivo.

Luego, instale la versión más reciente del Agente de Linux de Azure de tipo:

```bash
sudo yum install WALinuxAgent
```

Si no encuentra el repositorio de complementos, basta con agregar estas líneas al final del archivo .repo según la versión de Oracle Linux:

Para las máquinas virtuales Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Para las máquinas virtuales Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

A continuación, escriba:

```bash
sudo yum update WALinuxAgent
```

Normalmente, esto es todo lo que necesita, pero si por alguna razón necesita instalarlo desde https://github.com directamente, siga estos pasos.

## <a name="install-wget"></a>Instalar wget
Inicie sesión en la máquina virtual con SSH.

Instale wget (hay algunas distribuciones que no lo instalan de forma predeterminada, como las versiones Redhat, CentOS y Oracle Linux 6.4 y 6.5) y escriba `#sudo yum install wget` en la línea de comandos.

## <a name="download-the-latest-version"></a>Descargar la versión más reciente
Abra [la versión del Agente de Linux de Azure en Github](https://github.com/Azure/WALinuxAgent/releases) en una página web y compruebe el número de versión más reciente. (Puede buscar la versión actual escribiendo `#waagent --version`).

### <a name="for-version-20x-type"></a>Para la versión 2.0.x, escriba:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

La línea siguiente usa la versión 2.0.14 como ejemplo:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>Para la versión 2.1.x o posteriores, escriba:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

La línea siguiente usa la versión 2.1.0 como ejemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Instalación del agente de Linux de Azure
### <a name="for-version-20x-use"></a>Para la versión 2.0.x, use:
Convertir waagent en ejecutable:

```bash
chmod +x waagent
```

Copia del nuevo archivo ejecutable en /usr/sbin/.

Para la mayoría de las distribuciones de Linux, use:

```bash
sudo cp waagent /usr/sbin
```

Para CoreOS, use:

```bash
sudo cp waagent /usr/share/oem/bin/
```

Si se trata de una nueva instalación del Agente de Linux de Azure, ejecute:

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>Para la versión 2.1.x, use:
Es posible que necesite instalar primero el paquete. Consulte `setuptools` first--see [aquí](https://pypi.python.org/pypi/setuptools). A continuación, ejecute:

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent
Para la mayoría de las distribuciones de Linux:

```bash
sudo service waagent restart
```

Para Ubuntu, use:

```bash
sudo service walinuxagent restart
```

Para CoreOS, use:

```bash
sudo systemctl restart waagent
```

## <a name="confirm-the-azure-linux-agent-version"></a>Confirmación de la versión del agente Linux de Azure
    
```bash
waagent -version
```

Para CoreOS, es posible que el comando anterior no funcione.

Verá que la versión del Agente de Linux de Azure se actualizó a la versión nueva.

Para más información sobre el Agente de Linux de Azure, consulte el archivo [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent)(Léame del Agente de Linux de Azure).




<!--HONumber=Nov16_HO3-->


