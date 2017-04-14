---
title: "Selección de imágenes de máquina virtuale Linux con la CLI de Azure | Microsoft Docs"
description: "Infórmese sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual Linux con el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1c4e8b5168d15c480d2aea41ddf7570d310e1252
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>Cómo encontrar imágenes de máquina virtual Linux con la CLI de Azure
En este tema se describe cómo buscar publicadores, ofertas, SKU y versiones de cada ubicación en la que puede implementar. 


## <a name="use-azure-cli-20"></a>Uso de la CLI de Azure 2.0

Después de [instalar la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), use el comando `az vm image list` para ver una lista en caché de las imágenes de máquina virtual más populares. Por ejemplo, el siguiente ejemplo del comando `az vm image list -o table` muestra:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Búsqueda de todas las imágenes actuales

Para obtener la lista actual de todas las imágenes, use el comando `az vm image list` con la opción `--all`. A diferencia de los comandos de la CLI de Azure 1.0, el comando `az vm image list --all` devuelve de forma predeterminada todas las imágenes de **westus** (a menos que especifique un determinado argumento `--location`), por lo que el comando `--all` tarda algún tiempo en completarse. Si desea investigar de forma interactiva, use `az vm image list --all > allImages.json`, que devuelve una lista de todas las imágenes disponibles actualmente en Azure y la almacena como un archivo para su uso local. 

Puede elegir una entre varias opciones para limitar la búsqueda a una ubicación, oferta, publicador o SKU específicos si ya tiene uno o varios en mente. Si no especifica una ubicación, se devuelven los valores para **westus**.

### <a name="find-specific-images"></a>Búsqueda de imágenes específicas

Use `az vm image list` con un filtro para buscar información específica. Por ejemplo, a continuación se muestran las **ofertas** que están disponibles para **Debian** (recuerde que sin el modificador `--all`, solo se busca en la caché local de imágenes comunes):

```azurecli
az vm image list --offer Debian -o table --all
```

El resultado es similar a este: 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

Puede ejecutar filtros parecidos en las opciones **--publisher** y **--sku**. Incluso puede ejecutar coincidencias parciales en un filtro, como buscar **--offerDeb** para encontrar todas las imágenes de Debian o **--publisher Micr** para encontrar todas las imágenes publicadas por Microsoft.

Si sabe cuál va a ser la ubicación de implementación, puede usar los resultados de búsqueda de imágenes generales junto con los comandos `az vm image list-skus`, `az vm image list-offers` y `az vm image list-publishers` para encontrar exactamente lo que quiere y dónde se puede implementar. Por ejemplo, si por el ejemplo anterior sabe que `credativ` tiene una oferta Debian, puede usar `--location` y otras opciones para buscar exactamente lo que quiere. En el ejemplo siguiente se busca una imagen de Debian 8 en **westeurope**:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

La salida es la siguiente:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>Uso de CLI de Azure 1.0 

> [!NOTE]
> En este artículo se describe cómo navegar por las imágenes de máquina virtual y seleccionarlas mediante una instalación de la CLI de Azure 1.0 o Azure PowerShell que admite el modelo de implementación de Azure Resource Manager. Como requisito previo, cambie al modo de Resource Manager. Con la CLI de Azure, especifique el modo escribiendo `azure config mode arm`. 
> 

La forma más rápida de buscar una imagen es llamar al comando `azure vm image list` y pasar la ubicación, el nombre del publicador (no distingue mayúsculas de minúsculas) y una oferta, si la conoce. Por ejemplo, la lista siguiente es solo un ejemplo breve (muchas listas son bastante largas) si sabe que "Canonical" es un publicador de la oferta "UbuntuServer".

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

La columna **Urn** será el formulario que se pase a `azure vm quick-create`.

A menudo, sin embargo, aún no se conoce lo que está disponible. En este caso, puede navegar por las imágenes mediante el comando `azure vm image list-publishers` y especificar una ubicación de centro de datos en el símbolo del sistema. Por ejemplo, a continuación se enumeran todos los publicadores de imágenes en la ubicación Oeste de EE. UU. (pase el argumento de ubicación en minúsculas y sin espacios a partir de las ubicaciones estándar).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Estas listas pueden ser bastante largas, por lo que la lista del ejemplo anterior es simplemente un fragmento. Supongamos que hemos observado que Canonical es, de hecho, un publicador de imágenes en la ubicación Oeste de EE. UU. Ahora puede encontrar sus ofertas mediante una llamada a `azure vm image list-offers` y pasar la ubicación y el publicador cuando se soliciten, como en el ejemplo siguiente:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Ahora ya sabemos que, en la región oeste de EE. UU., Canonical publica la oferta **UbuntuServer** en Azure. Pero, ¿con qué SKU? Para obtener esos valores, llame a `azure vm image list-skus` y responda a la solicitud con la ubicación, el publicador y la oferta que haya encontrado.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Con esta información, ahora puede buscar con precisión la imagen que desee mediante una llamada a la llamada original en la parte superior.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>Pasos siguientes
Ahora puede elegir con precisión la imagen que desea utilizar. Para crear una máquina virtual rápidamente con la información de URN que acaba de encontrar, o para usar una plantilla con esa información de URN, consulte [Creación de una máquina virtual Linux con la CLI de Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

