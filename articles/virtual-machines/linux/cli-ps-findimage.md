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
ms.date: 08/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79eb69b83e4ffc0a4ad7c2631ce4d1306a1e335c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Búsqueda de imágenes de maquina virtual Linux en Azure Marketplace con la CLI de Azure
En este tema se describe cómo usar la CLI de Azure 2.0 para buscar imágenes de VM en Azure Marketplace. Utilice esta información para especificar una imagen de Marketplace cuando cree una máquina virtual Linux.

Asegúrese de que ha instalado la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente y que ha iniciado sesión en una cuenta de Azure (`az login`).

## <a name="terminology"></a>Terminología

En la CLI y demás herramientas de Azure, las imágenes de Marketplace se identifican según una jerarquía:

* **Publicador**: organización que ha creado la imagen. Ejemplo: Canonical
* **Oferta**: grupo de imágenes relacionadas creado por un publicador. Ejemplo: Ubuntu Server
* **SKU**: instancia de una oferta, por ejemplo, una versión principal de una distribución. Ejemplo: 16.04-LTS
* **Versión**: número de versión de una SKU de imagen. Al especificar la imagen, se puede reemplazar el número de versión por "latest", que selecciona la versión más reciente de la distribución.

Para especificar una imagen de Marketplace, normalmente se usa el *URN* de la imagen. El URN combina estos valores separados por el carácter de dos puntos (:): *Publicador*:*Oferta*:*Sku*:*Versión*. 


## <a name="list-popular-images"></a>Enumeración de imágenes populares

Ejecute el comando [az vm image list](/cli/azure/vm/image#az_vm_image_list) sin la opción `--all` para ver una lista de imágenes de VM populares en Azure Marketplace. Por ejemplo, ejecute el siguiente comando para mostrar una lista de almacenamiento en caché de imágenes populares en el formato de tabla:

```azurecli
az vm image list --output table
```

La salida incluye el URN (el valor de la columna *Urn*), que se usa para especificar la imagen. Al crear una máquina virtual con una de estas imágenes populares de Marketplace, puede especificar el alias del URN, como *UbuntuLTS*.

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
...
```

## <a name="find-specific-images"></a>Búsqueda de imágenes específicas

Para buscar una imagen de máquina virtual determinada en Marketplace, use el comando `az vm image list` con la opción `--all`. Esta versión del comando tarda algún tiempo en completarse y puede devolver una salida larga, por lo que normalmente la lista se filtra por `--publisher` u otro parámetro. 

Por ejemplo, el siguiente comando muestra todas las ofertas de Debian (recuerde que sin el conmutador `--all`, solo busca en la caché local de las imágenes comunes):

```azurecli
az vm image list --offer Debian --all --output table 

```

Salida parcial: 
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
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Aplique filtros similares con las opciones `--location`, `--publisher` y `--sku`. Incluso puede realizar coincidencias parciales en un filtro, como la búsqueda de `--offer Deb` de todas las imágenes de Debian.

Si no especifica una ubicación determinada con la opción `--location` los valores de `westus` se devuelven de forma predeterminada. (Establezca una ubicación predeterminada distinta ejecutando `az configure --defaults location=<location>`).

Por ejemplo, el siguiente comando muestra todas las SKU de Debian 8 en `westeurope`:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Salida parcial:

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
Otra forma de buscar una imagen en una ubicación es ejecutar los comandos [az vm image list-publishers](/cli/azure/vm/image#az_vm_image_list_publishers), [az vm image list-offers](/cli/azure/vm/image#az_vm_image_list_offers) y [az vm image list-skus](/cli/azure/vm/image#az_vm_image_list_skus) en orden. Con estos comandos, determine estos valores:

1. Listado de los publicadores de imágenes.
2. Para un publicador determinado, enumeración de sus ofertas.
3. Para una oferta determinada, enumeración de sus SKU.


Por ejemplo, el siguiente comando muestra los publicadores de imagen en la ubicación del oeste de EE. UU.:

```azurecli
az vm image list-publishers --location westus --output table
```

Salida parcial:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
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
Utilice esta información para buscar ofertas desde un publicador específico. Por ejemplo, si Canonical es un publicador de imágenes de la ubicación Oeste de EE. UU., busque sus ofertas al ejecutar `azure vm image list-offers`. Pase la ubicación y el publicador como en el ejemplo siguiente:

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

Por último, use el comando `az vm image list` para buscar una versión determinada de la SKU que quiera, por ejemplo, **16.04-LTS**:

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
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```
## <a name="next-steps"></a>pasos siguientes
Ahora puede elegir de forma precisa la imagen que desee usar tomando nota del valor URN. Pase este valor con el parámetro `--image` cuando cree una máquina virtual con el comando [az vm create](/cli/azure/vm#az_vm_create). Recuerde que puede reemplazar de forma opcional el número de versión del URN por "latest". Esta versión es siempre la versión más actualizada de la distribución. Para crear una máquina virtual rápidamente con la información del URN, vea [Creación y administración de máquinas virtuales Linux con la CLI de Azure](tutorial-manage-vm.md).
