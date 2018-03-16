---
title: "Conservar las direcciones IP cuando se conmuta por error máquinas virtuales de Azure en otra región de Azure | Microsoft Docs"
description: "Aquí se describe cómo conservar las direcciones IP de Azure en escenarios de conmutación por error de Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 15f87ba87d90cee765f52d3188796bc1ff7b8a35
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Conservar las direcciones IP para la conmutación por error de una máquina virtual de Azure

Azure Site Recovery permite la recuperación ante desastres de las máquinas virtuales de Azure. Cuando se conmuta por error desde una región de Azure a otra, los clientes a menudo necesitan conservar sus configuraciones de IP. Por ello, Site Recovery imita la red virtual de origen y la estructura de la subred de forma predeterminada cuando se crean estos recursos en la región de destino. En cuanto a las máquinas virtuales de Azure configuradas mediante direcciones IP privadas y estáticas, Site Recovery dedica el máximo esfuerzo en aprovisionar la misma IP privada en la máquina virtual de destino, siempre y cuando un recurso de Azure o una máquina virtual replicada no haya bloqueado esa IP.

En lo referente a aplicaciones sencillas, la configuración predeterminada que se detalla más arriba es todo lo que necesita. Si lo que tiene son aplicaciones de empresa más complejas, es posible que los clientes necesiten recursos de red adicionales para garantizar la conectividad posterior a la conmutación por error con otros componentes de su infraestructura. En este artículo se explican los requisitos de red para realizar la conmutación por error de máquinas virtuales de Azure desde una región a otra mientras se conservan las direcciones IP de la máquina virtual.

## <a name="azure-to-azure-connectivity"></a>Conectividad de Azure a Azure

En el primer escenario vemos que la **empresa A** tiene toda la infraestructura de las aplicaciones en Azure. Para garantizar la continuidad del negocio y el cumplimiento de las normas, la **empresa A** decide usar Azure Site Recovery para proteger sus aplicaciones.

Si tenemos en cuenta el requisito de retención de IP (como, por ejemplo, en el caso de los enlaces de aplicaciones), la empresa A tiene la misma red virtual y la estructura de subred en la región de destino. Para reducir aún más el objetivo de tiempo de recuperación (RTO), **la empresa A** utiliza nodos de réplica de SQL Always ON, controladores de dominio, etc. y coloca estos nodos en una red virtual diferente en la región de destino. Al usar un espacio de direcciones diferente para los nodos de réplica, la **empresa A** puede establecer una conexión VPN de sitio a sitio entre las regiones de origen y destino, lo que no sería posible si se usara el mismo espacio de direcciones en ambos extremos.

Este es el aspecto de la arquitectura de red antes de la conmutación por error:
- Las máquinas virtuales de la aplicación se hospedan en la región de Asia Oriental de Azure mediante una red virtual de Azure que consta de un espacio de direcciones 10.1.0.0/16. Esta red virtual se denomina **Source VNet**.
- Las cargas de trabajo de la aplicación se dividen en tres subredes: 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, y se denominan respectivamente **subred 1**, **subred 2** y **subred 3**.
- El Sudeste Asiático de Azure es la región de destino y tiene una red virtual de recuperación que se asemeja a la configuración del espacio de direcciones y de la subred del origen. Esta red virtual se denomina **Recovery VNet**.
- Los nodos de réplica necesarios para Always On, el controlador de dominio, etc., se colocan en una red virtual con el espacio de direcciones 20.1.0.0/16 en la subred 4 que tiene la dirección 20.1.0.0/24. La red virtual se denomina **Azure VNet** y está en el Sudeste Asiático de Azure.
- Las redes **Source VNet** y la **Azure VNet** están conectadas a través de la conexión VPN de sitio a sitio.
- La red **Recovery VNet** no está conectada a ninguna otra red virtual.
- La **empresa A** asigna o comprueba la dirección IP de destino de los elementos replicados. En este ejemplo, la IP de destino es la misma que la IP de origen para cada máquina virtual.

