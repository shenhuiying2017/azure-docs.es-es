---
title: "Introducción a la seguridad de Azure Service Fabric | Microsoft Docs"
description: "En este artículo se proporciona información general de la seguridad de Azure Service Fabric."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 64717da922701aabd27e15a67e8da1b0acb30b77
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-service-fabric-security-overview"></a>Introducción a la seguridad de Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. Service Fabric aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Los desarrolladores y administradores pueden evitar problemas complejos de infraestructura y centrarse en su lugar en las cargas de trabajo más exigentes y críticas que son escalables, confiables y fáciles de administrar.

Este artículo de información general sobre la seguridad de Azure Service Fabric se centra en las siguientes áreas:

-   Protección de su clúster
-   Información sobre la habilitación de supervisión y diagnóstico
-   Creación de entornos más seguros mediante el uso de certificados
-   Control de acceso basado en rol (RBAC)
-   Protección de clústeres mediante la seguridad de Windows
-   Configuración de la seguridad de las aplicaciones en Service Fabric
-   Protección de la comunicación de los servicios de Azure Service Fabric 

## <a name="secure-your-cluster"></a>Protección del clúster
Azure Service Fabric es un orquestador de servicios a través de un clúster de máquinas. Los clústeres deben estar protegidos para evitar que usuarios no autorizados se conecten a ellos, especialmente cuando ejecutan cargas de trabajo en entornos de producción. Aunque es posible crear un clúster no protegido, ello podría permitir que usuarios anónimos se conectaran a él, si expusiera los puntos de conexión de administración a Internet público.

Esta sección proporciona información general sobre los escenarios de seguridad para los clústeres que se ejecutan tanto de forma independiente como en Azure. También se describen las diversas tecnologías que se usan para implementar estos escenarios. Estos son los escenarios de seguridad de clúster:

-   Seguridad de nodo a nodo
-   Seguridad de cliente a nodo

### <a name="node-to-node-security"></a>Seguridad de nodo a nodo
La seguridad de nodo a nodo protege la comunicación entre las máquinas virtuales o las máquinas de un clúster. Con este tipo de seguridad, solo los equipos que están autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.

Los clústeres que se ejecutan en Azure o los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx) para las máquinas con Windows Server.

**Información sobre la seguridad basada en certificados de nodo a nodo**

