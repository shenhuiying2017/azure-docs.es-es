---
title: "Automatización de DocumentDB con la CLI de Azure 2.0 | Microsoft Docs"
description: Use la CLI de Azure 2.0 para administrar cuentas de base de datos de DocumentDB. DocumentDB es una base de datos NoSQL basada en la nube para datos JSON.
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/04/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 150e8f3e186683bce735d0952adb57544505d1e9
ms.lasthandoff: 04/06/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Automatización de la administración de cuentas de Azure DocumentDB con la CLI de Azure 2.0
> [!div class="op_single_selector"]
> * [Portal de Azure](documentdb-create-account.md)
> * [CLI de Azure 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [CLI de Azure 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

En la guía siguiente se describen los comandos para automatizar la administración de las cuentas de base de datos de DocumentDB mediante la versión preliminar de los comandos para DocumentDB disponibles en la CLI de Azure 2.0. También incluye comandos para administrar las claves de cuenta y las prioridades de conmutación por error de las [cuentas de base de datos de varias regiones][scaling-globally]. La actualización de la cuenta de base de datos permite agregar o quitar regiones y modificar las directivas de coherencia. Para la administración multiplataforma de su cuenta de base de datos de DocumentDB, puede usar la [Azure PowerShell](documentdb-manage-account-with-powershell.md), la [API de REST de proveedor de recursos][rp-rest-api] o [Azure Portal](documentdb-create-account.md).

## <a name="getting-started"></a>Introducción

Siga las instrucciones de [instalación y configuración de la CLI de Azure 2.0][install-az-cli2] para configurar el entorno de desarrollo con la CLI de Azure 2.0.

Ejecute el siguiente comando para iniciar sesión en su cuenta de Azure y siga los pasos que aparecen en pantalla.

    az login

Si aún no tiene un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), cree uno:

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

`<resourcegrouplocation>` debe ser una de las regiones en las que DocumentDB está disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Notas

* Ejecute 'az documentdb -h' para obtener una lista completa de los comandos disponibles o visite la [página de referencia][az-documentdb-ref].
* Ejecute ' az documentdb <command> - h' para obtener una lista los parámetros obligatorios y opcionales por comando.

## <a id="create-documentdb-account-cli"></a> Creación de una cuenta de base de datos de DocumentDB

Este comando permite crear una cuenta de base de datos de DocumentDB. Configure la nueva cuenta de base de datos como de región única o de [varias regiones][scaling-globally] con una determinada [directiva de coherencia](documentdb-consistency-levels.md). 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of DocumentDB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

Ejemplos: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Notas
* Las ubicaciones deben ser regiones donde DocumentDB esté disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).
* Para habilitar el acceso al portal, incluya la dirección de este último correspondiente a su región en el filtro de intervalos de IP, como se especifica en [Configuración de la directiva de control de acceso de IP](documentdb-firewall-support.md#configure-ip-policy).

## <a id="update-documentdb-account-cli"></a> Actualización de una cuenta de base de datos de DocumentDB

Este comando permite actualizar las propiedades de la cuenta de base de datos de DocumentDB. Esto incluye la directiva de coherencia y las ubicaciones en las que existe la cuenta de la base de datos.

> [!NOTE]
> Este comando permite agregar y quitar regiones, pero no le permite modificar las prioridades de conmutación por error. Para modificar las prioridades de conmutación por error, consulte [a continuación](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

Ejemplos: 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> Incorporación o eliminación de una región de una cuenta de DocumentDB

Para agregar o quitar regiones de la cuenta de base de datos de DocumentDB existente, use el comando [update](#update-documentdb-account-cli) con el indicador `--locations`. En el ejemplo siguiente se muestra cómo crear una nueva cuenta y, más tarde, agregar y quitar regiones de esa cuenta.

Ejemplo:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Eliminación de una cuenta de base de datos de DocumentDB

Este comando permite eliminar una cuenta de base de datos de DocumentDB existente.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Ejemplo:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Obtención de las propiedades de una cuenta de base de datos de DocumentDB

Este comando permite obtener las propiedades de una cuenta de base de datos de DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Ejemplo:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Enumeración de claves de cuenta

Cuando se crea una cuenta de DocumentDB, el servicio genera dos claves de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de DocumentDB. Al proporcionar dos claves de acceso, DocumentDB permite regenerar las claves sin interrupción en la cuenta de esta base de datos. También están disponibles las claves de solo lectura para autenticar las operaciones de solo lectura. Hay dos claves de lectura y escritura (principal y secundaria) y dos claves de solo lectura (principal y secundaria).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Ejemplo:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> Enumeración de cadenas de conexión

En el caso de las cuentas de MongoDB, se puede recuperar la cadena de conexión para conectar la aplicación de MongoDB a la cuenta de la base de datos con el comando siguiente.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Ejemplo:

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Regeneración de la clave de cuenta

Cambie las claves de acceso de la cuenta de DocumentDB de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de DocumentDB, de modo que puede usar una clave de acceso mientras regenera la otra.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Ejemplo:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Modificación de la prioridad de conmutación por error de una cuenta de base de datos de DocumentDB

Para las cuentas de bases de datos de varias regiones, puede cambiar la prioridad de conmutación por error de las distintas regiones en las que existe la cuenta de base de datos de DocumentDB. Para obtener más información sobre la conmutación por error en la cuenta de base de datos de DocumentDB, consulte [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Ejemplo:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene una cuenta de DocumentDB, el siguiente paso es crear una base de datos de DocumentDB. Puede crear una base de datos mediante uno de los siguientes procedimientos:

* Azure Portal, tal como se describe en [Creación de una colección y una base de datos de DocumentDB](documentdb-create-collection.md).
* Los ejemplos de C# .NET del proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repositorio [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) de GitHub.
* Los [SDK de DocumentDB](documentdb-sdk-dotnet.md). DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript.

Después de crear la base de datos, hay que [agregar una o varias colecciones](documentdb-create-collection.md) a la base de datos y, después, [incorporar documentos](documentdb-view-json-document-explorer.md) a las colecciones.


Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#ExecutingSqlQueries) en sus documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para más información acerca de DocumentDB, consulte estos recursos:

* [Ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

