<properties 
	pageTitle="Creación y administración de Caché en Redis de Azure mediante la interfaz de línea de comandos de Azure (CLI de Azure)" 
	description="En este tema se describe cómo instalar la CLI de Azure en cualquier plataforma, cómo usarla para conectarse a la cuenta de Azure y cómo crear y administra una caché en Redis desde la CLI de Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="sdanie"/>

# Creación y administración de Caché en Redis de Azure mediante la interfaz de línea de comandos de Azure (CLI de Azure)

La CLI de Azure es una excelente manera de administrar la infraestructura de Azure desde cualquier plataforma. En este artículo se muestra cómo crear y administrar las instancias de Caché en Redis de Azure usando la CLI de Azure.

## Requisitos previos

Para crear y administrar instancias de Caché en Redis de Azure mediante la CLI de Azure, debe realizar los pasos siguientes.

-	Debe tener una cuenta de Azure. En caso de no tener ninguna, puede crear una [cuenta de evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/) en tan solo unos momentos.
-	[Instalación de la CLI de Azure](../xplat-cli.md#install).
-	Conectar su instalación de CLI de Azure con una cuenta personal de Azure o con una cuenta de Azure profesional o educativa, e inicie sesión desde la CLI de Azure mediante el comando `azure login`. Para comprender las diferencias y elegir, consulte [Conexión a su suscripción de Azure](../xplat-cli.md#configure).
-	Antes de ejecutar cualquiera de los comandos siguientes, cambie la CLI de Azure al modo de Administrador de recursos ejecutando el comando siguiente. `azure config mode arm` Para obtener más información, vea [Configuración del modo Administrador de recursos de Azure](../virtual-machines/xplat-cli-azure-resource-manager.md#setting-the-azure-resource-manager-mode).

## Propiedades de caché en Redis

Las siguientes propiedades se utilizan al crear y actualizar instancias de caché en Redis.

| Propiedad | Switch | Descripción |
|------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n,--name | Nombre de la caché en Redis. |
| resource group | -g, --resource-group | Nombre del grupo de recursos. |
| location | -l, --location | Ubicación donde crear la caché. |
| size | -z, --size | Tamaño de la caché en Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6] |
| sku | -x, --sku | SKU de Redis. Debe ser uno de estos valores: [Basic, Standard] |
| MaxMemoryPolicy | -m, --max-memory-policy | Propiedad MaxMemoryPolicy de la caché en Redis. Valores válidos: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL] |
| EnableNonSslPort | -e, --enable-non-ssl-port | Propiedad EnableNonSslPort de la caché en Redis. Agregue esta marca si desea habilitar el puerto que no es SSL de la caché |
| subscription | -s, --subscription | Identificador de la suscripción. |
| key type | -t, --key-type | Tipo de la clave que renovar. Valores válidos: [Primary, Secondary] |

## Consulta de todos los comandos de caché en Redis

Para ver todos los comandos de Caché en Redis y sus parámetros, use el comando `azure rediscache -h`.

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
	help:      rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
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

## Creación de una caché en Redis

Para crear una caché en Redis, use el comando siguiente:

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para obtener más información sobre este comando, ejecute el comando `azure rediscache create -h`.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -l, --location <location>                    Location to create cache.
	help:      -z, --size <size>                            Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6]
	help:      -x, --sku <sku>                              Redis SKU. Should be one of : [Basic, Standard]
	help:      -m, --max-memory-policy <max-memory-policy>  MaxMemoryPolicy property of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -e, --enable-non-ssl-port                    EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -s, --subscription <id>                      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Eliminación de una caché en Redis existente

Para eliminar una caché en Redis, use el comando siguiente:

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para obtener más información sobre este comando, ejecute el comando `azure rediscache delete -h`.

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

## Lista de todas las memorias caché en Redis dentro de su suscripción o del grupo de recursos

Para enumerar todas las memorias caché en Redis incluidas en su suscripción o en el grupo de recursos, use el comando siguiente:

	azure rediscache list [options]

Para obtener más información sobre este comando, ejecute el comando `azure rediscache list -h`.

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

## Presentación de las propiedades de una caché en Redis existente

Para mostrar las propiedades de una caché en Redis existente, use el comando siguiente:

	azure rediscache show [--name <name> --resource-group <resource-group>]

Para obtener más información sobre este comando, ejecute el comando `azure rediscache show -h`.

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

## Cambio de la configuración de una caché en Redis existente

Para cambiar la configuración de una caché en Redis existente, use el comando siguiente:

	azure rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]

Para obtener más información sobre este comando, ejecute el comando `azure rediscache set -h`.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -m, --max-memory-policy <max-memory-policy>  Max Memory Policy of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -s, --subscription <subscription>            the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Renovación de la clave de autenticación para una caché en Redis existente

Para renovar la clave de autenticación para una caché en Redis existente, use el comando siguiente:

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especifique `Primary` o `Secondary` para `key-type`.

Para obtener más información sobre este comando, ejecute el comando `azure rediscache renew-key -h`.

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
	help:      -t, --key-type <key-type>              type of key to renew
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Lista de las claves principal y secundaria de una caché en Redis existente

Para enumerar las claves Principal y Secundaria de una caché en Redis existente, use el comando siguiente:

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para obtener más información sobre este comando, ejecute el comando `azure rediscache list-keys -h`.

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

<!---HONumber=Sept15_HO3-->