Service Fabric usa certificados de servidor X.509 que se especifican cuando se crea un clúster. Para obtener una descripción rápida de qué son estos certificados y cómo se pueden adquirir o crear, consulte [Trabajo con certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Configure la seguridad basada en certificados al crear el clúster mediante Azure Portal, las plantillas de Azure Resource Manager o una plantilla JSON independiente. Puede especificar un certificado principal y uno secundario opcional que se utiliza para la sustitución del certificado. Los certificados principales y secundarios que especifique deben ser diferentes de los certificados de cliente de solo lectura y de los de cliente de administración que determine para la [seguridad de cliente a nodo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Seguridad de cliente a nodo
La seguridad de cliente a nodo se configura mediante identidades de cliente. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para que sepa en qué identidades de cliente puede confiar. Esto puede hacerse de dos maneras:

-   Especifique los usuarios del grupo de dominio que se pueden conectar. 
-   Especifique los usuarios del nodo de dominio que se pueden conectar.

Service Fabrics admite dos tipos distintos de control de acceso para los clientes que están conectados a un clúster de Service Fabric:

-   Administrador
-   Usuario

Mediante el uso de control de acceso, los administradores de clústeres pueden limitar el acceso a ciertos tipos de operaciones de clúster. Esto hace que el clúster sea más seguro.

 Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.

**Información sobre la seguridad basada en certificados de cliente a nodo**

Configure la seguridad de certificados de cliente a nodo al crear el clúster mediante Azure Portal, las plantillas de Resource Manager o una plantilla JSON independiente. Debe especificar un certificado de cliente de administración o un certificado de cliente de usuario. 

Los certificados de cliente de administración y de cliente de usuario que especifique deben ser diferentes de los certificados principales y secundarios que determine para la seguridad de nodo a nodo.

Los clientes que se conectan al clúster mediante el certificado de administración tienen acceso completo a las funcionalidades de administración. Los clientes que se conectan al clúster mediante el certificado de cliente de usuario de solo lectura tienen acceso de este tipo a las funcionalidades de administración. En otras palabras, estos certificados se usan para RBAC.

Para aprender a configurar la seguridad de certificado en un clúster, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Información sobre la seguridad de Azure Active Directory de cliente a nodo en Azure**

Los clústeres que se ejecutan en Azure también pueden proteger el acceso a los puntos de conexión de administración con Azure Active Directory (Azure AD). Para información sobre cómo crear los artefactos de Azure Active Directory necesarios, cómo rellenarlos durante la creación de clústeres y cómo conectar a dichos clústeres, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD permite a las organizaciones (conocidas como inquilinos) administrar el acceso de los usuarios a las aplicaciones. Hay aplicaciones con una interfaz de usuario de inicio de sesión basada en web y otras que tienen una experiencia de cliente nativa.

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como Service Fabric Explorer y Visual Studio basados en web. Como resultado, creará dos aplicaciones de Azure AD para controlar el acceso al clúster, una web y otra nativa.

Para los clústeres de Azure, se recomienda utilizar la seguridad de Azure AD para autenticar a los clientes y certificados para la seguridad de nodo a nodo.

Para los clústeres Windows Server independientes con Windows Server 2012 R2 y Active Directory, se recomienda que utilice la seguridad de Windows con las cuentas administradas de grupo.  De lo contrario, use la seguridad de Windows con cuentas de Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Información sobre supervisión y diagnóstico en Azure Service Fabric
[La supervisión y el diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) son fundamentales para el desarrollo, la prueba y la implementación de aplicaciones y servicios en cualquier entorno. Las soluciones de Service Fabric funcionan mejor cuando implementa la supervisión y el diagnóstico para asegurarse de que las aplicaciones y los servicios funcionen según lo previsto en un entorno de desarrollo local o en producción.

Desde la perspectiva de la seguridad, los objetivos principales de la supervisión y el diagnóstico son:

-   Detectar y diagnosticar problemas de hardware e infraestructura que podrían ser debidos a un evento de seguridad.
-   Detectar problemas de software y de aplicaciones que podrían proporcionar el indicador de riesgo (IoC).
-   Conocer el consumo de recursos para ayudar a evitar una denegación de servicio involuntaria.

El flujo de trabajo general de la supervisión y el diagnóstico consta de tres pasos:

-   **Generación de eventos**: incluye eventos (registros, seguimientos y eventos personalizados) tanto en los niveles de infraestructura (clúster) como en los de aplicación o servicio. Obtenga más información sobre los [eventos de nivel de infraestructura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) y los [eventos de nivel de aplicación](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) para saber lo que se proporciona y cómo agregar otras instrumentaciones.

-   **Agregación de eventos**: es preciso recopilar y agregar los eventos generados para que se puedan mostrar. Normalmente, se recomienda usar [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (similar a la recopilación de registros basada en agentes) o [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (recopilación de registros en proceso).

-   **Analysis**: los eventos se deben visualizar y estar accesibles en algún formato, para permitir analizarlos y mostrarlos. Hay varias plataformas para el análisis y la visualización de datos de supervisión y diagnóstico. Los dos que se recomiendan son [Operations Managment Suite](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) y [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) debido a su mejor integración con Service Fabric.

También puede usar [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) para supervisar muchos de los recursos de Azure que incorporan un clúster de Service Fabric.

Un guardián es un servicio independiente que vigila el estado y la carga en los servicios, e informa sobre el estado de cualquier componente en la jerarquía del modelo de mantenimiento. Puede ayudar a evitar errores que se pasan por alto con la vista de un solo servicio. 

Los guardianas también son un buen lugar donde hospedar el código que realiza acciones de subsanación sin interacción del usuario (por ejemplo, limpiar los archivos de registro de almacenamiento a determinados intervalos de tiempo). Puede encontrar una implementación de servicio guardián de ejemplo en [Ejemplo de guardián de Azure Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Información sobre la protección de la comunicación mediante el uso de certificados
Los certificados le ayudan a proteger la comunicación entre los diversos nodos de un clúster de Windows independiente. Mediante el uso de certificados X.509, también se puede autenticar a los clientes que se conectan a este clúster. Esto garantiza que solo los usuarios autorizados puedan tener acceso al clúster. Se recomienda que habilite un certificado en el clúster al crearlo.

### <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 y Service Fabric
Los certificados digitales X509 se usan habitualmente para autenticar a clientes y servidores. También se usan para cifrar y firmar digitalmente los mensajes.

En la siguiente tabla se enumeran los certificados que va a necesitar en su instalación del clúster:

|Configuración de la información de certificado |Descripción|
|-------------------------------|-----------|
|ClusterCertificate|    Este certificado es necesario para proteger la comunicación entre los nodos de un clúster. Puede utilizar dos certificados diferentes: uno principal y otro secundario para la actualización.|
|ServerCertificate| Este certificado se presenta al cliente cuando intenta conectarse a este clúster. Puede utilizar dos certificados de servidor diferentes: uno principal y otro secundario para la actualización.|
|ClientCertificateThumbprints|  Se trata de un conjunto de certificados para instalar en los clientes autenticados.|
|ClientCertificateCommonNames|  Es el nombre común del primer certificado de cliente para CertificateCommonName. CertificateIssuerThumbprint es la huella digital del emisor de este certificado.|
|ReverseProxyCertificate|   Se trata de un certificado opcional que se puede especificar para proteger el [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Para más información acerca de cómo proteger los certificados, consulte [Protección de un clúster independiente en Windows mediante certificados X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Información sobre el control de acceso basado en rol
El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster. Se admiten dos tipos de control de acceso diferentes para los clientes que se conectan a un clúster: 

- Rol de administrador
- Rol de usuario

Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.

Especifique los roles de cliente de usuario y de administrador cuando cree el clúster con identidades independientes (incluidos los certificados) para cada uno. Para más información sobre la configuración del control de acceso predeterminada y cómo cambiarla, consulte [Control de acceso basado en roles para clientes de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Protección de un clúster independiente mediante la seguridad de Windows
Para evitar accesos no autorizados a un clúster de Service Fabric, debe proteger el clúster. La seguridad es especialmente importante cuando el clúster ejecuta cargas de trabajo de producción. Se describe cómo configurar la seguridad de nodo a nodo y de cliente a nodo mediante la seguridad de Windows en el archivo ClusterConfig.JSON.

**Configuración de la seguridad de Windows mediante gMSA**

Cuando Service Fabric tenga que ejecutarse en gMSA, configure la seguridad de nodo a nodo estableciendo [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Para crear relaciones de confianza entre los nodos, debe asegurarse de que ambos conocen su existencia mutuamente.

La seguridad de cliente a nodo se configura mediante ClientIdentities. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para que reconozca en qué identidades de cliente puede confiar.

**Configuración de la seguridad de Windows mediante un grupo de máquinas**

Si desea usar un grupo de máquinas dentro de un dominio de Active Directory, la seguridad de nodo a nodo se configura estableciendo ClusterIdentity. Para más información, consulte el artículo [Create a Machine Group in Active Directory](https://msdn.microsoft.com/library/aa545347) (Creación de un grupo de máquinas en Active Directory).

La seguridad de cliente a nodo se configura mediante ClientIdentities. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para reconocer en qué identidades de cliente puede confiar. Puede establecer la confianza de dos maneras diferentes:

-   Especifique los usuarios del grupo de dominio que se pueden conectar.
-   Especifique los usuarios del nodo de dominio que se pueden conectar.

## <a name="configure-application-security-in-service-fabric"></a>Configuración de la seguridad de la aplicación en Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Administración de los secretos en aplicaciones de Service Fabric
Este método ayuda a administrar los secretos en una aplicación de Service Fabric. Los secretos pueden ser cualquier información confidencial, como cadenas de conexión de almacenamiento, contraseñas u otros valores que no se deben administrar en texto sin formato.

Esta solución usa [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para administrar las claves y los secretos. Sin embargo, el uso de secretos en una aplicación es independiente de la plataforma en la nube. Esto significa que las aplicaciones pueden implementarse en un clúster que se hospede en cualquier lugar. Hay cuatro pasos principales en este flujo:

-   Obtener un certificado de cifrado de datos.
-   Instalar el certificado en el clúster.
-   Cifrar los valores de secreto al implementar una aplicación con el certificado e insértelos en un archivo de configuración Settings.xml del servicio.
-   Leer los valores cifrados de Settings.xml y usar el mismo certificado de cifrado para descifrarlos.

>[!NOTE]
>Más información sobre cómo [administrar secretos en aplicaciones de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configuración de directivas de seguridad para la aplicación
La seguridad de Azure Service Fabric permite proteger las aplicaciones que se ejecutan en distintas cuentas de usuario en el clúster. La seguridad de Service Fabric también ayuda a proteger los recursos que usan las aplicaciones en el momento de la implementación con la cuenta de usuario; por ejemplo, archivos, directorios y certificados. Esto aumenta la seguridad entre aplicaciones en ejecución, incluso en un entorno hospedado compartido.

Entre los pasos se incluyen:

-   Configuración de la directiva para un punto de entrada del programa de instalación del servicio.
-   Inicio de comandos de PowerShell desde un punto de entrada de configuración.
-   Uso de la redirección de la consola para la depuración local.
-   Configuración de una directiva para los paquetes de código del servicio.
-   Asignación de una directiva de acceso de seguridad a los puntos de conexión HTTP y HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Protección de la comunicación de los servicios en la seguridad de Azure Service Fabric
La seguridad es uno de los aspectos más importantes de la comunicación. El marco de trabajo de la aplicación de Reliable Services proporciona una serie de pilas de comunicación creadas previamente y herramientas que se pueden utilizar para mejorar la seguridad.

-   [Ayuda para garantizar un servicio cuando usa la comunicación remota para los servicios](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Ayuda para garantizar un servicio cuando se utiliza una pila de comunicación basada en WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información conceptual acerca de la seguridad de los clústeres, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) y [Azure Portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Para más información acerca de la seguridad de los clústeres de Service Fabric, consulte [Escenarios de seguridad de los clústeres de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
