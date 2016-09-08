<properties
   pageTitle="Crear un FQDN para una máquina virtual en el Portal de Azure | Microsoft Azure"
   description="Aprenda a crear un nombre de dominio completo o FQDN para una máquina virtual basada en el administrador de recursos en el Portal de Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# Crear un nombre de dominio completo en el Portal de Azure
Cuando crea una máquina virtual en el [Portal de Azure](https://portal.azure.com) mediante el modelo de implementación de Resource Manager, se crea automáticamente un recurso de IP pública para la máquina virtual. Use esta dirección IP para acceder de forma remota a la máquina virtual. Aunque el portal no crea [un nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN, de forma predeterminada, puede agregar uno cuando se crea la máquina virtual. Este artículo muestra los pasos para crear un nombre DNS o FQDN.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Ahora puede conectarse de manera remota a la máquina virtual con este nombre DNS, por ejemplo, con `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## Pasos siguientes
Ahora que la máquina virtual tiene un nombre DNS y una IP pública, puede implementar marcos y servicios de aplicaciones comunes, como nginx, MongoDB, Docker, etc.

También puede leer más sobre el [uso de Resource Manager](../resource-group-overview.md) para obtener sugerencias sobre la creación de las implementaciones de Azure.

<!---HONumber=AcomDC_0824_2016-->