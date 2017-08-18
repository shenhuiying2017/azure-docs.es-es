---
title: Procedimientos recomendados de seguridad de Azure Service Fabric | Microsoft Docs
description: "En este artículo se proporciona un conjunto de procedimientos recomendados para la reforzar la seguridad de Azure Service Fabric."
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
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Procedimientos recomendados de seguridad de Azure Service Fabric
La implementación de una aplicación en Azure es un proceso rápido, sencillo y rentable. Antes de implementar la aplicación en la nube en un entorno de producción, es útil evaluarla teniendo como referencia una lista de procedimientos recomendados esenciales.

Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. Service Fabric también aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Los desarrolladores y administradores pueden evitar problemas complejos de infraestructura y centrarse en su lugar en las cargas de trabajo más exigentes y críticas que son escalables, confiables y fáciles de administrar. 

Para cada procedimiento recomendado, explicaremos:

-   Qué es el procedimiento recomendado
-   Por qué le conviene habilitar este procedimiento recomendado
-   Cuál podría ser el resultado si no habilita el procedimiento recomendado
-   Cómo aprender a habilitar el procedimiento recomendado

Actualmente disponemos de los siguientes procedimientos recomendados de seguridad de Azure Service Fabric:

-   Uso de una plantilla de Azure Resource Manager (ARM) y el módulo Microsoft Azure PowerShell de Azure Service Fabric para crear un clúster seguro
-   Uso de certificados X.509
-   Configuración de directivas de seguridad
-   Configuración de seguridad de Reliable Actors
-   Configuración de SSL para Azure Service Fabric
-   Aislamiento y seguridad de red con Azure Service Fabric
-   Configuración de un almacén de claves de seguridad
-   Asignación de usuarios a roles


## <a name="best-practices-for-securing-your-cluster"></a>Procedimientos recomendados para proteger el clúster

**Idea general**

Use siempre un clúster seguro
-   Seguridad del clúster: uso de certificados
-   Acceso de cliente (solo lectura y administración): uso de AAD

Use implementaciones automatizadas
-   Use scripts para generar, implementar y pasar secretos
-   Conserve los secretos en KV, use AD para todos los demás accesos de cliente
-   Ningún usuario debe tener acceso a ellos sin autenticación.

Además, tenga en cuenta lo siguiente:
-   Cree zonas desmilitarizadas con grupos de seguridad de red (NSG)
-   Use servidores de salto a RDP en máquinas virtuales de clúster o para administrar el clúster

Los clústeres deben estar protegidos para evitar que usuarios no autorizados se conecten a su clúster, especialmente cuando en él se están ejecutando cargas de trabajo de producción. Aunque es posible crear un clúster no protegido, si lo hace, permitirá que cualquier usuario anónimo se conecte a él si expone los puntos de conexión de administración al Internet público.

Tecnologías que se usan para implementar estos escenarios. Estos son los [escenarios de seguridad de clúster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security):

-   Seguridad de nodo a nodo: protege la comunicación entre las máquinas virtuales y los equipos del clúster. De esta forma se garantiza que solo los equipos que están autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.
Los clústeres que se ejecutan en Azure o los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) o la [seguridad de Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) para las máquinas con Windows Server.
-   Seguridad de cliente a nodo: protege la comunicación entre un cliente Service Fabric y los nodos individuales del clúster.
-   Control de acceso basado en rol (RBAC): especifique los roles de cliente de usuario y administrador cuando cree el clúster con identidades independientes (certificados, AAD, etc.) para cada uno.
-   Recomendaciones de seguridad: para los clústeres de Azure, se recomienda utilizar la seguridad de AAD para autenticar los clientes y certificados para la seguridad de nodo a nodo.

