---
title: "Protección de un clúster de Service Fabric | Microsoft Docs"
description: "Describe los escenarios de seguridad de un clúster de Service Fabric y las diferentes tecnologías que se utilizan para implementarlos."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 5afbe575a8affc37b8f902c0988585a83921e3d2
ms.contentlocale: es-es
ms.lasthandoff: 07/27/2017

---
# <a name="service-fabric-cluster-security-scenarios"></a>Escenarios de seguridad de los clústeres de Service Fabric
Un clúster de Service Fabric es un recurso que usted posee. Los clústeres deben estar protegidos para evitar que usuarios no autorizados se conecten a su clúster, especialmente cuando en él se están ejecutando cargas de trabajo de producción. Aunque es posible crear un clúster no protegido, si lo hace, permitirá que cualquier usuario anónimo se conecte a él si expone los puntos de conexión de administración al Internet público. 

En este artículo se proporciona información general sobre los escenarios de seguridad de los clústeres que se ejecutan en Azure o de forma independiente, así como sobre las diversas tecnologías que se utilizan para implementar estos escenarios. Estos son los escenarios de seguridad de clúster:

* Seguridad de nodo a nodo
* Seguridad de cliente a nodo
* Control de acceso basado en roles (RBAC)

## <a name="node-to-node-security"></a>Seguridad de nodo a nodo
Protege la comunicación entre las máquinas virtuales o las máquinas del clúster. De esta forma se garantiza que solo los equipos que están autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.

![Diagrama de comunicación de nodo a nodo][Node-to-Node]

Los clústeres que se ejecutan en Azure o los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx) para las máquinas con Windows Server.

### <a name="node-to-node-certificate-security"></a>Seguridad basada en certificados de nodo a nodo
Service Fabric usa certificados de servidor X.509 que se especifican como parte de las configuraciones del tipo de nodo cuando se crea un clúster. Al final de este artículo, se proporciona una descripción rápida de qué son estos certificados y cómo se pueden adquirir o crear.

