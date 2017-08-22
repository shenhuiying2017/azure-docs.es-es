---
title: "Introducción a la seguridad de Azure Service Fabric | Microsoft Docs"
description: "En este artículo se proporciona información general de la seguridad de Service Fabric."
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
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 4cbd2791649c6d2dd005521cedb44c17aa874073
ms.contentlocale: es-es
ms.lasthandoff: 08/11/2017

---
# <a name="azure-service-fabric-security-overview"></a>Introducción a la seguridad de Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. Service Fabric aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Los desarrolladores y administradores pueden evitar problemas complejos de infraestructura y centrarse en su lugar en las cargas de trabajo más exigentes y críticas que son escalables, confiables y fáciles de administrar.

Este artículo de información general sobre la seguridad de Azure Service Fabric se centra en las siguientes áreas:

-   Protección de su clúster
-   Supervisión y diagnóstico
-   Seguridad en el uso de certificados
-   Control de acceso basado en roles (RBAC)
-   Protección de un clúster mediante la seguridad de Windows
-   Configuración de la seguridad de la aplicación en Service Fabric
-   Protección de la comunicación de los servicios en la seguridad de Azure Service Fabric

## <a name="securing-your-cluster"></a>Protección de su clúster
Azure Service Fabric es un orquestador de servicios a través de un clúster de máquinas. Los clústeres deben estar protegidos para evitar que usuarios no autorizados se conecten a un clúster, especialmente cuando en él se están ejecutando cargas de trabajo de producción. Aunque es posible crear un clúster no protegido, si lo hace, permitirá que cualquier usuario anónimo se conecte a él si expone los puntos de conexión de administración al Internet público.

En esta sección se proporciona información general sobre los escenarios de seguridad de los clústeres que se ejecutan en Azure o de forma independiente, así como sobre las diversas tecnologías que se utilizan para implementar estos escenarios. Estos son los escenarios de seguridad de clúster:

-   Seguridad de nodo a nodo
-   Seguridad de cliente a nodo

### <a name="node-to-node-security"></a>Seguridad de nodo a nodo
Protege la comunicación entre las máquinas virtuales o las máquinas del clúster. De esta forma se garantiza que solo los equipos que están autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.

Los clústeres que se ejecutan en Azure o los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx) para las máquinas con Windows Server.

**Seguridad basada en certificados de nodo a nodo**

