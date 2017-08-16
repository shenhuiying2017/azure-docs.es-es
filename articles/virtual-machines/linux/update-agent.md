---
title: "Actualización del agente Linux de Azure desde Github | Microsoft Docs"
description: "Obtenga información sobre cómo actualizar el agente Linux de Azure para la máquina virtual de Linux en Azure a la última versión de GitHub"
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
ms.date: 08/02/2017
ms.author: mingzhan
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: c79e37976a58ae5384b5856e0f7f258a773ef0fd
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Actualización del agente Linux de Azure en una máquina virtual

Para actualizar el [Agente de Linux de Azure](https://github.com/Azure/WALinuxAgent) en una máquina virtual Linux, debe:

- Tener en Azure una VM que ejecuta Linux.
- Estar conectado a esa VM de Linux mediante SSH

Siempre debe comprobar primero si existe un paquete en el repositorio de distribución de Linux. Es posible que el paquete disponible no se corresponda con la última versión, pero, al habilitar la actualización automática, se garantiza que el Agente Linux siempre obtenga la actualización más reciente. Si tiene problemas para instalar los administradores de paquetes, debe solicitar soporte técnico al proveedor de la distribución.

## <a name="updating-the-azure-linux-agent"></a>Actualización del Agente Linux de Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Actualización de la memoria caché del paquete

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent

#### <a name="restart-agent-for-1404"></a>Reinicio del agente para 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Reinicio del agente para 16.04/17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Actualización de la memoria caché del paquete

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Habilitación de la actualización automática del agente
Esta versión de Debian no tiene una versión > = 2.0.16, lo que significa que AutoUpdate no está disponible en este caso. La salida del comando anterior mostrará si el paquete está actualizado.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Actualización de la memoria caché del paquete

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada 

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Comprobación de las actualizaciones disponibles

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada 

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Comprobación de las actualizaciones disponibles

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada 

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Comprobación de las actualizaciones disponibles

La salida anterior mostrará si el paquete está actualizado.

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada 

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Comprobación de la versión del paquete actual

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Comprobación de las actualizaciones disponibles

En la salida anterior, se mostrará si el paquete está actualizado.

#### <a name="install-the-latest-package-version"></a>Instalación de la última versión del paquete

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada 

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicio del servicio waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 y 7

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


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Actualización del Agente Linux cuando no existe ningún paquete del agente para su distribución

Instale wget (hay algunas distribuciones que no lo instalan de forma predeterminada, como las versiones Red Hat, CentOS y Oracle Linux 6.4 y 6.5) y escriba `sudo yum install wget` en la línea de comandos.

### <a name="1-download-the-latest-version"></a>1. Descargar la versión más reciente
Abra [la versión del Agente de Linux de Azure en Github](https://github.com/Azure/WALinuxAgent/releases) en una página web y compruebe el número de versión más reciente. (Puede buscar la versión actual escribiendo `waagent --version`).

#### <a name="for-version-22x-or-later-type"></a>Para la versión 2.2.x o posteriores, escriba:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip.zip
cd WALinuxAgent-2.2.x
```

La línea siguiente usa la versión 2.2.0 como ejemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Instalación del agente de Linux de Azure

#### <a name="for-version-22x-use"></a>Para la versión 2.2.x, use:
Es posible que necesite instalar primero el paquete. Consulte `setuptools` first--see [aquí](https://pypi.python.org/pypi/setuptools). A continuación, ejecute:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Comprobación de que la actualización automática está habilitada

En primer lugar, compruebe si lo siguiente está habilitado:

```bash
cat /etc/waagent.conf
```

Busque "AutoUpdate.Enabled". Si ve esta salida, significa que la característica está habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar la ejecución:

```bash
sudo sed -i 's/AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reinicio del servicio waagent
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

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirmación de la versión del agente Linux de Azure
    
```bash
waagent -version
```

Para CoreOS, es posible que el comando anterior no funcione.

Verá que la versión del Agente de Linux de Azure se actualizó a la versión nueva.

Para más información sobre el Agente de Linux de Azure, consulte el archivo [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent)(Léame del Agente de Linux de Azure).