La seguridad basada en certificados se configura al crear el clúster mediante Azure Portal, las plantillas de Azure Resource Manager o una plantilla JSON independiente. Puede especificar un certificado principal y uno secundario opcional que se utiliza para la sustitución del certificado. Los certificados principales y secundarios que especifique deben ser diferentes de los certificados de cliente de solo lectura y los de cliente de administración que determine para la [seguridad de cliente a nodo](#client-to-node-security).

En el caso de Azure, lea [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para descubrir cómo configurar la seguridad de certificado en un clúster.

Para Windows Server de modo independiente, lea [Protección de un clúster de Windows independiente mediante certificados ](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Seguridad de Windows de nodo a nodo
Para Windows Server de modo independiente, lea [Protección de un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Seguridad de cliente a nodo
Autentica los clientes y protege la comunicación entre un cliente y los nodos individuales del clúster. Este tipo de seguridad autentica y protege las comunicaciones de cliente, lo que garantiza que solo los usuarios autorizados pueden acceder al clúster y a las aplicaciones implementadas en él. Los clientes se identifican exclusivamente mediante sus credenciales de seguridad de Windows o las credenciales de seguridad del certificado.

![Diagrama de comunicación de cliente a nodo][Client-to-Node]

Los clústeres que se ejecutan en Azure o los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Seguridad basada en certificados de cliente a nodo
 La seguridad basada en certificados de cliente a nodo se configura al crear el clúster mediante Azure Portal, las plantillas de Resource Manager o una plantilla JSON independiente, especificando un certificado de cliente de administración y uno de cliente de usuario.  Es recomendable que los certificados de cliente de administración y de cliente de usuario que especifique sean diferentes de los certificados principales y secundarios que determine para la seguridad de [nodo a nodo](#node-to-node-security). De forma predeterminada, los certificados de clúster para la seguridad de nodo a nodo se agregan a la lista de certificados de administración de cliente permitidos.

Los clientes que se conectan al clúster mediante el certificado de administración tienen acceso completo a las funcionalidades de administración.  Los clientes que se conectan al clúster mediante el certificado de cliente de usuario de solo lectura tienen acceso de este tipo a las funcionalidades de administración. Es decir, estos certificados se usan para el control de acceso basado en rol (RBAC) que se describen más adelante en este artículo.

En el caso de Azure, lea [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para descubrir cómo configurar la seguridad de certificado en un clúster.

Para Windows Server de modo independiente, lea [Protección de un clúster de Windows independiente mediante certificados ](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Seguridad de Azure Active Directory (AAD) de cliente a nodo en Azure
Los clústeres que se ejecutan en Azure también pueden proteger el acceso a los puntos de conexión de administración con Azure Active Directory (AAD). Consulte [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obtener más información sobre cómo crear los artefactos de AAD necesarios, cómo rellenarlos durante la creación de clústeres y cómo conectar dichos clústeres después.

## <a name="security-recommendations"></a>Recomendaciones de seguridad
Para los clústeres de Azure, se recomienda utilizar la seguridad de AAD para autenticar clientes y certificados para la seguridad de nodo a nodo.

Para los clústeres Windows Server independientes, se recomienda que utilice la seguridad de Windows con las cuentas administradas de grupo (GMA) si tiene Windows Server 2012 R2 y Active Directory. De lo contrario, use la seguridad de Windows con cuentas de Windows.

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)
El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster. Se admiten dos tipos de control de acceso diferentes para los clientes que se conectan a un clúster: administrador y usuario.

Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.

Especifique los roles de cliente de usuario y administrador cuando cree el clúster con identidades independientes (certificados, AAD, etc.) para cada uno. Para más información sobre la configuración del control de acceso predeterminada y cómo cambiarla, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 y Service Fabric
Los certificados digitales X509 se usan habitualmente para autenticar a clientes y servidores y para cifrar y firmar mensajes digitalmente. Para más información sobre estos certificados, vaya a [Trabajar con certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algunos puntos importantes que hay que tener en cuenta:

* Los certificados usados en clústeres que ejecutan cargas de trabajo de producción deberán crearse mediante un servicio de certificados de Windows Server correctamente configurado u obtenerse de una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority)autorizada.
* No use nunca certificados temporales o de pruebas en producción creados con herramientas como MakeCert.exe.
* Puede usar un certificado autofirmado, pero solo para los clústeres de prueba y no para los que se encuentran en fase de producción.

### <a name="server-x509-certificates"></a>Certificados de servidor X.509
Los certificados de servidor tienen la tarea principal de autenticar un servidor (nodo) en los clientes o de autenticar un servidor (nodo) en un servidor (nodo). Una de las comprobaciones iniciales cuando un nodo o un cliente autentica un nodo consiste en comprobar el valor del nombre común en el campo Sujeto. Este nombre común o uno de los nombres alternativos del sujeto del certificado debe estar presente en la lista de nombres comunes permitidos.

En el siguiente artículo se describe cómo generar certificados con nombres alternativos de firmante (SAN): [Cómo agregar un nombre alternativo del firmante a un certificado LDAP seguro](http://support.microsoft.com/kb/931351).

El campo de sujeto puede contener varios valores, cada uno con un prefijo de inicialización para indicar el tipo de valor. Normalmente, la inicialización es "CN" para nombre común, por ejemplo, "CN = www.contoso.com". El campo Sujeto también puede estar en blanco. Si el campo opcional Nombre alternativo de sujeto está relleno, debe contener tanto el nombre común del certificado como una entrada por nombre alternativo de sujeto. Estos se especifican como valores de nombre DNS.

El valor del campo Propósitos planteados del certificado debe incluir un valor apropiado, como "Autenticación de servidor" o "Autenticación de cliente".

### <a name="client-x509-certificates"></a>Certificados de cliente X.509
Los certificados de cliente normalmente no los emite una entidad de certificación de terceros. En su lugar, el almacén Personal de la ubicación del usuario actual contiene normalmente los certificados de cliente colocados ahí por una entidad de certificación raíz, con un propósito planteado de "Autenticación de cliente". El cliente puede usar este tipo de certificado cuando se requiere autenticación mutua.

> [!NOTE]
> Todas las operaciones de administración en el clúster de Service Fabric requieren certificados de servidor. No se pueden usar certificados de cliente para la administración.
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Pasos siguientes
En este artículo se proporciona información conceptual sobre la seguridad de los clústeres. Después,


1.  [cree un clúster en Azure con una plantilla de Resource Manager](service-fabric-cluster-creation-via-arm.md) 
2.  [Azure Portal](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