Service Fabric usa certificados de servidor X.509 que se especifican como parte de las configuraciones del tipo de nodo cuando se crea un clúster. En este artículo, se proporciona una descripción rápida de qué son estos certificados y [cómo se pueden adquirir o crear](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

La seguridad basada en certificados se configura al crear el clúster mediante Azure Portal, las plantillas de Azure Resource Manager o una plantilla JSON independiente. Puede especificar un certificado principal y uno secundario opcional que se utiliza para la sustitución del certificado. Los certificados principales y secundarios que especifique deben ser diferentes de los certificados de cliente de solo lectura y los de cliente de administración que determine para la [seguridad de cliente a nodo](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Seguridad de cliente a nodo
La seguridad de cliente a nodo se configura mediante ClientIdentities. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para que sepa en qué identidades de cliente puede confiar. Esto puede hacerse de dos maneras:

-   Especifique los usuarios del grupo de dominio que se pueden conectar, o bien
-   Especifique los usuarios del nodo de dominio que se pueden conectar.

Service Fabrics admite dos tipos distintos de control de acceso para los clientes que están conectados a un clúster de Service Fabric:

-   Administrador
-   Usuario

El control de acceso permite al administrador de clústeres limitar el acceso a determinados tipos de operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster. Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.

**Seguridad basada en certificados de cliente a nodo**

La seguridad basada en certificados de cliente a nodo se configura al crear el clúster mediante Azure Portal, las plantillas de Resource Manager o una plantilla JSON independiente, especificando un certificado de cliente de administración y uno de cliente de usuario. Los certificados de cliente de administración y de cliente de usuario que especifique deben ser diferentes de los certificados principales y secundarios que determine para la seguridad de nodo a nodo.

Los clientes que se conectan al clúster mediante el certificado de administración tienen acceso completo a las funcionalidades de administración. Los clientes que se conectan al clúster mediante el certificado de cliente de usuario de solo lectura tienen acceso de este tipo a las funcionalidades de administración. Es decir, estos certificados se usan para el control de acceso basado en rol (RBAC).

En el caso de Azure, lea [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para descubrir cómo configurar la seguridad de certificado en un clúster.

**Seguridad de Azure Active Directory (AAD) de cliente a nodo en Azure**

Los clústeres que se ejecutan en Azure también pueden proteger el acceso a los puntos de conexión de administración con Azure Active Directory (AAD). Consulte [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para obtener más información sobre cómo crear los artefactos de AAD necesarios, cómo rellenarlos durante la creación de clústeres y cómo conectar dichos clústeres después.

AAD permite que las organizaciones (conocidas como inquilinos) administren el acceso de los usuarios a las aplicaciones, que se dividen en aplicaciones con interfaz de usuario de inicio de sesión basada en web y aplicaciones con experiencia de cliente nativa.

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como Service Fabric Explorer y Visual Studio basados en web. Como resultado, creará dos aplicaciones de AAD para controlar el acceso al clúster, una aplicación web y una aplicación nativa.
Para los clústeres de Azure, se recomienda utilizar la seguridad de AAD para autenticar clientes y certificados para la seguridad de nodo a nodo.

Para los clústeres Windows Server independientes, se recomienda que utilice la seguridad de Windows con las cuentas administradas de grupo (GMA) si tiene Windows Server 2012 R2 y Active Directory. De lo contrario, use la seguridad de Windows con cuentas de Windows.

## <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Supervisión y diagnóstico para Azure Service Fabric
[La supervisión y el diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) son fundamentales para el desarrollo, la prueba y la implementación de aplicaciones y servicios en cualquier entorno. Las soluciones de Service Fabric funcionan mejor cuando diseña e implementa la supervisión y el diagnóstico que ayudan a asegurarse de que las aplicaciones y los servicios funcionen según lo previsto en un entorno de desarrollo local o en producción.

Desde la perspectiva de la seguridad, los objetivos principales de la supervisión y el diagnóstico son:

-   Detectar y diagnosticar problemas de hardware e infraestructura que podrían ser debidos a un evento de seguridad.
-   Detectar problemas de software y de aplicaciones que podrían proporcionar el indicador de riesgo (IoC).
-   Conocer el consumo de recursos para ayudar a evitar una denegación de servicio involuntaria.

El flujo de trabajo general de la supervisión y el diagnóstico consta de tres pasos:

-   **Generación de eventos**: incluye eventos (registros, seguimientos y eventos personalizados) tanto en los niveles de infraestructura (clúster) como en los de aplicación o servicio. Obtenga más información sobre los [eventos de nivel de infraestructura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) y los [eventos de nivel de aplicación](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) para comprender lo que se proporciona y cómo agregar otras instrumentaciones.
-   **Agregación de eventos**: es preciso recopilar y agregar los eventos generados para que se puedan mostrar. Normalmente, se recomienda usar [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (más similar a la recopilación de registros basada en agentes) o [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (recopilación de registros en proceso).
-   **Analysis**: los eventos se deben visualizar y estar accesibles en algún formato, para permitir analizarlos y mostrarlos cuando sea necesario. Hay varias plataformas excelentes en el mercado para los análisis y la visualización de datos de supervisión y diagnóstico. Los dos que se recomiendan son [OMS](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) y [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) debido a su mejor integración con Service Fabric.

También puede usar [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) para supervisar muchos de los recursos de Azure que incorporan un clúster de Service Fabric.

Un guardián es un servicio independiente que vigila el estado y la carga en los servicios e informa sobre el estado de cualquier componente en la jerarquía del modelo de mantenimiento. Esto puede ayudar a evitar errores que se pasan por alto con la vista de un solo servicio. Los guardianas también son un buen lugar donde hospedar el código que realiza acciones de subsanación sin interacción del usuario (por ejemplo, limpiar los archivos de registro de almacenamiento a determinados intervalos de tiempo). [Aquí](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/) puede encontrar la implementación de un servicio guardián de ejemplo.

## <a name="secure-using-certificates"></a>Seguridad en el uso de certificados
Mediante certificados, le indica cómo proteger la comunicación entre los diversos nodos de un clúster de Windows independiente, así cómo autenticar a los clientes que se conectan a él, mediante certificados X.509. Esto garantiza que solo los usuarios autorizados pueden tener acceso al clúster y a las aplicaciones implementadas, y realizar tareas de administración. La seguridad basada en certificados se debe haber habilitado en el clúster al crearlo.

### <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 y Service Fabric
Los certificados digitales X509 se usan habitualmente para autenticar a clientes y servidores y para cifrar y firmar mensajes digitalmente.

En la siguiente tabla se enumeran los certificados que va a necesitar en su instalación del clúster:

|Configuración de la información de certificado |Descripción|
|-------------------------------|-----------|
|ClusterCertificate|    Este certificado es necesario para proteger la comunicación entre los nodos de un clúster. Puede utilizar dos certificados diferentes, uno principal y otro secundario para la actualización.|
|ServerCertificate| Este certificado se presenta al cliente cuando intenta conectarse a este clúster. Puede utilizar dos certificados de servidor diferentes, uno principal y otro secundario para la actualización.|
|ClientCertificateThumbprints|  Se trata de un conjunto de certificados que desea instalar en los clientes autenticados.|
|ClientCertificateCommonNames|  Establezca el nombre común del primer certificado de cliente para CertificateCommonName. CertificateIssuerThumbprint es la huella digital del emisor de este certificado.|
|ReverseProxyCertificate|   Se trata de un certificado opcional que se puede especificar si desea proteger el [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Para más información sobre cómo proteger los certificados, [haga clic aquí](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)
El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster. Se admiten dos tipos de control de acceso diferentes para los clientes que se conectan a un clúster: administrador y usuario.

Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.

Especifique los roles de cliente de usuario y administrador cuando cree el clúster con identidades independientes (certificados, AAD, etc.) para cada uno. Para más información sobre la configuración del control de acceso predeterminada y cómo cambiarla, consulte [Control de acceso basado en roles para clientes de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-using-windows-security"></a>Protección de un clúster independiente mediante la seguridad de Windows
Para evitar accesos no autorizados a un clúster de Service Fabric, debe proteger el clúster. La seguridad es especialmente importante cuando el clúster ejecuta cargas de trabajo de producción. Se describe cómo configurar la seguridad de nodo a nodo y de cliente a nodo mediante la seguridad de Windows en el archivo ClusterConfig.JSON.

**Configuración de la seguridad de Windows mediante gMSA**

La seguridad de nodo a nodo se configura estableciendo [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) cuando Service Fabric tiene que ejecutarse en gMSA. Para crear relaciones de confianza entre los nodos, debe asegurarse de que ambos conocen su existencia mutuamente.

La seguridad de cliente a nodo se configura mediante ClientIdentities. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para que sepa en qué identidades de cliente puede confiar.

**Configuración de la seguridad de Windows mediante un grupo de máquinas**

La seguridad de nodo a nodo se configura mediante el uso de ClusterIdentity, si desea usar un grupo de máquinas dentro de un dominio de Active Directory. Para más información, consulte el artículo [Create a Machine Group in Active Directory](https://msdn.microsoft.com/library/aa545347) (Creación de un grupo de máquinas en Active Directory).

La seguridad de cliente a nodo se configura mediante ClientIdentities. Para establecer la confianza entre un cliente y el clúster, debe configurar el clúster para que sepa en qué identidades de cliente puede confiar. Puede establecer la confianza de dos maneras diferentes:

-   Especifique los usuarios del grupo de dominio que se pueden conectar.
-   Especifique los usuarios del nodo de dominio que se pueden conectar.

## <a name="configure-application-security-in-service-fabric"></a>Configuración de la seguridad de la aplicación en Service Fabric
### <a name="managing-secrets-in-service-fabric-applications"></a>Administración de secretos en aplicaciones de Service Fabric
Este método ayuda a administrar secretos en una aplicación de Service Fabric. Los secretos pueden ser cualquier información confidencial, como cadenas de conexión de almacenamiento, contraseñas u otros valores que no se deben administrar en texto sin formato.

Esta solución usa [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para administrar las claves y los secretos. Sin embargo, el uso de secretos en una aplicación es independiente de la plataforma de nube para permitir que las aplicaciones se implementen en un clúster hospedado en cualquier parte. Hay cuatro pasos principales en este flujo:

-   Obtener un certificado de cifrado de datos.
-   Instalar el certificado en el clúster.
-   Cifrar los valores de secreto al implementar una aplicación con el certificado e insértelos en un archivo de configuración Settings.xml del servicio.
-   Leer los valores cifrados de Settings.xml y usar el mismo certificado de cifrado para descifrarlos.

>[!Note]
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

-   [Ayuda para proteger un servicio cuando usa la comunicación remota para los servicios](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).
-   [Ayuda para proteger un servicio cuando se utiliza una pila de comunicación basada en WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack).

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información conceptual acerca de la seguridad de los clústeres, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) y [Azure Portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Para más información, consulte [Escenarios de seguridad de los clústeres de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

