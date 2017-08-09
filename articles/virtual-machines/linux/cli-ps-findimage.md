---
title: "Selección de imágenes de máquina virtual Linux con la CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar la CLI de Azure para determinar el publicador, la oferta, la SKU y la versión para imágenes de VM de Marketplace."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/11/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72c5c2efe2c8a60f13d18b595062448e6a0d1816
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Búsqueda de imágenes de maquina virtual Linux en Azure Marketplace con la CLI de Azure
En este tema se describe cómo usar la CLI de Azure 2.0 para buscar imágenes de VM en Azure Marketplace. Utilice esta información para especificar una imagen de Marketplace cuando cree una máquina virtual Linux.

Asegúrese de que ha instalado la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente y que ha iniciado sesión en una cuenta de Azure (`az login`).

## <a name="list-popular-images"></a>Enumeración de imágenes populares

Ejecute el comando [az vm image list](/cli/azure/vm/image#list) sin la opción `--all` para ver una lista de imágenes de VM populares en Azure Marketplace. Por ejemplo, ejecute el siguiente comando para mostrar una lista de almacenamiento en caché de imágenes populares en el formato de tabla:

```azurecli
az vm image list --output table
```

El resultado incluye el URN (el valor en la columna *Urn*), que tiene la forma de *Publicador*:*Oferta*:*SKU*:*Versión*. Use este valor para especificar una imagen al crear una VM con `az vm create`. Al crear una VM con una de las imágenes de VM populares, puede especificar el alias URN como *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="list-all-current-images"></a>Enumeración de todas las imágenes actuales

Para obtener la lista actual de todas las imágenes de VM en Marketplace, use el comando `az vm image list` con la opción `--all`. Esta versión del comando tarda tiempo en completarse:


```azurecli
az vm image list --all
```

Si no especifica una ubicación determinada con la opción `--location` los valores de `westus` se devuelven de forma predeterminada. (Establezca una ubicación predeterminada distinta ejecutando `az configure --defaults location=<location>`).


Si desea investigar de forma interactiva, dirija el resultado a un archivo local. Por ejemplo:

```azurecli
az vm image list --all > allImages.json
```




## <a name="find-specific-images"></a>Búsqueda de imágenes específicas

Use `az vm image list` con opciones adicionales para restringir la búsqueda a una ubicación, oferta, publicador o SKU específicos. Por ejemplo, el siguiente comando muestra todas las ofertas de Debian (recuerde que sin el conmutador `--all`, solo busca en la caché local de las imágenes comunes):

```azurecli
az vm image list --offer Debian --all --output table 
```

El resultado puede ser una lista larga, por lo que se trunca a continuación: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
...
```


Aplique filtros similares con las opciones `--location`, `--publisher` y `--sku`. Incluso puede realizar coincidencias parciales en un filtro, como la búsqueda de `--offer Deb` de todas las imágenes de Debian.

Por ejemplo, el siguiente comando muestra todas las SKU de Debian 8 en `westeurope`:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Salida:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```


## <a name="navigate-the-images"></a>Navegación por las imágenes 
Otra forma de buscar una imagen en una ubicación es ejecutar los comandos [az vm image list-publishers](/cli/azure/vm/image#list-publishers), [az vm image list-offers](/cli/azure/vm/image#list-offers) y [az vm image list-skus](/cli/azure/vm/image#list-skus) en orden. Con estos comandos, determine estos valores:

1. Listado de los publicadores de imágenes.
2. Para un publicador determinado, enumeración de sus ofertas.
3. Para una oferta determinada, enumeración de sus SKU.


Por ejemplo, el siguiente comando muestra los publicadores de imagen en la ubicación del oeste de EE. UU.:

```azurecli
az vm image list-publishers --location westus --output table
```

Salida:

```
Location    Name
----------  ----------------------------------------------------
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Estas listas pueden ser largas, por lo que este resultado de ejemplo es solo un fragmento de código. Utilice esta información para buscar ofertas desde un publicador específico. Por ejemplo, si Canonical es un publicador de imágenes en la ubicación del oeste de EE. UU., busque las ofertas ejecutando `azure vm image list-offers`. Pase la ubicación y el publicador como en el ejemplo siguiente:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Salida:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Verá que en la región del oeste de EE. UU, Canonical publica la oferta **UbuntuServer** sobre Azure. Pero, ¿con qué SKU? Para obtener esos valores, ejecute `azure vm image list-skus` y establezca la ubicación, el publicador y la oferta que haya encontrado:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Salida:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Por último, use el comando `az vm image list` para buscar una versión específica de la SKU que desee, por ejemplo, **14.04-LTS**:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Salida:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
```
## <a name="next-steps"></a>Pasos siguientes
Ahora puede elegir de forma precisa la imagen que desee usar tomando nota del valor URN. Al especificar la imagen, puede reemplazar de forma opcional el número de versión en el URN con "más actualizado". Esta versión es siempre la versión más actualizada de la distribución. Para crear una máquina virtual rápidamente con la información de URN que acaba de encontrar, vea [Creación de una máquina virtual Linux con la CLI de Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