![Conectividad de Azure a Azure antes de la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Conmutación por error de región completa

Si se produce una interrupción a nivel regional, **la empresa A** puede recuperar toda la implementación de forma rápida y sencilla gracias a los potentes [planes de recuperación](site-recovery-create-recovery-plans.md) de Azure Site Recovery. Una vez establecida la dirección IP de destino de cada máquina virtual antes de la conmutación por error, **la empresa A** puede realizar la conmutación por error y automatizar el establecimiento de la conexión entre las redes Recovery VNet y Azure VNet, tal como se muestra en el diagrama siguiente.

![Conexión de Azure a Azure antes de la conmutación por error de región completa](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

En función de los requisitos de la aplicación, las conexiones entre las dos redes virtuales en la región de destino se pueden establecer antes, durante (como un paso intermedio) o después de la conmutación por error. Use los [planes de recuperación](site-recovery-create-recovery-plans.md) para agregar scripts y definir el orden de la conmutación por error.

Asimismo, la empresa A también tiene la opción de usar el emparejamiento de VNET o la conexión VPN de sitio a sitio para poder conectar las redes Recovery VNet y Azure VNet. El emparejamiento de VNET no utiliza una puerta de enlace de VPN y tiene distintas restricciones. Además, [los precios del emparejamiento de VNET](https://azure.microsoft.com/pricing/details/virtual-network) se calculan de forma diferente que los [precios de VPN Gateway entre redes virtuales](https://azure.microsoft.com/pricing/details/vpn-gateway). En cuanto a las conmutaciones por error, es aconsejable imitar la conectividad del origen, incluyendo el tipo de conexión, para así poder minimizar incidentes impredecibles que puedan surgir de los cambios en la red.

### <a name="isolated-application-failover"></a>Conmutación por error de aplicaciones aisladas

En ciertas circunstancias, es posible que los usuarios necesiten realizar una conmutación por error de las partes de la infraestructura de aplicaciones. Como ejemplo, se puede realizar una conmutación por error de una aplicación o nivel que se encuentre en una subred dedicada. Aunque es posible realizar una conmutación por error en una subred con retención de IP, no es aconsejable en la mayoría de los casos, ya que aumenta considerablemente las incoherencias de conectividad. Asimismo, también perderá la conectividad de subred en otras subredes dentro de la misma red virtual de Azure.

La mejor manera de sacarle partido a los requisitos de la conmutación por error de una aplicación a nivel de subred, es usar las diferentes direcciones IP de destino para realizar la conmutación por error (siempre que la conectividad sea necesaria en otras subredes de la red virtual de origen) o aislar cada aplicación en su propia red virtual de origen. Gracias al último enfoque, puede establecer una conectividad entre redes en el origen y emularla al realizar la conmutación por error en la región de destino.

Si quiere diseñar aplicaciones individuales para lograr resistencia, le recomendamos que aloje una aplicación en su propia red virtual dedicada y que establezca la conectividad entre esas redes virtuales según sea necesario. Esto le permitirá realizar una conmutación por error de aplicaciones aisladas a la vez que conserva las direcciones IP privadas originales.

La configuración previa a la conmutación por error presenta el siguiente aspecto:
- Las máquinas virtuales de la aplicación se hospedan en la región de Asia Oriental de Azure y usan una red virtual de Azure que consta de un espacio de direcciones 10.1.0.0/16 para la primera aplicación y un espacio de direcciones 15.1.0.0/16 para la segunda aplicación. Las redes virtuales de la primera y segunda aplicación se denominan **Source VNet1** y **Source VNet2** respectivamente.
- Cada red virtual se divide en otras dos subredes.
- El Sudeste Asiático de Azure es la región de destino y dispone de las redes virtuales de recuperación denominadas Recovery VNet1 y Recovery VNet2.
- Los nodos de réplica necesarios para Always On, el controlador de dominio, etc., se colocan en una red virtual con el espacio de direcciones 20.1.0.0/16 en la **subred 4** que tiene la dirección 20.1.0.0/24. La red virtual se denomina Azure VNet y está en el Sudeste Asiático de Azure.
- Las redes **Source VNet1** y **Azure VNet** están conectadas a través de la conexión VPN de sitio a sitio. Igualmente, las redes **Source VNet2** y **Azure VNet** están conectadas a través de la conexión VPN de sitio a sitio.
- Las redes **Source VNet1** y **Source VNet2** también están conectadas a través de una conexión de VPN de sitio a sitio en este ejemplo. Dado que las dos redes virtuales están en la misma región, se puede usar el emparejamiento de VNET en vez de una conexión VPN de sitio a sitio.
- Las redes **Recovery VNet1** y **Recovery VNet2** no están conectadas a ninguna otra red virtual.
- Para reducir el objetivo de tiempo de recuperación (RTO), las puertas de enlace de VPN se configuran en **Recovery VNet1** y **Recovery VNet2** antes de la conmutación por error.

![Aplicación aislada en la conectividad de Azure a Azure antes de realizar la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

Si se produce una situación de desastre que afecte solo a una aplicación (que en este ejemplo se aloja en la red Source VNet2), la empresa A puede recuperar la aplicación afectada tal como se indica a continuación:
- Las conexiones VPN entre **Source VNet1** y **Source VNet2** y entre **Source VNet2** y **Azure VNet** están desconectadas.
- Se establecen conexiones VPN entre **Source VNet1** y **Recovery VNet2** y entre **Recovery VNet2** y **Azure VNet**.
- Las máquinas virtuales de **Source VNet2** se conmutan por error en **Recovery VNet2**.

![Aplicación aislada en la conectividad de Azure a Azure después de realizar la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

El ejemplo anterior donde se realiza una conmutación por error aislada se puede ampliar para que incluya más aplicaciones y conexiones de red. Se recomienda seguir un modelo de conexión similar siempre que sea posible, cuando se conmuta por error desde un origen a un destino.

### <a name="further-considerations"></a>Consideraciones a tener en cuenta

Las puertas de enlace VPN pueden usar direcciones IP públicas y saltos de puerta de enlace para establecer conexiones. Si no desea usar una IP pública o si quiere evitar saltos adicionales, puede usar Global VNet Peering para emparejar redes virtuales en diferentes regiones de Azure.

Esta característica está actualmente en versión preliminar pública y se ampliará para admitir más regiones; asimismo, se habilitará la conectividad directa de máquina virtual a máquina virtual sin ninguna intervención pública de Internet y sin saltos adicionales.

Para obtener más información, consulte la [documentación de emparejamiento](../virtual-network/tutorial-connect-virtual-networks-portal.md#register) y los [precios](https://azure.microsoft.com/pricing/details/virtual-network/).

## <a name="on-premises-to-azure-connectivity"></a>Conectividad local a Azure

En el segundo escenario tendremos en cuenta la **empresa B**, en la cual una parte de la infraestructura de aplicaciones se ejecuta en Azure y el resto se ejecuta de forma local. Para garantizar la continuidad del negocio y el cumplimiento de las normas, la **empresa B** decide usar Azure Site Recovery para proteger las aplicaciones que se ejecutan en Azure.

Este es el aspecto de la arquitectura de red antes de la conmutación por error:
- Las máquinas virtuales de la aplicación se hospedan en la región de Asia Oriental de Azure mediante una red virtual de Azure que consta de un espacio de direcciones 10.1.0.0/16. Esta red virtual se denomina **Source VNet**.
- Las cargas de trabajo de la aplicación se dividen en tres subredes: 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, y se denominan respectivamente **subred 1**, **subred 2** y **subred 3**.
- El Sudeste Asiático de Azure es la región de destino y tiene una red virtual de recuperación que se asemeja a la configuración del espacio de direcciones y de la subred del origen. Esta red virtual se denomina **Recovery VNet**.
- Las máquinas virtuales de la región de Asia Oriental de Azure están conectadas al centro de datos local mediante ExpressRoute o una conexión VPN de sitio a sitio.
- Para reducir el objetivo de tiempo de recuperación (RTO), la empresa B debe aprovisionar las puertas de enlace en la red Recovery VNet en la región del Sudeste Asiático de Azure antes de realizar la conmutación por error.
- La **empresa B** asigna o comprueba la dirección IP de destino de los elementos replicados. En este ejemplo, la IP de destino es la misma que la IP de origen para cada máquina virtual.

![Conectividad local a Azure antes de realizar la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Conmutación por error de región completa

Si se produce una interrupción a nivel regional, **la empresa B** puede recuperar toda la implementación de forma rápida y sencilla gracias a los potentes [planes de recuperación](site-recovery-create-recovery-plans.md) de Azure Site Recovery. Una vez establecida la dirección IP de destino de cada máquina virtual antes de la conmutación por error, **la empresa B** puede realizar la conmutación por error y automatizar el establecimiento de la conexión entre las redes Recovery VNet y el centro de datos local, tal como se muestra en el diagrama siguiente.

Recuerde que debe desconectar la conexión original entre la región de Asia Oriental de Azure y el centro de datos local antes de establecer la conexión entre la región del Sudeste Asiático de Azure y el centro de datos local. Asimismo, también se vuelve a configurar el enrutamiento local para que apunte a la región de destino y a las puertas de enlace una vez realizada la conmutación por error.

![Conectividad local a Azure después de realizar la conmutación por error](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>Conmutación por error de subred

A diferencia del escenario de Azure a Azure que se describe para la **empresa A**, no es posible realizar una conmutación por error de nivel de subred en la **empresa B**. Esto es porque el espacio de direcciones en redes virtuales de origen y de recuperación es el mismo, y el origen de la conexión local está activo.

Para conseguir la resistencia de las aplicaciones, es recomendable alojar cada aplicación en su propia red virtual dedicada de Azure. A continuación, se pueden conmutar por error las aplicaciones de forma aislada y las conexiones locales a origen necesarias se pueden enrutar hacia la región de destino, tal como se describió anteriormente.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre los [planes de recuperación](site-recovery-create-recovery-plans.md).
