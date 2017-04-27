---
title: "Automatización de Azure DocumentDB: administración con Powershell | Microsoft Docs"
description: Use Azure Powershell para administrar las cuentas de base de datos de DocumentDB. DocumentDB es una base de datos NoSQL basada en la nube para datos JSON.
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1d7691fd5248691f42ad31224f2fff9f7f0d4b9e
ms.lasthandoff: 04/03/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-powershell"></a>Automatización de la administración de cuentas de Azure DocumentDB con Azure Powershell
> [!div class="op_single_selector"]
> * [Azure Portal](documentdb-create-account.md)
> * [CLI de Azure 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [CLI de Azure 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

En la guía siguiente se describen los comandos para automatizar la administración de las cuentas de base de datos de DocumentDB mediante Azure Powershell. También incluye comandos para administrar las claves de cuenta y las prioridades de conmutación por error de las [cuentas de base de datos de varias regiones][scaling-globally]. La actualización de la cuenta de base de datos le permite agregar o quitar regiones y modificar las directivas de coherencia. Para la administración entre plataformas de su cuenta de base de datos de DocumentDB, puede usar la [CLI de Azure](documentdb-automation-resource-manager-cli.md), la [API de REST de proveedor de recursos][rp-rest-api] o [Azure Portal](documentdb-create-account.md).

## <a name="getting-started"></a>Introducción

Siga las instrucciones de [Cómo instalar y configurar Azure PowerShell][powershell-install-configure] para instalar e iniciar sesión en su cuenta de Azure Resource Manager en Powershell.

### <a name="notes"></a>Notas

* Si desea ejecutar los siguientes comandos sin necesidad de confirmación del usuario, anexe la marca `-Force` al comando.
* Todos los siguientes comandos son sincrónicos.

## <a id="create-documentdb-account-powershell"></a> Creación de una cuenta de base de datos de DocumentDB

Este comando le permite crear una cuenta de base de datos de DocumentDB. Configure la nueva cuenta de base de datos como de región única o de [varias regiones][scaling-globally] con una determinada [directiva de coherencia](documentdb-consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` El nombre de la ubicación de la región de escritura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error de 0. Debe haber exactamente una región de escritura por cuenta de base de datos.
* `<read-region-location>` El nombre de la ubicación de la región de lectura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error mayor que 0. Puede haber más de una región de lectura por cuenta de base de datos.
* `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de DocumentDB](documentdb-firewall-support.md).
* `<default-consistency-level>` El nivel de coherencia predeterminado de la cuenta de DocumentDB. Para más información, consulte [Niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).
* `<max-interval>` Cuando se utiliza con coherencia de obsolescencia limitada, este valor representa la cantidad de tiempo de obsolescencia (en segundos) tolerada. El intervalo aceptado para este valor es de 1 - 100.
* `<max-staleness-prefix>` Cuando se utiliza con la coherencia de obsolescencia limitada, este valor representa el número de solicitudes obsoletas toleradas. El intervalo aceptado para este valor es de 1 - 2.147.483.647.
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<resource-group-location>` La ubicación del grupo de recursos de Azure a la que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB que se va a crear. Solo se admiten minúsculas, números y el carácter "-", y debe tener entre 3 y 50 caracteres.

Ejemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -PropertyObject $DocumentDBProperties

### <a name="notes"></a>Notas
* En el ejemplo anterior se crea una cuenta de base de datos con dos regiones. También es posible crear una cuenta de base de datos con una región (que se designa como la región de escritura y tiene un valor de prioridad de conmutación por error de 0) o más de dos regiones. Para obtener más información, vea la información de [cuentas de bases de datos de varias regiones][scaling-globally].
* Las ubicaciones deben ser regiones donde DocumentDB esté disponible de forma general. Se proporciona la lista actual de regiones en la [página Regiones de Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Actualización de una cuenta de base de datos de DocumentDB

Este comando permite actualizar las propiedades de la cuenta de base de datos de DocumentDB. Esto incluye la directiva de coherencia y las ubicaciones en las que existe la cuenta de la base de datos.

> [!NOTE]
> Este comando le permite agregar y quitar regiones, pero no le permite modificar las prioridades de conmutación por error. Para modificar las prioridades de conmutación por error, consulte [a continuación](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` El nombre de la ubicación de la región de escritura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error de 0. Debe haber exactamente una región de escritura por cuenta de base de datos.
* `<read-region-location>` El nombre de la ubicación de la región de lectura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error mayor que 0. Puede haber más de una región de lectura por cuenta de base de datos.
* `<default-consistency-level>` El nivel de coherencia predeterminado de la cuenta de DocumentDB. Para más información, consulte [Niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).
* `<ip-range-filter>` Especifica el conjunto de direcciones IP o intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Para más información, consulte [Compatibilidad con el firewall de DocumentDB](documentdb-firewall-support.md).
* `<max-interval>` Cuando se utiliza con coherencia de obsolescencia limitada, este valor representa la cantidad de tiempo de obsolescencia (en segundos) tolerada. El intervalo aceptado para este valor es de 1 - 100.
* `<max-staleness-prefix>` Cuando se utiliza con la coherencia de obsolescencia limitada, este valor representa el número de solicitudes obsoletas toleradas. El intervalo aceptado para este valor es de 1 - 2.147.483.647.
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<resource-group-location>` La ubicación del grupo de recursos de Azure a la que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB que se va actualizar.

Ejemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -PropertyObject $DocumentDBProperties

## <a id="delete-documentdb-account-powershell"></a> Eliminación de una cuenta de base de datos de DocumentDB

Este comando le permite eliminar una cuenta de base de datos de DocumentDB existente.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB que se va a eliminar.

Ejemplo:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Obtención de las propiedades de una cuenta de base de datos de DocumentDB

Este comando le permite obtener las propiedades de una cuenta de base de datos de DocumentDB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB.

Ejemplo:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Actualización de etiquetas de una cuenta de base de datos de DocumentDB

En el ejemplo siguiente se describe cómo establecer [etiquetas de recursos de Azure][azure-resource-tags] para su cuenta de base de datos de DocumentDB.

> [!NOTE]
> Este comando se puede combinar con los comandos de creación o actualización anexando la marca `-Tags` con el parámetro correspondiente.

Ejemplo:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Enumerar claves de cuenta

Cuando se crea una cuenta de DocumentDB, el servicio genera dos claves de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de DocumentDB. Al proporcionar dos claves de acceso, DocumentDB permite regenerar las claves sin interrupción en la cuenta de esta base de datos. También están disponibles las claves de solo lectura para autenticar las operaciones de solo lectura. Hay dos claves de lectura y escritura (principal y secundaria) y dos claves de solo lectura (principal y secundaria).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB.

Ejemplo:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Enumeración de cadenas de conexión

En el caso de las cuentas de MongoDB, se puede recuperar la cadena de conexión para conectar la aplicación de MongoDB a la cuenta de la base de datos con el comando siguiente.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB.

Ejemplo:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Regenerar la clave de cuenta

Cambie las claves de acceso de la cuenta de DocumentDB de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de DocumentDB, de modo que puede usar una clave de acceso mientras regenera la otra.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB.
* `<key-kind>` Uno de los cuatro tipos de claves: ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] que desea regenerar.

Ejemplo:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modificar la prioridad de conmutación por error de una cuenta de base de datos de DocumentDB

Para las cuentas de bases de datos de varias regiones, puede cambiar la prioridad de conmutación por error de las distintas regiones en las que existe la cuenta de base de datos de DocumentDB. Para obtener más información sobre la conmutación por error en la cuenta de base de datos de DocumentDB, consulte [Distribución de datos global con DocumentDB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` El nombre de la ubicación de la región de escritura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error de 0. Debe haber exactamente una región de escritura por cuenta de base de datos.
* `<read-region-location>` El nombre de la ubicación de la región de lectura de la cuenta de base de datos. Esta ubicación debe tener un valor de prioridad de conmutación por error mayor que 0. Puede haber más de una región de lectura por cuenta de base de datos.
* `<resource-group-name>` El nombre del [grupo de recursos de Azure][azure-resource-groups] al que pertenece la nueva cuenta de base de datos de DocumentDB.
* `<database-account-name>` El nombre de la cuenta de base de datos de DocumentDB.

Ejemplo:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/en-us/azure/powershell-install-configure
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[distribute-data-globally]: https://docs.microsoft.com/en-us/azure/documentdb/documentdb-distribute-data-globally
[azure-resource-groups]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/
