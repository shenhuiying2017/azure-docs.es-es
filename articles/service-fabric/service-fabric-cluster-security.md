---
title: "Protección de un clúster de Azure Service Fabric | Microsoft Docs"
description: "Información acerca de los escenarios de seguridad para un clúster de Azure Service Fabric y las diferentes tecnologías que se pueden usar para implementarlos."
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
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: f0fdbd7fc4ec48037371ffa296cf668897e45b70
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>Escenarios de seguridad de los clústeres de Service Fabric
Un clúster de Azure Service Fabric es un recurso que usted posee. Tiene la responsabilidad de proteger los clústeres para impedir que usuarios no autorizados se conecten a ellos. Proteger el clúster es especialmente importante si en él se ejecutan cargas de trabajo de producción. Aunque es posible crear un clúster sin protección, si este expone los puntos de conexión de administración a Internet público, podrían conectarse a él usuarios anónimos. Los clústeres sin protección no se admiten para cargas de trabajo de producción. 

Este artículo es una introducción a escenarios de seguridad para los clústeres de Azure e independientes, y las distintas tecnologías que se pueden usar para implementarlos:

* Seguridad de nodo a nodo
* Seguridad de cliente a nodo
* Control de acceso basado en rol (RBAC)

## <a name="node-to-node-security"></a>Seguridad de nodo a nodo
La seguridad de nodo a nodo ayuda a proteger la comunicación entre las máquinas virtuales o los equipos de un clúster. Este escenario de seguridad garantiza que solo los equipos autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.

![Diagrama de comunicación de nodo a nodo][Node-to-Node]

Los clústeres que se ejecutan en Azure y los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx) para los equipos con Windows Server.

### <a name="node-to-node-certificate-security"></a>Seguridad basada en certificados de nodo a nodo
Service Fabric usa certificados de servidor X.509 que se especifican como parte de las configuraciones del tipo de nodo al crear un clúster. Al final de este artículo se proporciona una introducción a estos certificados y a cómo se pueden adquirir o crear.

