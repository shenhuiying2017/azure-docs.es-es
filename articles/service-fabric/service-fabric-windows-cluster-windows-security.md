---
title: "Protección de un clúster en ejecución en Windows mediante la seguridad de Windows | Microsoft Docs"
description: "Aprenda a configurar la seguridad de nodo a nodo y de cliente a nodo en un clúster independiente que se ejecute en Windows mediante la seguridad de Windows."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6294a6fac8748f70c807df76feb425cb627bf4c3
ms.lasthandoff: 03/15/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger un clúster independiente en Windows mediante la seguridad de Windows
Para evitar accesos no autorizados a un clúster de Service Fabric, debe proteger el clúster. La seguridad es especialmente importante cuando el clúster ejecuta cargas de trabajo de producción. En este artículo se describe cómo configurar la seguridad de nodo a nodo y de cliente a nodo mediante la seguridad de Windows en el archivo *ClusterConfig.JSON*.  El proceso se corresponde con el paso de seguridad de configuración descrito en [Creación de un clúster independiente con Windows Server](service-fabric-cluster-creation-for-windows-server.md). Para más información sobre cómo Service Fabric usa la seguridad de Windows, consulte [Escenarios de seguridad de los clústeres](service-fabric-cluster-security.md).

> [!NOTE]
> Considere la selección de seguridad de nodo a nodo con cuidado, ya que no hay ninguna actualización de clúster de una opción de seguridad a otra. Para cambiar la selección de seguridad, tendrá que volver a generar el clúster completo.
>
>

## <a name="configure-windows-security"></a>Configuración de la seguridad de Windows  
El archivo de configuración de ejemplo *ClusterConfig.Windows.JSON* descargado junto con el paquete de clúster independiente [Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) contiene una plantilla para configurar la seguridad de Windows. La seguridad de Windows se configura en la sección **Propiedades** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **Opciones de configuración** | **Descripción** |
| --- | --- |
| ClusterCredentialType |Establézcalo en *Windows* para habilitar la seguridad de Windows. |
| ServerCredentialType |Establézcalo en *Windows* para habilitar la seguridad de Windows para clientes.<br /><br />Esto indica que los clientes del clúster, y el clúster propiamente dicho, se están ejecutando dentro de un dominio de Active Directory. |  
| WindowsIdentities |Contiene las identidades de cliente y del clúster. |  
| ClusterIdentity |Utilice un nombre de grupo de máquina como, por ejemplo, domain\machinegroup, para configurar la seguridad de nodo a nodo. |  
| ClientIdentities |Permite configurar la seguridad de cliente a nodo. Una matriz de cuentas de usuario de cliente. |  
| Identidad |Agregue el usuario de dominio, domain\username, como identidad del cliente. |  
| IsAdmin |Establézcala en true para especificar que el usuario de dominio tiene acceso de cliente de administrador, mientras que false especifica un acceso de cliente de usuario. |  

La [seguridad de nodo a nodo](service-fabric-cluster-security.md#node-to-node-security) se configura mediante **ClusterIdentity**. Para crear relaciones de confianza entre los nodos, debe asegurarse de que ambos conocen su existencia mutuamente. Esto puede realizarse mediante la creación de un grupo de dominio que incluya todos los nodos del clúster. Este nombre de grupo debe especificarse en **ClusterIdentity**. Para obtener más información, consulte el artículo sobre cómo [crear un grupo en Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).  

La [seguridad de cliente a nodo](service-fabric-cluster-security.md#client-to-node-security) se configura mediante **ClientIdentities**. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para que sepa en qué identidades de cliente puede confiar. Puede establecer la confianza de dos maneras diferentes:

- Especifique los usuarios del grupo de dominio que se pueden conectar.
- Especifique los usuarios del nodo de dominio que se pueden conectar.

Service Fabrics admite dos tipos distintos de control de acceso para los clientes que están conectados a un clúster de Service Fabric: administrador y usuario. El control de acceso permite al administrador de clústeres limitar el acceso a determinados tipos de operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster.  Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.  

La sección de **seguridad** del ejemplo siguiente configura la seguridad de Windows y especifica que las máquinas de *ServiceFabric/clusterA.contoso.com* forman parte del clúster y que *CONTOSO\usera* tiene acceso de cliente de administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric no debe implementarse en un controlador de dominio. Asegúrese de que el archivo ClusterConfig.json no incluye la dirección IP del controlador de dominio cuando use un grupo de máquinas o una cuenta de servicio administrada de grupo (gMSA).
>
>

## <a name="next-steps"></a>Pasos siguientes
Después de configurar la seguridad de Windows en el archivo *ClusterConfig.JSON* , reanude el proceso de creación de clústeres en [Creación de un clúster independiente que se ejecuta en Windows](service-fabric-cluster-creation-for-windows-server.md).

Para más información sobre la seguridad de nodo a nodo, la seguridad de cliente a nodo y el control de acceso basado en roles, consulte [Escenarios de seguridad de clúster](service-fabric-cluster-security.md).

Consulte [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md) para obtener ejemplos de conexión mediante PowerShell o FabricClient.