Para configurar el clúster de Windows independiente, consulte [Opciones de configuración de clústeres de Windows independientes](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Use plantillas de Azure Resource Manager y el módulo Microsoft Azure PowerShell de Azure Service Fabric para crear un clúster seguro.
[Aquí](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) hay disponible una guía paso a paso que le dirige en la configuración de un clúster seguro de Azure Service Fabric en Azure con Azure Resource Manager.

Use la plantilla de Azure Resource Manager para personalizar el clúster
-   Almacenamiento administrado por el programa de instalación de discos duros virtuales de máquinas virtuales

Uso de la plantilla de Azure Resource Manager para los cambios de la unidad para el grupo de recursos
-   Fácil administración de la configuración
-   Auditoría

Trate la configuración del clúster como código
-   Sea exhaustivo en la comprobación de las configuraciones que elija implementar
-   Evite el uso de comandos implícitos para retocar directamente los recursos

Muchos aspectos del [ciclo de vida de la aplicación de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) pueden automatizarse. El [módulo Microsoft Azure Powershell de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiza las tareas comunes para implementar, actualizar, quitar y probar aplicaciones de Azure Service Fabric. También cuenta con API HTTP y administradas para la administración de aplicaciones.

## <a name="use-x509-certificates"></a>Uso de certificados X.509
Los clústeres siempre deben protegerse mediante certificados X.509 o con la seguridad de Windows. La configuración solo se configura en el momento de creación del clúster y no es posible habilitar la seguridad una vez creado.

Si va a especificar un [certificado de clúster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), establezca el valor de ClusterCredentialType en X509. Si va a especificar un certificado de servidor para conexiones externas, establezca ServerCredentialType en X509.

-   Los certificados usados en clústeres que ejecutan cargas de trabajo de producción deberán crearse mediante un servicio de certificados de Windows Server correctamente configurado u obtenerse de una entidad de certificación (CA) autorizada.
-   No use nunca certificados temporales o de pruebas en producción creados con herramientas como MakeCert.exe.
-   Puede usar un certificado autofirmado, pero solo para los clústeres de prueba y no para los que se encuentran en fase de producción.

Si el clúster no es seguro. Cualquiera puede conectarse de forma anónima y realizar operaciones de administración, por lo que los clústeres de producción siempre deben protegerse mediante certificados X.509 o la seguridad de Windows.

Para más información sobre cómo habilitar certificados en un clúster de Service Fabric, consulte [Agregar o quitar certificados para un clúster de Service Fabric de Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configuración de directivas de seguridad
Service Fabric también protege los recursos que usan las aplicaciones en el momento de la implementación con la cuenta de usuario; por ejemplo, archivos, directorios y certificados. Esto aumenta la seguridad entre aplicaciones en ejecución, incluso en un entorno hospedado compartido.

-   Use un usuario o grupo de dominio de Active Directory: puede ejecutar el servicio con las credenciales de una cuenta de usuario o grupo de Active Directory. Esto se aplica a Active Directory local dentro del dominio y no a Azure Active Directory (AAD). Mediante el uso de un grupo o usuario de dominio puede tener acceso a otros recursos del dominio (por ejemplo, recursos compartidos de archivos) para los que se han concedido permisos.

-   Asigne una directiva de acceso de seguridad para los puntos de conexión HTTP y HTTPS: si aplica una directiva RunAs a un servicio y el manifiesto de servicio declara que hay recursos de puntos de conexión con el protocolo HTTP, es preciso especificar una directiva SecurityAccessPolicy para asegurarse de que los puertos asignados a dichos puntos de conexión aparezcan correctamente en la lista de control de acceso de la cuenta de usuario RunAs en la que se ejecuta el servicio. En caso contrario, http.sys no tiene acceso al servicio y aparecen errores en las llamadas del cliente.
Para más información sobre cómo habilitar directivas de seguridad en Service Fabric, consulte [Configuración de directivas de seguridad para la aplicación](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="reliable-actors-security-configuration"></a>Configuración de seguridad de Reliable Actors
Service Fabric Reliable Actors es una implementación del modelo de diseño del actor. Al igual que sucede con cualquier modelo de diseño de software, la decisión sobre si usar un modelo específico se toma en función de si un problema de diseño de software se adapta al modelo o no.

Como regla general, tenga en cuenta el patrón de actor para modelar su problema o escenario si:
-   El espacio del problema implica un gran número (miles o más) de unidades pequeñas, independientes y aisladas de estado y lógica.
-   Desea trabajar con objetos uniproceso que no requieren una interacción importante de los componentes externos, incluida la consulta del estado en un conjunto de actores.
-   Las instancias de actor no bloquearán a los autores de llamadas con retrasos imprevisibles emitiendo operaciones de E/S.

En Service Fabric, los actores se implementan en el marco de trabajo de Reliable Actors: un marco de trabajo de la aplicación basado en el patrón de actor y en [Reliable Services de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Cada servicio de Reliable Actors que escribe es en realidad un servicio de confianza con estado particionado.
Cada actor se define como una instancia de un tipo de actor, similar a la forma en que un objeto de .NET es una instancia de un tipo de .NET. Por ejemplo, puede haber un tipo de actor que implemente la funcionalidad de una calculadora y podría haber muchos actores de ese tipo que se distribuyesen en varios nodos en un clúster. Cada uno de esos actores se identifica de forma única mediante un identificador de actor.

[Las configuraciones de seguridad del replicador](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) se usan para proteger el canal de comunicación que se utiliza durante la replicación. Esto significa que los servicios no ven el tráfico de replicación de unos y los otros, lo que garantiza que los datos de alta disponibilidad también están protegidos. De forma predeterminada, una sección de configuración de seguridad vacía impide la seguridad de la replicación.
Las configuraciones de replicador configuran el replicador que es responsable de hacer que el proveedor de estado del actor resulte altamente confiable.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configuración de SSL para Azure Service Fabric

Autenticación del servidor: [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) los puntos de conexión de administración del clúster en un cliente de administración, de forma que este sabe que está hablando con el clúster real. Este certificado proporciona también [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) para la API de administración de HTTPS y para Service Fabric Explorer sobre HTTPS.
Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene uno de estos certificados, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:
-   El certificado debe contener una clave privada.
-   El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
-   El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio cloudapp.net. Debe adquirir un nombre de dominio personalizado para usarlo cuando obtenga acceso a su servicio. Cuando solicite un certificado de una entidad de certificación, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usó para tener acceso a su aplicación. Por ejemplo, si su nombre de dominio personalizado es contoso.com, solicitaría un certificado a su entidad de certificación para **.contoso.com** o **www.contoso.com.**
-   Este certificado debe usar un cifrado de 2048 bits como mínimo.

HTTP es inseguro y está sometido a ataques de interceptación porque los datos que se transfieren desde el explorador web en el servidor web o entre otros puntos de conexión se transmiten en texto sin formato. Esto significa que los atacantes pueden interceptar y ver datos confidenciales, como los detalles de las tarjetas de crédito e inicios de sesión de cuenta. Cuando los datos se envían o se publican a través de un explorador mediante HTTPS, SSL garantiza que esa información se cifre y esté a salvo de la interceptación.

Para más información, consulte [Configuración de SSL para una aplicación en Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Aislamiento y seguridad de red con Azure Service Fabric
Use la [plantilla de Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) como ejemplo de configuración de un clúster seguro de tres nodos y para controlar el tráfico de red entrante y saliente con grupos de seguridad de red.

La plantilla tiene un grupo de seguridad de red para cada uno de los conjuntos de escalado de máquinas virtuales a fin de controlar el tráfico dentro y fuera de ellos. De forma predeterminada, las reglas se configuran para permitir todo el tráfico que necesitan los servicios del sistema y los puertos de la aplicación especificados en la plantilla. Revise esas reglas y realice los cambios que necesite, incluso puede agregar algunas para las aplicaciones.

Para más información, consulte [Azure Service Fabric: escenarios comunes de redes](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-a-key-vault-for-security"></a>Configuración de un almacén de claves de seguridad
Los certificados se usan en Service Fabric para proporcionar autenticación y cifrado con el fin de proteger diversos aspectos de un clúster y sus aplicaciones.

Service Fabric usa certificados X.509 para proteger un clúster y proporcionar características de seguridad de las aplicaciones. Azure Key Vault se usa para [administrar certificados](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) para clústeres de Service Fabric en Azure. Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados de Key Vault y los instala en las máquinas virtuales del clúster.

La relación entre [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), un clúster de Service Fabric y el proveedor de recursos de Azure que usa los certificados almacenados en el almacén de claves cuando se crea el clúster.

**Creación de un grupo de recursos** El primer paso es crear un grupo de recursos específicamente para el almacén de claves. Se recomienda colocar el almacén de claves en su propio grupo de recursos. Esto le permite quitar los grupos de recursos de proceso y almacenamiento, incluido el grupo de recursos que tiene el clúster de Service Fabric, sin perder las claves y los secretos. El grupo de recursos que contiene el almacén de claves debe estar en la misma región que el clúster que lo usa.

**Creación de un almacén de claves en el nuevo grupo de recursos** El almacén de claves debe estar habilitado para la implementación de forma que el proveedor de recursos de proceso pueda recibir de él certificados e instalarlos en instancias de máquina virtual.
Para más información sobre cómo configurar Azure Key Vault, consulte [Introducción a Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-roles"></a>Asignación de roles a usuarios
Una vez que haya creado las aplicaciones para representar el clúster, debe asignar los usuarios a los roles compatibles con Service Fabric: solo lectura y administrador. Puede asignar los roles mediante el Portal de Azure clásico.

>[!Note]
> Para más información sobre los roles de Service Fabric, consulte [Control de acceso basado en roles para clientes de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric admite dos tipos distintos de control de acceso para los clientes que están conectados a un [clúster de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administrador y usuario. El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster.

## <a name="next-steps"></a>Pasos siguientes
- Configuración del [entorno de desarrollo](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) de Service Fabric.
- Más información sobre las [opciones de soporte técnico de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).


