---
title: Acerca de las redes en Azure para la recuperación ante desastres de Azure mediante Azure Site Recovery | Microsoft Docs
description: Proporciona información general de las redes para la replicación de máquinas virtuales de Azure mediante Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/16/2018
ms.author: sujayt
ms.openlocfilehash: 55eed1afa9d9dbf2c16643cf9186a7e6dafa847e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211028"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Acerca de las redes en Azure para la replicación de Azure

>[!NOTE]
> La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.

En este artículo se proporciona la guía de redes cuando se van a replicar y recuperar máquinas virtuales de Azure de una región a otra, mediante [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de comenzar

Obtenga información sobre cómo Site Recovery proporciona recuperación ante desastres para [este escenario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infraestructura de red típica

El siguiente diagrama representa el entorno de Azure típico para aplicaciones que se ejecutan en máquinas virtuales de Azure:

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Si usa Azure ExpressRoute o una conexión VPN desde su red local a Azure, el entorno tendrá este aspecto:

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Normalmente, los clientes protegen sus redes mediante firewalls y grupos de seguridad de red (NSG). Los firewalls usan la inclusión en lista blanca basada en direcciones URL o IP para controlar la conectividad de red. Los NSG proporcionan reglas que usan intervalos de direcciones IP para controlar la conectividad de red.

>[!IMPORTANT]
> Site Recovery no admite el uso de un proxy autenticado para controlar la conectividad de red y la replicación no se podrá habilitar.


## <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en la dirección URL para controlar la conectividad de salida, admita estas direcciones URL de Site Recovery:


**URL** | **Detalles**  
--- | ---
* .blob.core.windows.net | Se requiere para que los datos se puedan escribir en la cuenta de almacenamiento de la caché en la región de origen de la máquina virtual.
login.microsoftonline.com | Se requiere para la autorización y la autenticación de las direcciones URL del servicio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Se requiere para la comunicación del servicio Site Recovery desde la máquina virtual.
*.servicebus.windows.net | Se requiere para que se puedan escribir datos de supervisión y diagnóstico de Site Recovery desde la máquina virtual.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

Si utiliza un firewall basado en IP, proxy o reglas NSG para controlar la conectividad saliente, estos intervalos IP tienen que permitirse.

- Todos los intervalos de direcciones IP que correspondan a las cuentas de almacenamiento en la región de origen.
    - Cree una regla de NSG basada en la [etiqueta del servicio Storage](../virtual-network/security-overview.md#service-tags) para la región de origen.
    - Permita estas direcciones para que los datos se puedan escribir en la cuenta de almacenamiento en caché, desde la máquina virtual.
- Todos los intervalos de direcciones IP que corresponden a los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) de Office 365.
    - Si se agregan en el futuro nuevas direcciones a los intervalos de Office 365, deberá crear nuevas reglas de NSG.
- Las direcciones IP de punto de conexión de servicio de Site Recovery ([disponibles en un archivo XML](https://aka.ms/site-recovery-public-ips)), que dependen de la ubicación de destino:
-  Puede [descargar y usar este script](https://aka.ms/nsg-rule-script), para crear automáticamente las reglas necesarias en el NSG.
- Se recomienda crear las reglas de NSG necesarias en un grupo NSG de NSG de prueba y comprobar que no haya ningún problema antes de crear las reglas en un grupo de NSG de producción.


Los intervalos de direcciones IP de Site Recovery son los siguientes:

   **Destino** | **IP de Site Recovery** |  **IP de supervisión de Site Recovery**
   --- | --- | ---
   Asia oriental | 52.175.17.132 | 13.94.47.61
   Sudeste asiático | 52.187.58.193 | 13.76.179.223
   India Central | 52.172.187.37 | 104.211.98.185
   Sur de la India | 52.172.46.220 | 104.211.224.190
   Centro-Norte de EE. UU | 23.96.195.247 | 168.62.249.226
   Europa del Norte | 40.69.212.238 | 52.169.18.8
   Europa occidental | 52.166.13.64 | 40.68.93.145
   Este de EE. UU | 13.82.88.226 | 104.45.147.24
   Oeste de EE. UU | 40.83.179.48 | 104.40.26.199
   Centro-Sur de EE. UU | 13.84.148.14 | 104.210.146.250
   Central EE. UU: | 40.69.144.231 | 52.165.34.144
   Este de EE. UU. 2 | 52.184.158.163 | 40.79.44.59
   Este de Japón | 52.185.150.140 | 138.91.1.105
   Oeste de Japón | 52.175.146.69 | 138.91.17.38
   Sur de Brasil | 191.234.185.172 | 23.97.97.36
   Australia Oriental | 104.210.113.114 | 191.239.64.144
   Sudeste de Australia | 13.70.159.158 | 191.239.160.45
   Centro de Canadá | 52.228.36.192 | 40.85.226.62
   Este de Canadá | 52.229.125.98 | 40.86.225.142
   Centro occidental de EE.UU | 52.161.20.168 | 13.78.149.209
   Oeste de EE. UU 2 | 52.183.45.166 | 13.66.228.204
   Oeste de Reino Unido | 51.141.3.203 | 51.141.14.113
   Sur del Reino Unido 2 | 51.140.43.158 | 51.140.189.52
   Sur del Reino Unido 2 | 13.87.37.4| 13.87.34.139
   Norte del Reino Unido | 51.142.209.167 | 13.87.102.68
   Corea Central | 52.231.28.253 | 52.231.32.85
   Corea del Sur | 52.231.298.185 | 52.231.200.144
   Centro de Francia | 52.143.138.106 | 52.143.136.55
   Sur de Francia | 52.136.139.227 |52.136.136.62


## <a name="example-nsg-configuration"></a>Configuración de NSG de ejemplo

En este ejemplo se muestra cómo configurar reglas de NSG para la replicación de una máquina virtual.

- Si usa reglas de NSG para controlar la conectividad de salida, utilice reglas para permitir el tráfico HTTPS de salida al puerto 443 para todos los intervalos de direcciones IP necesarios.
- En el ejemplo se supone que la ubicación de origen de la máquina virtual es "Este de EE. UU." y la ubicación de destino es "Centro de EE. UU".

### <a name="nsg-rules---east-us"></a>Reglas de NSG: este de EE. UU.

1. Cree una regla de seguridad HTTPS (443) de salida para "Storage.EastUS" en el NSG tal y como se muestra en la captura de pantalla siguiente.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Cree reglas HTTPS (443) de salida para todos los intervalos de direcciones IP que se correspondan con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Cree reglas HTTPS (443) de salida para las direcciones IP de Site Recovery que corresponden a la ubicación de destino:

   **Ubicación** | **Dirección IP de Site Recovery** |  **Dirección IP de supervisión de Site Recovery**
    --- | --- | ---
   Central EE. UU: | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reglas de NSG: centro de EE. UU.

Estas reglas son necesarias para que la replicación se pueda habilitar de la región de destino a la región de origen con posterioridad a la conmutación por error:

1. Cree una regla de seguridad HTTPS (443) de salida saliente para "Storage.CentralUS" en el NSG.

2. Cree reglas HTTPS (443) de salida para todos los intervalos de direcciones IP que se correspondan con los [puntos de conexión de autenticación e identidad IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

3. Cree reglas HTTPS (443) de salida para las direcciones IP de Site Recovery que corresponden a la ubicación de origen:

   **Ubicación** | **Dirección IP de Site Recovery** |  **Dirección IP de supervisión de Site Recovery**
    --- | --- | ---
   Central EE. UU: | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Configuración de la aplicación virtual de red

Si usa aplicaciones virtuales de red (NVA) para controlar el tráfico de red saliente de las máquinas virtuales, el dispositivo podría verse limitado si todo el tráfico de replicación pasa a través de la NVA. Se recomienda crear un punto de conexión de servicio de red en la red virtual de "Storage" para que el tráfico de replicación no se dirija a la NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Crear el punto de conexión de servicio de red de Storage
Puede crear un punto de conexión de servicio de red en la red virtual de "Storage" para que el tráfico de replicación no sobrepase el límite de Azure.

- Seleccione la red virtual de Azure y haga clic en "Puntos de conexión de servicio".

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Haga clic en "Agregar" y se abrirá la pestaña "Agregar extremos del servicio".
- Seleccione "Microsoft.Storage" en "Servicio", elija las subredes necesarias en el campo "Subredes" y haga clic en "Agregar".

>[!NOTE]
>No restrinja el acceso de red virtual a las cuentas de almacenamiento que usa para ASR. Debe permitir el acceso desde todas las redes.

### <a name="forced-tunneling"></a>Tunelización forzada

Puede invalidar la ruta del sistema predeterminada de Azure para el prefijo de dirección 0.0.0.0/0 con una [ruta personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) y desviar el tráfico de la máquina virtual a una aplicación virtual de red (NVA) local, pero esta configuración no se recomienda para la replicación de Site Recovery. Si va a usar rutas personalizadas, debe [crear un punto de conexión de servicio de red virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) en su red virtual de "Storage" para que el tráfico de replicación no salga de los límites de Azure.

## <a name="next-steps"></a>Pasos siguientes
- Comience a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](site-recovery-azure-to-azure.md).
- Más información sobre la [retención de direcciones IP](site-recovery-retain-ip-azure-vm-failover.md) en la conmutación por error de máquinas virtuales de Azure.
- Más información sobre la recuperación ante desastres de [máquinas virtuales de Azure con ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
