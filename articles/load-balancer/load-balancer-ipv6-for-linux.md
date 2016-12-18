---
title: "Configuración de DHCPv6 para VM de Linux | Microsoft Docs"
description: "Cómo configurar DHCPv6 para VM de Linux."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
keywords: "IPv6, Azure Load Balancer, pila doble, dirección ip pública, ipv6 nativo, móvil, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: cf1eafc7bca5bddeb32f1e1e05e660d6877ed805
ms.openlocfilehash: f14a0473ebcf21e04759cb8eb77eb684c4d9a9cb

---

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configuración de DHCPv6 para VM de Linux

Algunas de las imágenes de la máquina virtual con Linux en Azure Marketplace no tienen DHCPv6 configurado de forma predeterminada. Para admitir IPv6, DHCPv6 debe configurarse en la distribución de sistema operativo Linux que usa. Las distintas distribuciones de Linux presentan diversas formas de configurar DHCPv6 al usar paquetes diferentes.

> [!NOTE]
> En Azure Marketplace se han configurado previamente imágenes de SUSE Linux y CoreOS recientes con DHCPv6. No es necesario realizar ningún cambio adicional al usar esas imágenes.

En este documento se describe cómo habilitar DHCPv6 para que su máquina virtual con Linux obtenga una dirección IPv6.

> [!WARNING]
> La edición incorrecta de archivos de configuración de red puede dar lugar a la pérdida de acceso de red a la máquina virtual. Recomendamos que pruebe los cambios de configuración en sistemas que no sean de producción. Las instrucciones de este artículo se han probado en las versiones más recientes de las imágenes Linux en Azure Marketplace. Consulte la documentación de su versión específica de Linux para obtener instrucciones más detalladas.

## <a name="ubuntu"></a>Ubuntu

1. Edite el archivo `/etc/dhcp/dhclient6.conf` y agregue la línea siguiente:

        timeout 10;

2. Edite la configuración de red de la interfaz eth0 con la siguiente configuración:

   * En **Ubuntu 12.04 y 14.04**, edite el archivo `/etc/network/interfaces.d/eth0.cfg`
   * En **Ubuntu 16.04**, edite el archivo `/etc/network/interfaces.d/50-cloud-init.cfg`

        iface eth0 inet6 auto        up sleep 5        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renueve la dirección IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Edite el archivo `/etc/dhcp/dhclient6.conf` y agregue la línea siguiente:

        timeout 10;

2. Edite el archivo `/etc/network/interfaces` y agregue la configuración siguiente:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renueve la dirección IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Edite el archivo `/etc/sysconfig/network` y agregue el parámetro siguiente:

        NETWORKING_IPV6=yes

2. Edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue los dos parámetros siguientes:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renueve la dirección IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-opensuse-13"></a>SLES 11 y openSUSE 13

En Azure se han configurado previamente imágenes de SLES y openSUSE recientes con DHCPv6. No es necesario realizar ningún cambio adicional al usar esas imágenes. Si tiene una máquina virtual basada en una imagen de SUSE anterior o personalizada, siga estos pasos:

1. Instale el paquete `dhcp-client` , en caso de ser necesario:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edite el archivo `/etc/sysconfig/network/ifcfg-eth0` y agregue el parámetro siguiente:

        DHCLIENT6_MODE='managed'

3. Renueve la dirección IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 y openSUSE Leap

En Azure se han configurado previamente imágenes de SLES y openSUSE recientes con DHCPv6. No es necesario realizar ningún cambio adicional al usar esas imágenes. Si tiene una máquina virtual basada en una imagen de SUSE anterior o personalizada, siga estos pasos:

1. Edite el archivo `/etc/sysconfig/network/ifcfg-eth0` y reemplace este parámetro

        #BOOTPROTO='dhcp4'

    por el siguiente valor:

        BOOTPROTO='dhcp'

2. Agregue el parámetro siguiente a `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renueve la dirección IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

En Azure se han configurado previamente imágenes de CoreOS recientes con DHCPv6. No es necesario realizar ningún cambio adicional al usar esas imágenes. Si tiene una máquina virtual basada en una imagen de CoreOS anterior o personalizada, siga estos pasos:

1. Edite el archivo `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renueve la dirección IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```



<!--HONumber=Nov16_HO3-->


