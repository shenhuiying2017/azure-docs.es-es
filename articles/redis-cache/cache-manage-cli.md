---
title: "Administración de Azure Redis Cache mediante la CLI de Azure | Microsoft Docs"
description: "En este tema se describe cómo instalar la CLI de Azure en cualquier plataforma, cómo usarla para conectarse a la cuenta de Azure y cómo crear y administra una caché en Redis desde la CLI de Azure."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: ba078a870a3998568170cc197bd6698b97b7fadb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Creación y administración de Caché en Redis de Azure mediante la interfaz de línea de comandos de Azure (CLI de Azure)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [CLI de Azure](cache-manage-cli.md)
>
>

La CLI de Azure es una excelente manera de administrar la infraestructura de Azure desde cualquier plataforma. En este artículo se muestra cómo crear y administrar las instancias de Caché en Redis de Azure usando la CLI de Azure.

> [!NOTE]
> Este artículo se aplica a una versión anterior de la CLI de Azure. Para conocer los scripts de ejemplo más recientes de la CLI 2.0 de Azure, consulte [Ejemplos de caché de Redis para la CLI de Azure](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Para crear y administrar instancias de Caché en Redis de Azure mediante la CLI de Azure, debe realizar los pasos siguientes.

* Debe tener una cuenta de Azure. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos momentos.
* [Instalación de la CLI de Azure](../cli-install-nodejs.md).
* Conecte su instalación de CLI de Azure con una cuenta personal de Azure o con una cuenta de Azure profesional o educativa, e inicie sesión desde la CLI de Azure mediante el comando `azure login` . Para comprender las diferencias y elegir, consulte [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md).
* Antes de ejecutar cualquiera de los comandos siguientes, cambie la CLI de Azure al modo de Administrador de recursos mediante la ejecución del comando `azure config mode arm` . Para más información, vea [Use the Azure CLI to manage Azure resources and resource groups (Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure)](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Propiedades de caché en Redis
Las siguientes propiedades se utilizan al crear y actualizar instancias de caché en Redis.

| Propiedad | Switch | Description |
| --- | --- | --- |
| name |-n,--name |Nombre de la caché en Redis. |
| resource group |-g, --resource-group |Nombre del grupo de recursos. |
| location |-l, --location |Ubicación donde crear la caché. |
| size |-z, --size |Tamaño de la caché en Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |SKU de Redis. Debe ser uno de estos valores: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Propiedad EnableNonSslPort de la caché en Redis. Agregue esta marca si desea habilitar el puerto que no es SSL de la caché |
| Configuración de Redis |-c, --redis-configuration |Configuración de Redis. Escriba una cadena con formato JSON de valores y claves de configuración aquí. Formato:"{"":"","":""}" |
| Configuración de Redis |-f, --redis-configuration-file |Configuración de Redis. Escriba la ruta de acceso de un archivo que contiene valores y claves de configuración aquí. Formato de la entrada del archivo: {"":"","":""} |
| Número de particiones |-r, --shard-count |Número de particiones que se creará en una caché de clúster premium con la agrupación de clústeres. |
| Red virtual |-v, --virtual-network |Si hospeda la memoria caché en una red virtual, especifica el id. de recurso de ARM exacto de la red virtual en la que se va a implementar la Caché en Redis. Formato de ejemplo: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| key type |-t, --key-type |Tipo de la clave que renovar. Valores válidos: [Primary, Secondary] |
| StaticIP |-p, --static-ip <static-ip> |Si hospeda la memoria caché en una red virtual, especifica una dirección IP única en la subred de la memoria caché. Si no se ofrece, elija una para usted en la subred. |
| Subred |t, --subnet <subnet> |Si hospeda la memoria caché en una red virtual, especifica el nombre de la subred en la que se va a implementar la memoria caché. |
| VirtualNetwork |-v, --virtual-network <virtual-network> |Si hospeda la memoria caché en una red virtual, especifica el id. de recurso de ARM exacto de la red virtual en la que se va a implementar la Caché en Redis. Formato de ejemplo: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscription |-s, --subscription |Identificador de la suscripción. |

## <a name="see-all-redis-cache-commands"></a>Consulta de todos los comandos de caché en Redis
Para ver todos los comandos de Caché en Redis y sus parámetros, use el comando `azure rediscache -h` .

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Creación de una caché en Redis
Para crear una caché en Redis, use el comando siguiente:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para más información sobre este comando, ejecute el comando `azure rediscache create -h` .

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Eliminación de una caché en Redis existente
Para eliminar una caché en Redis, use el comando siguiente:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para más información sobre este comando, ejecute el comando `azure rediscache delete -h` .

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Lista de todas las memorias Redis Cache dentro de su suscripción o del grupo de recursos
Para enumerar todas las memorias Redis Cache incluidas en su suscripción o en el grupo de recursos, use el comando siguiente:

    azure rediscache list [options]

Para más información sobre este comando, ejecute el comando `azure rediscache list -h` .

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Presentación de las propiedades de una caché en Redis existente
Para mostrar las propiedades de una caché en Redis existente, use el comando siguiente:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para más información sobre este comando, ejecute el comando `azure rediscache show -h` .

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Cambio de la configuración de una caché en Redis existente
Para cambiar la configuración de una caché en Redis existente, use el comando siguiente:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para más información sobre este comando, ejecute el comando `azure rediscache set -h` .

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renovación de la clave de autenticación para una caché en Redis existente
Para renovar la clave de autenticación para una caché en Redis existente, use el comando siguiente:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especifique `Primary` o `Secondary` para `key-type`.

Para más información sobre este comando, ejecute el comando `azure rediscache renew-key -h`.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Lista de las claves principal y secundaria de una caché en Redis existente
Para enumerar las claves Principal y Secundaria de una caché en Redis existente, use el comando siguiente:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para más información sobre este comando, ejecute el comando `azure rediscache list-keys -h` .

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
