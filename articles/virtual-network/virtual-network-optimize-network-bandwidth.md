---
title: "Optimización del rendimiento de la red de una máquina virtual | Microsoft Docs"
description: "Aprenda a optimizar el rendimiento de la red de una máquina virtual de Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: d77440fe62bbd0e720e5ae60b15574dacc4180c0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimización del rendimiento de red en las máquinas virtuales de Azure

Las máquinas virtuales de Azure (VM) tienen una configuración de red predeterminada que se puede optimizar para mejorar aún más el rendimiento de la red. En este artículo se describe cómo optimizar el rendimiento de la red de las máquinas virtuales Windows y Linux de Microsoft Azure, incluidas las distribuciones principales como Ubuntu, CentOS y Red Hat.

## <a name="windows-vm"></a>Máquina virtual de Windows

Si la máquina virtual Windows es compatible con [redes aceleradas](virtual-network-create-vm-accelerated-networking.md), habilite esta característica para conseguir un rendimiento óptimo. En el caso de otras máquinas virtuales Windows, el uso de escalado en el lado de la recepción (RSS) pueden logar un rendimiento máximo mayor que las que no lo usan. En las máquinas virtuales Windows, RSS se puede deshabilitar de forma predeterminada. Complete los siguientes pasos para determinar si RSS está habilitado y, si no lo está, para habilitarlo.

1. Escriba el comando `Get-NetAdapterRss` de PowerShell para ver si RSS está habilitado para un adaptador de red. En la siguiente salida de ejemplo que devuelve `Get-NetAdapterRss`, RSS no está habilitado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Escriba el siguiente comando para habilitar RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    El comando anterior no tiene ninguna salida. El comando cambió la configuración de NIC, lo que ha provocado una pérdida temporal de la conectividad durante aproximadamente un minuto. Durante la pérdida de conectividad aparece un cuadro de diálogo de reconexión. La conectividad se suele restaurar al tercer intento.
3. Confirme que RSS está habilitado en la máquina virtual, para lo que debe volver a escribir el comando `Get-NetAdapterRss`. Si se realiza correctamente, se devuelve la siguiente salida de ejemplo:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Máquina virtual de Linux

De manera predeterminada, en las máquinas virtuales Linux de Azure RSS está siempre habilitado. Los kernels de Linux lanzados desde octubre de 2017 incluyen nuevas opciones de optimización de red que permiten que las máquinas virtuales Linux logren un mayor rendimiento de la red.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para las nuevas implementaciones

Para obtener la optimización, primero instale la última versión compatible de 16.04-LTS, así:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Una vez que la actualización se complete, escriba los comandos siguientes para obtener el kernel más reciente:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Comando opcional:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Actualización del kernel de Azure de Ubuntu para las máquinas virtuales existentes

Se puede lograr un rendimiento significativo instalando el kernel de Linux de Azure propuesto. Para comprobar si tienen este kernel, compruebe la versión del kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Luego, ejecute estos comandos como raíz.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obtener las optimizaciones más recientes, primero realice la actualización a la última versión compatible así:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
Una vez que la actualización se complete, instale los servicios de integración de Linux (LIS) más recientes.
La optimización del rendimiento es en LIS, a partir de 4.2.2-2, aunque las versiones posteriores contienen más mejoras. Escriba los siguientes comandos para instalar el LIS más reciente:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obtener la optimización, primero realice la actualización a la última versión compatible así:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
Una vez que la actualización se complete, instale los servicios de integración de Linux (LIS) más recientes.
La optimización del rendimiento se realiza en LIS a partir de la versión 4.2. Escriba los siguientes comandos para descargar e instalar LIS:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Para más información sobre la versión 4.2 de Linux Integration Services para Hyper-V, vea la [página de descarga](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Pasos siguientes
* Ahora que la máquina virtual está optimizada, vea el resultado con [pruebas de ancho de banda y rendimiento (NTTTCP)](virtual-network-bandwidth-testing.md) para su escenario.
* Obtenga más información con las [Preguntas más frecuentes (P+F) acerca de Azure Virtual Network](virtual-networks-faq.md)
