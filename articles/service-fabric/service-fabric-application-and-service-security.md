---
title: Información sobre la seguridad de las aplicaciones de Azure Service Fabric | Microsoft Docs
description: Información general sobre cómo ejecutar de forma segura aplicaciones de microservicios en Service Fabric. Obtenga información acerca de cómo ejecutar un script de inicio y servicios en cuentas de seguridad diferentes, autenticar y autorizar a los usuarios, administrar secretos de aplicación, proteger las comunicaciones de servicios, usar una puerta de enlace de API y proteger los datos en reposo de las aplicaciones.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: fa6d46186ad833b68e60c24f742d210b7845759a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207917"
---
# <a name="service-fabric-application-and-service-security"></a>Seguridad del servicio y la aplicación de Service Fabric
Una arquitectura de microservicios puede aportar [muchas ventajas](service-fabric-overview-microservices.md). Administrar la seguridad de los microservicios, sin embargo, es un desafío y no es lo mismo que administrar la seguridad de las aplicaciones tradicionales monolíticas. 

Con un monolito, normalmente la aplicación se ejecuta en uno o varios servidores dentro de una red y resulta más fácil identificar las API, la dirección IP y los puertos expuestos. A menudo hay un perímetro o un límite, y una base de datos que proteger. Si ese sistema se ve en peligro debido a una infracción de seguridad o a un ataque, es probable que todo el contenido del sistema esté disponible para el atacante. Con microservicios, el sistema es más complejo.  Los servicios se descentralizan y se distribuyen en varios hosts, y migran de un host a otro.  Con la seguridad apropiada, se limitan los privilegios que un atacante puede obtener así como la cantidad de datos disponibles en un único ataque al poner en riesgo un servicio.  La comunicación no es interna, pero se realiza a través de una red y hay muchos puertos expuestos e interacciones entre servicios. Saber qué son estas interacciones de servicio y cuándo se producen resulta fundamental para la seguridad de las aplicaciones.

Este artículo no es una guía de seguridad de microservicios, ya que muchos de estos recursos están disponibles en línea. Sin embargo, describe cómo pueden abordarse los diferentes aspectos de seguridad en Service Fabric.

## <a name="authentication-and-authorization"></a>Autenticación y autorización
A menudo es necesario para los recursos y las API expuestas por un servicio limitarse a determinados usuarios de confianza o clientes. La autenticación es el proceso de determinar la identidad de un usuario de forma confiable.  La autorización es el proceso que pone las API o los servicios a disposición de algunos usuarios autenticados y no de otros.

### <a name="authentication"></a>Autenticación
El primer paso para tomar decisiones de confianza sobre el nivel de API es la autenticación. La autenticación es el proceso de determinar la identidad de un usuario de forma confiable.  Cuando hay microservicios, la autenticación normalmente se controla de forma centralizada. Si usa una API Gateway, puede [descargar la autenticación](/azure/architecture/patterns/gateway-offloading) a la puerta de enlace. Si utiliza este método, asegúrese de que no se pueda llegar directamente a los servicios individuales (sin la API Gateway) a menos que se implemente una seguridad adicional para autenticar si los mensajes proceden de la puerta de enlace o no.

Si se puede tener acceso directamente a los servicios, es posible emplear un servicio de autenticación como Azure Active Directory o un microservicio de autenticación dedicado que actúe como servicio de token de seguridad (STS) puede utilizarse para autenticar a los usuarios. Las decisiones de confianza se comparten entre los servicios con tokens de seguridad o cookies. 

En ASP.NET Core, el mecanismo principal para [autenticar a los usuarios](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) es el sistema de pertenencia a Identity de ASP.NET Core. Identity de ASP.NET Core almacena información de usuario (incluida la información de inicio de sesión, roles y notificaciones) en un almacén de datos configurado por el desarrollador. Admite la autenticación en dos fases.  También se admiten proveedores de autenticación externos, por lo que los usuarios pueden iniciar sesión con los procesos de autenticación existentes de proveedores como Microsoft, Google, Facebook o Twitter. 

