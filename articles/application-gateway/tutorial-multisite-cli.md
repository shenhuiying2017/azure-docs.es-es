---
title: "Creación de una puerta de enlace de aplicaciones con hospedaje de varios sitios mediante la CLI de Azure | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones que hospede varios sitios mediante la CLI de Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: df475cb6eed2b75275e573721f754e7de87698f5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Creación de una puerta de enlace de aplicaciones con hospedaje de varios sitios mediante la CLI de Azure

Puede usar la CLI de Azure para configurar el [hospedaje de varios sitios web](application-gateway-multi-site-overview.md) cuando se crea una [puerta de enlace de aplicaciones](application-gateway-introduction.md). En este tutorial, creará grupos de back-end mediante conjuntos de escalado de máquinas virtuales. A continuación, configurará reglas y agentes de escucha basados en dominios de su propiedad para asegurarse de que el tráfico web llega a los servidores adecuados de los grupos. En este tutorial, se da por supuesto que posee varios dominios y usa los ejemplos *www.contoso.com* y *www.fabrikam.com*.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Configuración de la red
> * Creación de una puerta de enlace de aplicaciones
> * Creación de agentes de escucha y reglas de enrutamiento
> * Crear conjuntos de escalado de máquinas virtuales con los grupos de back-end
> * Creación de un registro CNAME en el dominio

![Ejemplo de enrutamiento de varios sitios](./media/tutorial-multisite-cli/scenario.png)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Para crear un grupo de recursos, use [az group create](/cli/azure/group#create).

En el ejemplo siguiente, se crea un grupo de recursos llamado *myResourceGroupAG* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Crear recursos de red 

Cree la red virtual llamada *myVNet* y la subred llamada *myAGSubnet* mediante [az network vnet create](/cli/azure/network/vnet#az_net). Luego, puede agregar la subred llamada *myBackendSubnet* que necesitan los servidores back-end mediante [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Cree la dirección IP pública llamada *myAGPublicIPAddress* mediante [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Puede usar [az network application-gateway create](/cli/azure/application-gateway#create) para crear la puerta de enlace de aplicaciones llamada *myAppGateway*. Cuando se crea una puerta de enlace de aplicaciones mediante la CLI de Azure, se especifica información de configuración, como capacidad, SKU y HTTP. La puerta de enlace de aplicaciones se asigna a los elementos *myAGSubnet* y *myAGPublicIPAddress* creados anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

La puerta de enlace de aplicaciones puede tardar varios minutos en crearse. Después de crear la puerta de enlace de aplicaciones, puede ver estas nuevas características de ella:

- *appGatewayBackendPool*: una puerta de enlace de aplicaciones debe tener al menos un grupo de direcciones de servidores back-end.
- *appGatewayBackendHttpSettings*: especifica que se use el puerto 80 y un protocolo HTTP para la comunicación.
- *appGatewayHttpListener*: agente de escucha predeterminado asociado con *appGatewayBackendPool*.
- *appGatewayFrontendIP*: asigna *myAGPublicIPAddress* a *appGatewayHttpListener*.
- *rule1*: la regla de enrutamiento predeterminada asociada a *appGatewayHttpListener*.

### <a name="add-the-backend-pools"></a>Adición de grupos de back-end

Agregue los grupos de back-end llamados *contosoPool* y *fabrikamPool* que son necesarios para contener los servidores backend mediante [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Adición de agentes de escucha

Es necesario un agente de escucha para que la puerta de enlace de aplicaciones enrute el tráfico de forma adecuada al grupo de servidores back-end. En este tutorial, creará dos agentes de escucha para los dos dominios. En este ejemplo, se crean los agentes de escucha para los dominios de *www.contoso.com* y *www.fabrikam.com*. 

Agregue los agentes de escucha llamados *contosoListener* y *fabrikamListener* que son necesarios para enrutar el tráfico con [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Adición de reglas de enrutamiento

Las reglas se procesan en el orden en el que se crean y el tráfico se dirige mediante la primera regla que coincida con la dirección URL que se envía a la puerta de enlace de aplicaciones. Por ejemplo, si tiene una regla que usa un agente de escucha básico y una regla que usa un agente de escucha multisitio en el mismo puerto, la regla con el agente de escucha multisitio debe aparecer antes que la regla con el agente de escucha básico para que la regla multisitio funcione según lo previsto. 

En este ejemplo, va a crear dos reglas nuevas y a eliminar la regla predeterminada que se creó junto con la puerta de enlace de aplicaciones. Puede agregar la regla mediante [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Creación de conjuntos de escalado de máquinas virtuales

En este ejemplo, creará tres conjuntos de escalado de máquinas virtuales que admiten los tres grupos de back-end en la puerta de enlace de aplicaciones. Los conjuntos de escalado que crea se llaman *myvmss1*, *myvmss2* y *myvmss3*. Cada conjunto de escalado contiene dos instancias de máquina virtual en las que se instalará NGINX.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalación de NGINX

```azurecli-interactive
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Creación de un registro CNAME en el dominio

Después de crear la puerta de enlace de aplicaciones con su dirección IP pública, puede obtener la dirección DNS y usarla para crear un registro CNAME en el dominio. Para obtener la dirección DNS de la puerta de enlace de aplicaciones, puede usar [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copie el valor de *fqdn* de DNSSettings y úselo como valor del registro CNAME que creó. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

No se recomienda el uso de registros A, ya que la IP virtual puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

Escriba el nombre de dominio en la barra de direcciones del explorador. Por ejemplo, http://www.contoso.com.

![Prueba del sitio de contoso en la puerta de enlace de aplicaciones](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Cambie la dirección al otro dominio. Debería ver algo parecido al ejemplo siguiente:

![Prueba del sitio de fabrikam en la puerta de enlace de aplicaciones](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración de la red
> * Creación de una puerta de enlace de aplicaciones
> * Creación de agentes de escucha y reglas de enrutamiento
> * Crear conjuntos de escalado de máquinas virtuales con los grupos de back-end
> * Creación de un registro CNAME en el dominio

> [!div class="nextstepaction"]
> [Obtenga más información sobre lo que puede hacer con la puerta de enlace de aplicaciones](application-gateway-introduction.md).