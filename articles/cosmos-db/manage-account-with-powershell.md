---
title: "Azure Automation Cosmos DB: administración con Powershell | Microsoft Docs"
description: Use Azure Powershell para administrar las cuentas de Azure Cosmos DB.
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: d2436ad639c53360f4d1afde99d668285b606aa9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Creación de una cuenta de Azure Cosmos DB mediante PowerShell

En la guía siguiente, se describen los comandos para automatizar la administración de las cuentas de base de datos de Azure Cosmos DB mediante Azure Powershell. También incluye comandos para administrar las claves de cuenta y las prioridades de conmutación por error de las [cuentas de base de datos de varias regiones][scaling-globally]. La actualización de la cuenta de base de datos le permite agregar o quitar regiones y modificar las directivas de coherencia. Para la administración entre plataformas de su cuenta de base de datos de Azure Cosmos DB, puede usar la [CLI de Azure](cli-samples.md), la [API de REST de proveedor de recursos][rp-rest-api] o [Azure Portal](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Introducción

Siga las instrucciones de [Cómo instalar y configurar Azure PowerShell][powershell-install-configure] para instalar e iniciar sesión en su cuenta de Azure Resource Manager en Powershell.

### <a name="notes"></a>Notas

* Si desea ejecutar los siguientes comandos sin necesidad de confirmación del usuario, anexe la marca `-Force` al comando.
* Todos los siguientes comandos son sincrónicos.

## <a id="create-documentdb-account-powershell"></a> Creación de una cuenta de Azure Cosmos DB

Este comando le permite crear una cuenta de base de datos de Azure Cosmos DB. Configure la nueva cuenta de base de datos como de región única o de [varias regiones][scaling-globally] con una determinada [directiva de coherencia](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` El nombre de la ubicación de la región de escritura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error de 0. Debe haber exactamente una región de escritura por cuenta de base de datos.
* `<read-region-location>` El nombre de la ubicación de la región de lectura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error mayor que 0. Puede haber más de una región de lectura por cuenta de base de datos.
* `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de Azure Cosmos DB](firewall-support.md).
* `<default-consistency-level>` El nivel de coherencia predeterminado de la cuenta de Azure Cosmos DB. Para más información, consulte [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` Cuando se utiliza con coherencia de obsolescencia limitada, este valor representa la cantidad de tiempo de obsolescencia (en segundos) tolerada. El intervalo aceptado para este valor es de 1 - 100.
* `<max-staleness-prefix>` Cuando se utiliza con la coherencia de obsolescencia limitada, este valor representa el número de solicitudes obsoletas toleradas. El intervalo aceptado para este valor es de 1 - 2.147.483.647.
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<resource-group-location>` La ubicación del grupo de recursos de Azure al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB que se va a crear. Solo se admiten minúsculas, números y el carácter "-", y debe tener entre 3 y 50 caracteres.

Ejemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Notas
* En el ejemplo anterior se crea una cuenta de base de datos con dos regiones. También es posible crear una cuenta de base de datos con una región (que se designa como la región de escritura y tiene un valor de prioridad de conmutación por error de 0) o más de dos regiones. Para obtener más información, vea la información de [cuentas de bases de datos de varias regiones][scaling-globally].
* Las ubicaciones deben ser regiones donde Azure Cosmos DB esté disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Actualizar una cuenta de base de datos Azure Cosmos DB

Este comando permite actualizar las propiedades de la cuenta de base de datos de Azure Cosmos DB. Esto incluye la directiva de coherencia y las ubicaciones en las que existe la cuenta de la base de datos.

> [!NOTE]
> Este comando le permite agregar y quitar regiones, pero no le permite modificar las prioridades de conmutación por error. Para modificar las prioridades de conmutación por error, consulte [a continuación](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` El nombre de la ubicación de la región de escritura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error de 0. Debe haber exactamente una región de escritura por cuenta de base de datos.
* `<read-region-location>` El nombre de la ubicación de la región de lectura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error mayor que 0. Puede haber más de una región de lectura por cuenta de base de datos.
* `<default-consistency-level>` El nivel de coherencia predeterminado de la cuenta de Azure Cosmos DB. Para más información, consulte [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de Azure Cosmos DB](firewall-support.md).
* `<max-interval>` Cuando se utiliza con coherencia de obsolescencia limitada, este valor representa la cantidad de tiempo de obsolescencia (en segundos) tolerada. El intervalo aceptado para este valor es de 1 - 100.
* `<max-staleness-prefix>` Cuando se utiliza con la coherencia de obsolescencia limitada, este valor representa el número de solicitudes obsoletas toleradas. El intervalo aceptado para este valor es de 1 - 2.147.483.647.
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<resource-group-location>` La ubicación del grupo de recursos de Azure al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB que se va a actualizar.

Ejemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Eliminar una cuenta de base de datos Azure Cosmos DB

Este comando le permite eliminar una cuenta de base de datos de Azure Cosmos DB existente.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB que se va a eliminar.

Ejemplo:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Obtener las propiedades de una cuenta de base de datos Azure Cosmos DB

Este comando le permite obtener las propiedades de una cuenta de base de datos de Azure Cosmos DB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB.

Ejemplo:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Actualización de la etiquetas de una cuenta de base de datos de Azure Cosmos DB

En el ejemplo siguiente, se describe cómo establecer [etiquetas de recursos de Azure][azure-resource-tags] para su cuenta de base de datos de Azure Cosmos DB.

> [!NOTE]
> Este comando se puede combinar con los comandos de creación o actualización anexando la marca `-Tags` con el parámetro correspondiente.

Ejemplo:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Enumerar claves de cuenta

Cuando se crea una cuenta de Azure Cosmos DB, el servicio genera dos claves de acceso maestras que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de Azure Cosmos DB. Al proporcionar dos claves de acceso, Azure Cosmos DB permite regenerar las claves sin interrupción en la cuenta de Azure Cosmos DB. También están disponibles las claves de solo lectura para autenticar las operaciones de solo lectura. Hay dos claves de lectura y escritura (principal y secundaria) y dos claves de solo lectura (principal y secundaria).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB.

Ejemplo:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Enumeración de cadenas de conexión

En el caso de las cuentas de MongoDB, se puede recuperar la cadena de conexión para conectar la aplicación de MongoDB a la cuenta de la base de datos con el comando siguiente.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB.

Ejemplo:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Regenerar la clave de cuenta

Cambie periódicamente las claves de acceso de la cuenta de Azure Cosmos DB para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de Azure Cosmos DB, de modo que puede usar una clave de acceso mientras regenera la otra.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB.
* `<key-kind>` Uno de los cuatro tipos de claves: ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] que desea regenerar.

Ejemplo:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modificación de la prioridad de conmutación por error de una cuenta de base de datos de Azure Cosmos DB

En el caso de cuentas de bases de datos de varias regiones, puede cambiar la prioridad de conmutación por error de las distintas regiones en las que existe la cuenta de base de datos de Azure Cosmos DB. Para obtener más información sobre la conmutación por error en la cuenta de base de datos de Azure Cosmos DB, consulte [Distribución de datos global con Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` El nombre de la ubicación de la región de escritura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error de 0. Debe haber exactamente una región de escritura por cuenta de base de datos.
* `<read-region-location>` El nombre de la ubicación de la región de lectura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error mayor que 0. Puede haber más de una región de lectura por cuenta de base de datos.
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de Azure Cosmos DB.
* `<database-account-name>` El nombre de la cuenta de base de datos de Azure Cosmos DB.

Ejemplo:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>pasos siguientes

* Para conectarse con .NET, consulte [Conexión y consultas con .NET](create-sql-api-dotnet.md).
* Para conectarse con Node.js, consulte [Conexión y consultas con Node.js y una aplicación de MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/