### <a name="authorization"></a>Autorización
Después de la autenticación, los servicios tienen que autorizar el acceso de usuario o determinar lo que un usuario es capaz de hacer. Este proceso permite que un servicio ponga las API a disposición de algunos usuarios autenticados, pero no de todos. La autorización es ortogonal e independiente de la autenticación, que es el proceso de determinar quién es un usuario. La autenticación puede crear una o varias identidades para el usuario actual.

La [autorización de ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) puede llevarse a cabo según los roles de los usuarios o según una directiva personalizada, lo que puede incluir inspeccionar las notificaciones u otra heurística.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Restricción y protección del acceso mediante una puerta de enlace de API
Las aplicaciones en la nube normalmente necesitan una puerta de enlace front-end para proporcionar un único punto de entrada para usuarios, dispositivos u otras aplicaciones. Una [puerta de enlace de API](/azure/architecture/microservices/gateway) se ubica entre los clientes y los servicios, y es el punto de entrada a todos los servicios que proporciona la aplicación. Actúa como un proxy inverso, enrutando las solicitudes de los clientes a los servicios. También puede realizar diversas tareas transversales como la autenticación, la autorización, la terminación SSL y la limitación de velocidad. Si no implementa una puerta de enlace, los clientes deben enviar las solicitudes directamente a los servicios front-end.

