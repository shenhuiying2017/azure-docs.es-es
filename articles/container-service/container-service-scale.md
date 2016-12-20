---
title: "Escalado del clúster de ACS con la CLI de Azure | Microsoft Docs"
description: "Cómo escalar el clúster de Azure Container Service mediante la CLI de Azure."
services: container-service
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Escalado de Azure Container Service
Se pueden escalar horizontalmente los nodos de Azure Container Service (ACS) mediante la herramienta CLI de Azure. Cuando se utiliza la CLI de Azure para escalar, la herramienta devuelve un nuevo archivo de configuración que representa el cambio que se aplica al contenedor.

## <a name="about-the-command"></a>Acerca del comando
La CLI de Azure debe estar en modo Azure Resource Manager para que pueda interactuar con los contenedores de Azure. Cambie al modo Resource Manager mediante una llamada a `azure config mode arm`. El comando `acs` tiene un comando secundario denominado `scale` que realiza todas las operaciones de escalado para un servicio de contenedor. Ejecute `azure acs scale --help` para obtener ayuda acerca de los distintos parámetros que se usan en el comando de escalado, que genera algo parecido a esto:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Use el comando para escalar
Para escalar un servicio de contenedor, primero necesitará conocer el **grupo de recursos** y **el nombre del Azure Container Service (ACS)**, además de especificar el nuevo número de agentes. Con una cantidad mayor o menor, puede reducir o aumentar verticalmente, respectivamente.

Quizá quiera conocer el recuento actual de agentes para un servicio de contenedor antes de escalar. Utilice el comando `azure acs show <resource group> <ACS name>` para devolver la configuración de ACS. Anote el resultado de <mark>Count</mark>.

#### <a name="see-current-count"></a>Consulta del recuento actual
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Escalado a un recuento nuevo
Como probablemente ya está claro, puede escalar el servicio de contenedor mediante una llamada a `azure acs scale` y al proporcionar los valores de **resource group**, **ACS name** y **agent count**. Al escalar un servicio de contenedor, la CLI de Azure devuelve una cadena JSON que representa la nueva configuración del servicio de contenedor, incluido el nuevo recuento de agentes.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Pasos siguientes
* [Implementar un clúster](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


