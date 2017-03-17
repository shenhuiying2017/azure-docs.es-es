---
title: Uso del filtro de paquetes de FreeBSD para crear un firewall en Azure | Microsoft Docs
description: "Obtenga información sobre cómo implementar un firewall NAT con el filtro de paquetes de FreeBSD de Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 0f7c9dcf3cce67352d38db55ddbbcf06221f26d3
ms.openlocfilehash: 64b40683a3400cdf7dfa5b76c5a8827ff822b6a4
ms.lasthandoff: 03/02/2017


---

# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Procedimiento para usar el filtro de paquetes de FreeBSD con el fin de crear un firewall seguro en Azure
En este artículo se explica cómo implementar un firewall NAT con el filtro de paquetes de FreeBSD a través de la plantilla de Azure Resource Manager para el escenario común de servidor web.

## <a name="what-is-pf"></a>¿Qué es el filtro de paquetes (PF)?
PF es un filtro de paquetes con estado con licencia para BSD, una pieza central del software de cortafuegos. Esta herramienta se ha desarrollado rápidamente y ahora tiene varias ventajas con respecto a otros firewalls disponibles. Traducción de direcciones de red (NAT) se encuentra en PF desde el primer día. El programador de paquetes y la administración de colas activas se han integrado en PF, incorporando ALTQ, con lo que se puede configurar desde PF. Características como pfsync y CARP para conmutación por error y redundancia, authpf para la autenticación de sesiones y ftp-proxy para facilitar el uso de firewalls en el difícil protocolo FTP difícil, también se han incorporado a PF. En resumen, PF es un firewall eficaz y con numerosas características. 

## <a name="get-started"></a>Primeros pasos
Si le interesa configurar un firewall seguro en la nube para sus servidores web, empecemos a hacerlo. También puede aplicar los scripts usados en esta plantilla de Azure Resource Manager para configurar la topología de red.
La plantilla de Azure Resource Manager configura una máquina virtual de FreeBSD que realiza tareas NAT y de redirección usando dos máquinas virtuales de FreeBSD y PF con el servidor web Nginx instalado y configurado. Además de llevar a cabo NAT para el tráfico de salida de los dos servidores web, la máquina virtual de redirección/NAT intercepta las solicitudes HTTP y las redirige a los servidores web con distribución "round-robin". La red virtual usa el espacio de direcciones IP no enrutables 10.0.0.2/24 y puede modificar los parámetros de la plantilla. La plantilla de Azure Resource Manager también define una tabla de rutas para la red virtual completa, que es una colección de rutas individuales que se usa para invalidar las rutas de Azure predeterminadas en función de la dirección IP de destino. 

![pf_topology](./media/virtual-machines-linux-freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implementación a través de la CLI de Azure
Necesita tener instalada la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e iniciar sesión en una cuenta de Azure con [az login](/cli/azure/#login). Cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado "`myResourceGroup`" en la ubicación `West US`.

```azurecli
az group create --name myResourceGroup --location westus
```

Después, implemente la plantilla [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) con [az group deployment create](/cli/azure/group/deployment#create). Descargue [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) en la misma ruta de acceso y definir sus propios valores de recursos, como `adminPassword`, `networkPrefix` y `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Después de aproximadamente cinco minutos, obtendrá la información de `"provisioningState": "Succeeded"`. Después, puede acceder mediante SSH a la máquina virtual de front-end (NAT) o al servidor de web Nginx en un explorador usando la dirección IP pública o el FQDN de la máquina virtual de front-end (NAT). En el ejemplo siguiente se muestra el FQDN y la dirección IP pública que se asigna a la máquina virtual de front-end (NAT) en el grupo de recursos `myResourceGroup`. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Pasos siguientes
¿Desea configurar su propia NAT en Azure? El código abierto es gratis, pero ¿es eficaz? PF es una buena elección. Mediante la plantilla [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), solo tiene cinco minutos para configurar un firewall NAT con PF de FreeBSD de carga equilibrada con distribución "round-robin" de Azure para el escenario común de servidores web. 

Si desea obtener información sobre la oferta de FreeBSD en Azure, consulte [Introducción a FreeBSD en Azure](./virtual-machines-freebsd-intro-on-azure.md).

Para saber más sobre PF, consulte el [manual de FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) o el [manual del usuario PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).