En Service Fabric, una puerta de enlace puede ser cualquier servicio sin estado como una [aplicación ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) u otro servicio designado para la entrada de tráfico, como [Træfik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/) o [Azure API Management](https://docs.microsoft.com/azure/api-management).

API Management se integra directamente con Service Fabric, lo que le permite publicar API con un amplio conjunto de reglas de enrutamiento para los servicios back-end de Service Fabric.  Puede proteger el acceso a servicios back-end, evitar ataques DOS usando la limitación, o verificar las claves API, los tokens JWT, los certificados y otras credenciales. Para obtener más información, consulte [Información general de Service Fabric con Azure API Management](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Administración de secretos de aplicación
Los secretos pueden ser cualquier información confidencial, como cadenas de conexión de almacenamiento, contraseñas u otros valores que no se deben administrar en texto sin formato. En esta guía se usa Azure Key Vault para administrar las claves y los secretos. Sin embargo, el *uso* de secretos en una aplicación es independiente de la plataforma de nube para permitir que las aplicaciones se implementen en un clúster hospedado en cualquier parte.

La forma recomendada de administrar la configuración de servicio es mediante los [paquetes de configuración de servicio][config-package]. Los paquetes de configuración se actualizan mediante actualizaciones acumulativas administradas con validación de estado y reversión automática. Esto es preferible a la configuración global ya que reduce las posibilidades de una interrupción del servicio global. Los secretos cifrados no son ninguna excepción. Service Fabric presenta características integradas para cifrar y descifrar valores en un archivo Settings.xml de paquete de configuración mediante cifrado de certificados.

En el diagrama siguiente se ilustra el flujo básico para la administración de secretos en una aplicación de Service Fabric:

![información general de administración de secretos][overview]

Hay cuatro pasos principales en este flujo:

1. Obtener un certificado de cifrado de datos.
2. Instalar el certificado en el clúster.
3. Cifrar los valores de secreto al implementar una aplicación con el certificado e insértelos en un archivo de configuración Settings.xml del servicio.
4. Leer los valores cifrados de Settings.xml y usar el mismo certificado de cifrado para descifrarlos. 

[Azure Key Vault][key-vault-get-started] se usa aquí como ubicación de almacenamiento seguro para los certificados y como forma de obtener los certificados instalados en clústeres de Service Fabric en Azure. Si no va a implementar en Azure, no es necesario usar Key Vault para administrar secretos en aplicaciones de Service Fabric.

Para obtener un ejemplo, consulte [Administración de los secretos de aplicación](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Seguridad para el entorno de hospedaje
Azure Service Fabric le permite proteger aplicaciones que se ejecutan en distintas cuentas de usuario en el clúster. Service Fabric también protege los recursos que usan las aplicaciones en el momento de la implementación con la cuenta de usuario; por ejemplo, archivos, directorios y certificados. Esto aumenta la seguridad entre aplicaciones en ejecución, incluso en un entorno hospedado compartido.

El manifiesto de aplicación declara las entidades de seguridad (usuarios y grupos) que requerían ejecutar los servicios y proteger los recursos.  A estas entidades de seguridad se hace referencia en las directivas, por ejemplo en las directivas de acceso de seguridad, de ejecución, de enlace de puntos de conexión o de uso compartido de paquetes.  Las directivas se aplican entonces a los recursos del servicio en la sección **ServiceManifestImport** del manifiesto de aplicación.

Al declarar los principios, también se pueden definir y crear grupos de usuarios, de modo que se puedan agregar uno o varios usuarios a cada grupo para poder administrarlos de forma conjunta. Esto es útil cuando hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo.

De forma predeterminada, las aplicaciones de Service Fabric se ejecutan en la misma cuenta en que se ejecuta el proceso Fabric.exe. Service Fabric también permite ejecutar aplicaciones en una cuenta de usuario local o una cuenta de sistema local especificada en el manifiesto de la aplicación. Para obtener más información, consulte [Ejecución de un servicio como cuenta de usuario local o cuenta de sistema local](service-fabric-application-runas-security.md).  También puede [ejecutar un script de inicio de servicio como cuenta de usuario local o del sistema](service-fabric-run-script-at-service-startup.md).

Si va a ejecutar Service Fabric en un clúster de Windows independiente, puede ejecutar un servicio en [cuentas de dominio de Active Directory](service-fabric-run-service-as-ad-user-or-group.md) o en [cuentas de servicio administradas de grupo](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Protección de contenedores
Service Fabric proporciona un mecanismo para los servicios dentro de un contenedor para acceder a un certificado que está instalado en los nodos de un clúster de Windows o Linux (versión 5.7 o superior). Este certificado PFX puede usarse para autenticar la aplicación o el servicio, o para proteger la comunicación con otros servicios. Para obtener más información, consulte [Importación de un certificado en un contenedor](service-fabric-securing-containers.md).

Además, Service Fabric también admite gMSA (cuentas de servicio administradas de grupo) para los contenedores de Windows. Para obtener más información, consulte [Configuración de gMSA para contenedores de Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Comunicación segura del servicio
En Service Fabric, un servicio se ejecuta en algún lugar en un clúster de Service Fabric que normalmente se distribuye entre varias máquinas virtuales. Service Fabric ofrece varias opciones para proteger las comunicaciones de servicio.

Puede habilitar los puntos de conexión HTTPS en los servicios web [ASP.NET Core o Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).

Puede establecer una conexión segura entre el proxy inverso y los servicios, lo que crea un canal seguro de un extremo a otro. La conexión a servicios seguros solo se admite cuando se configura el proxy inverso para escuchar en HTTPS. Para obtener información acerca de cómo configurar el proxy inverso, lea [Proxy inverso en Azure Service Fabric](service-fabric-reverseproxy.md).  [Conectarse a un servicio seguro](service-fabric-reverseproxy-configure-secure-communication.md) describe cómo establecer una conexión segura entre el proxy inverso y los servicios.

El marco de trabajo de la aplicación de Reliable Services proporciona una serie de pilas de comunicación creadas previamente y herramientas que puede utilizar para mejorar la seguridad. Aprenda a mejorar la seguridad cuando se utiliza la comunicación remota de servicios (en [C#](service-fabric-reliable-services-secure-communication.md) o [Java](service-fabric-reliable-services-secure-communication-java.md)) o con [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Cifrado de datos en reposo de la aplicación
Cada [tipo de nodo](service-fabric-cluster-nodetypes.md) de un clúster de Service Fabric que se ejecuta en Azure se encuentra respaldado por un [conjunto de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Mediante una plantilla de Azure Resource Manager, puede asociar discos de datos a los conjuntos de escalado que componen el clúster de Service Fabric.  Si los servicios guardan los datos en un disco de datos conectado, puede [cifrar esos discos de datos](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) para proteger los datos de aplicación.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* [Ejecución de un script de instalación al iniciar el servicio](service-fabric-run-script-at-service-startup.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)
* [Más información sobre la seguridad del clúster](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png