Configure la seguridad basada en certificados al crear el clúster, mediante Azure Portal, las plantillas de Azure Resource Manager o una plantilla JSON independiente. Puede especificar un certificado principal y uno secundario opcional, que se utiliza para la sustitución del certificado. Los certificados principales y secundarios que especifique deben ser diferentes de los certificados de cliente de solo lectura y de los de cliente de administración que determine para la [seguridad de cliente a nodo](#client-to-node-security).

Para aprender a configurar la seguridad basada en certificados en un clúster para Azure, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para aprender a configurar la seguridad basada en certificados en los clústeres para uno de Windows Server independiente, consulte [Protección de un clúster independiente en Windows mediante certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Seguridad de Windows de nodo a nodo
Para aprender a configurar la seguridad de Windows para clústeres de Windows Server independientes, consulte [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Seguridad de cliente a nodo
La seguridad de cliente a nodo autentica los clientes y ayuda a proteger la comunicación entre un cliente y los nodos individuales del clúster. Este tipo de seguridad ayuda a garantizar que solo los usuarios autorizados accedan al clúster y a las aplicaciones implementadas en él. Los clientes se identifican exclusivamente mediante sus credenciales de seguridad de Windows o las credenciales de seguridad del certificado.

![Diagrama de comunicación de cliente a nodo][Client-to-Node]

Los clústeres que se ejecutan en Azure y los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Seguridad basada en certificados de cliente a nodo
Configure la seguridad basada en certificados de cliente a nodo al crear el clúster, mediante Azure Portal, las plantillas de Resource Manager o una plantilla JSON independiente. Para crear el certificado, especifique un certificado de cliente de administración o de cliente de usuario. Es recomendable que los certificados de cliente de administración y de cliente de usuario que especifique sean diferentes de los certificados principales y secundarios que determine para la [seguridad de nodo a nodo](#node-to-node-security). De forma predeterminada, los certificados de clúster para la seguridad de nodo a nodo se agregan a la lista de certificados de administración de cliente permitidos.

Los clientes que se conectan al clúster mediante el certificado de administración tienen acceso completo a las funcionalidades de administración. Los clientes que se conectan al clúster mediante el certificado de cliente de usuario de solo lectura tienen acceso de este tipo a las funcionalidades de administración. Estos certificados se usan para el control de acceso basado en rol (RBAC) que se describe más adelante en este artículo.

Para aprender a configurar la seguridad basada en certificados en un clúster para Azure, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para aprender a configurar la seguridad basada en certificados en los clústeres para uno de Windows Server independiente, consulte [Protección de un clúster independiente en Windows mediante certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Seguridad de Azure Active Directory de cliente a nodo en Azure
Para los clústeres que se ejecutan en Azure también se puede proteger el acceso a los puntos de conexión de administración con Azure Active Directory (Azure AD). Para aprender a crear los artefactos de Azure AD necesarios, a rellenarlos al crear el clúster y a conectarlos después a estos últimos, consulte el artículo de [configuración de un clúster con una plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Recomendaciones de seguridad
Para la seguridad de nodo a nodo en los clústeres de Azure, se recomienda utilizar la seguridad de Azure AD para autenticar a los clientes y certificados.

Para los clústeres de Windows Server independientes, si tiene Windows Server 2012 R2 y Windows Active Directory, se recomienda la seguridad de Windows con las cuentas de servicio administradas de grupo. De lo contrario, use la seguridad de Windows con cuentas de Windows.

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en rol (RBAC)
Puede usar el control de acceso para limitarlo a determinadas operaciones de clúster para los diferentes grupos de usuarios. Esto ayuda a que el clúster esté más protegido. Se admiten dos tipos de control de acceso para los clientes que se conectan a un clúster: rol de administrador y usuario.

Los usuarios con el rol de administrador tienen acceso total a las funcionalidades de administración (incluidas las de lectura y escritura). Los usuarios con el rol de usuario, de forma predeterminada, tienen acceso de solo lectura a las funcionalidades de administración (por ejemplo, funcionalidad de consulta). También pueden resolver aplicaciones y servicios.

Los roles de administrador y de usuario del cliente se definen al crear el clúster. Para asignar roles, proporcione identidades diferentes (por ejemplo, con certificados o Azure AD) a cada tipo de rol. Para más información sobre la configuración del control de acceso predeterminada y cómo cambiarla, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 y Service Fabric
Los certificados digitales X.509 se usan habitualmente para autenticar clientes y servidores. También se usan para cifrar y firmar digitalmente los mensajes. Para más información acerca de los certificados digitales X.509, consulte [Trabajar con certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algunos puntos importantes que hay que tener en cuenta:

* Para crear certificados para clústeres que ejecutan cargas de trabajo de producción, use un servicio de certificados de Windows Server correctamente configurado o uno de una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) autorizada.
* Absténgase de usar certificados temporales o de prueba que se crean con herramientas como MakeCert.exe en un entorno de producción.
* Puede usar un certificado autofirmado, pero solo en un clúster de prueba. No utilice un certificado autofirmado en un entorno de producción.

### <a name="server-x509-certificates"></a>Certificados de servidor X.509
Los certificados de servidor tienen la tarea principal de autenticar un servidor (nodo) en los clientes o de autenticar un servidor (nodo) en un servidor (nodo). Cuando un nodo o un cliente autentica un nodo, una de las comprobaciones iniciales se ocupa del valor del nombre común en el campo **Firmante**. Este nombre común o uno de los nombres alternativos del sujeto (SAN) del certificado deben encontrarse en la lista de nombres comunes permitidos.

Para aprender a generar certificados que tengan SAN, consulte [Cómo agregar un nombre alternativo del sujeto a un certificado LDAP seguro](http://support.microsoft.com/kb/931351).

El campo **Asunto** puede tener varios valores. Los valores llevan un prefijo de inicialización para indicar el tipo de valor. Normalmente, la inicialización es **CN** (para *nombre común*); por ejemplo, **CN = www.contoso.com**. El campo **Firmante** puede estar en blanco. Si el campo opcional **Nombre alternativo de firmante** está relleno, debe contener tanto el nombre común del certificado como una entrada por nombre alternativo de firmante. Estos se especifican como valores de **Nombre DNS**.

El valor del campo **Propósitos planteados** del certificado debe incluir un valor apropiado, como **Autenticación de servidor** o **Autenticación de cliente**.

### <a name="client-x509-certificates"></a>Certificados de cliente X.509
Los certificados de cliente normalmente no los emite una entidad de certificación de terceros. En su lugar, el almacén Personal de la ubicación del usuario actual contiene normalmente los certificados de cliente que coloca allí una entidad de certificación raíz, con un **Propósito planteado** de **Autenticación de cliente**. El cliente puede usar este certificado cuando se requiera autenticación mutua.

> [!NOTE]
> Todas las operaciones de administración en el clúster de Service Fabric requieren certificados de servidor. No se pueden usar certificados de cliente para la administración.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un clúster en Azure con una plantilla de Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Creación de un clúster mediante Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
