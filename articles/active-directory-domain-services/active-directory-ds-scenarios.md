---
title: "Active Directory Domain Services: escenarios de implementación | Microsoft Docs"
description: "Escenarios de implementación de Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: fc1aa4d778a6232258470fc9a58a98906c86ec7b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="deployment-scenarios-and-use-cases"></a>Escenarios y casos de uso de implementación
En esta sección, echamos un vistazo a algunos escenarios y casos de uso que se benefician de Azure Active Directory (AD) Domain Services.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Administración sencilla y segura de máquinas virtuales de Azure
Puede usar Azure Active Directory Domain Services para administrar máquinas virtuales de Azure de forma optimizada. Azure Virtual Machines puede combinarse con el dominio administrado, lo que permite utilizar sus credenciales corporativas de AD para iniciar sesión. Este enfoque ayuda a evitar problemas de administración de credenciales, como el mantenimiento de cuentas de administrador local en cada una de sus máquinas virtuales de Azure.

Las máquinas virtuales de servidor que están unidas al dominio administrado también se pueden administrar y proteger mediante la directiva de grupo. Puede aplicar las líneas de base de seguridad necesarias para las máquinas virtuales de Azure y bloquearlas con arreglo a las directrices de seguridad corporativa. Por ejemplo, puede utilizar las funcionalidades de administración de directiva de grupo para restringir los tipos de aplicaciones que pueden iniciarse en estas máquinas virtuales.

![Administración simplificada de máquinas virtuales de Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

A medida que los servidores y otras infraestructuras alcanzan el final de su ciclo de vida, Contoso traslada a la nube muchas aplicaciones que actualmente se hospedan de forma local. Su norma actual de TI dictamina que los servidores que hospedan aplicaciones corporativas se deben unir a un dominio y administrarse mediante la directiva de grupo. El administrador de TI de Contoso prefiere unir a un dominio las máquinas virtuales implementadas en Azure, con el fin de facilitar la administración. Como resultado, los administradores y usuarios pueden iniciar sesión con sus credenciales corporativas. Al mismo tiempo, las máquinas pueden configurarse para cumplir con las líneas de base de seguridad necesarias mediante directivas de grupo. Contoso preferiría no tener que implementar, supervisar y administrar los controladores de dominio de Azure para proteger las máquinas virtuales de Azure. Por lo tanto, Azure AD Domain Services es un elemento idóneo para este caso de uso.

**Notas de implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

* Los dominios administrados que proporcionan Azure AD Domain Services solo admiten una única estructura de unidad organizativa plana. Todas las máquinas unidas a un dominio residen en una única unidad organizativa plana. Sin embargo, puede crear unidades organizativas personalizadas.
* Los Servicios de dominio de Azure AD admiten una directiva de grupo simple en forma de un GPO integrado para cada uno de los contenedores de usuarios y equipos. Puede crear GPO personalizados y dirigirlos a unidades organizativas personalizadas.
* Los Servicios de dominio de Azure AD son compatible con el esquema base de objeto de equipo de AD. No puede extender el esquema del objeto de equipo.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Subida y desplazamiento de una aplicación local que usa la autenticación de enlace LDAP a los Servicios de infraestructura de Azure
![Enlace LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso cuenta con una aplicación local que se compró a un ISV hace muchos años. La aplicación está actualmente en modo de mantenimiento por el ISV y para Contoso resulta excesivamente caro solicitar cambios en la aplicación. Esta aplicación tiene un front-end basado en web que recopila las credenciales de usuario mediante un formulario web y luego autentica a los usuarios mediante la realización de un enlace LDAP a Active Directory corporativo. A Contoso le gustaría migrar esta aplicación a los Servicios de infraestructura de Azure. Es conveniente que la aplicación funcione como está, sin que sea necesario realizar ningún cambio. Además, los usuarios deben poder autenticarse con las credenciales corporativas existentes y sin tener que volver a entrenar a los usuarios a hacer las cosas de manera diferente. En otras palabras, los usuarios finales deben olvidarse de dónde se ejecuta la aplicación y la migración debe ser transparente para ellos.

**Notas de implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

* Asegúrese de que la aplicación no necesita modificar o escribir en el directorio. No se admite el acceso de escritura de LDAP a dominios administrados proporcionado por los Servicios de dominio de Azure AD.
* No se pueden cambiar las contraseñas directamente en el dominio administrado. Los usuarios finales pueden cambiar su contraseña bien mediante el mecanismo de autoservicio de cambio de contraseña de Azure AD o en el directorio local. Estos cambios se sincronizan y están disponibles automáticamente en el dominio administrado.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Subida y desplazamiento de una aplicación local que usa la lectura LDAP para acceder al directorio a los Servicios de infraestructura de Azure
Contoso tiene una aplicación de línea de negocio (LOB) local que se desarrolló hace casi una década. Esta aplicación tiene en cuenta el directorio y se diseñó para funcionar con Windows Server AD. La aplicación usa LDAP (Lightweight Directory Access Protocol) para leer los atributos o información sobre los usuarios de Active Directory. La aplicación no modifica los atributos ni escribe en el directorio. A Contoso le gustaría migrar esta aplicación a los Servicios de infraestructura de Azure y retirar el hardware local antiguo que actualmente hospeda esta aplicación. La aplicación no se puede volver a escribir para que use las modernas API de directorio, como la API de Azure AD Graph basada en REST. Por lo tanto, se necesita una opción de subida y desplazamiento mediante la cual se pueda migrar la aplicación para ejecutarse en la nube, sin modificar el código o volver a escribirla.

**Notas de implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

* Asegúrese de que la aplicación no necesita modificar o escribir en el directorio. No se admite el acceso de escritura de LDAP a dominios administrados proporcionado por los Servicios de dominio de Azure AD.
* Asegúrese de que la aplicación no necesita un esquema de Active Directory ampliado o personalizado. No se admiten extensiones de esquema en los Servicios de dominio de Azure AD.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migración de una aplicación de dominio o servicio local a los Servicios de infraestructura de Azure
Algunas aplicaciones constan de varios niveles, donde uno de los niveles necesita realizar llamadas autenticadas a un nivel de back-end, como un nivel de base de datos. Las cuentas de servicio de Active Directory se utilizan normalmente para estos casos de uso. Puede desplazar dichas aplicaciones a los servicios de infraestructura de Azure y usar Azure AD Domain Services para las necesidades de la identidad de estas aplicaciones. Puede optar por usar la misma cuenta de servicio sincronizada desde su directorio local a Azure AD. Como alternativa, primero puede crear una unidad organizativa personalizada y después crear una cuenta de servicio independiente en dicha unidad organizativa para implementar estas aplicaciones.

![Cuenta de servicio mediante WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso tiene una aplicación de almacén de software personalizada que incluye un front-end web, un servidor SQL y un servidor FTP de back-end. La autenticación integrada de Windows de las cuentas de servicio se usa para autenticar el web front-end en el servidor FTP. El front-end web está configurado para ejecutarse como una cuenta de servicio. El servidor back-end está configurado para autorizar el acceso de la cuenta de servicio para el front-end web. Contoso prefiere no tener que implementar una máquina virtual de controlador de dominio en la nube para mover esta aplicación a los servicios de infraestructura de Azure. El administrador de TI de Contoso puede implementar los servidores que hospedan el front-end web, el servidor SQL y el servidor FTP en las máquinas virtuales de Azure. Estas máquinas se unen después a un dominio administrado por Azure AD Domain Services. Entonces, pueden usar la misma cuenta de servicio en su directorio local para la autenticación de la aplicación. Esta cuenta de servicio se sincroniza con el dominio administrado de Azure AD Domain Services y está disponible para su uso.

**Notas de implementación**

Tenga en cuenta los siguientes puntos importantes para este escenario de implementación:

* Asegúrese de que la aplicación usa nombre de usuario y contraseña para la autenticación. Los Servicios de dominio de Azure AD no admiten la autenticación basada en certificado o tarjeta inteligente.
* No se pueden cambiar las contraseñas directamente en el dominio administrado. Los usuarios finales pueden cambiar su contraseña bien mediante el mecanismo de autoservicio de cambio de contraseña de Azure AD o en el directorio local. Estos cambios se sincronizan y están disponibles automáticamente en el dominio administrado.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implementaciones de Servicios de Escritorio Remoto de Windows Server en Azure
Puede usar Azure AD Domain Services para proporcionar servicios de dominio de AD administrados a los servidores de escritorio remotos implementados en Azure.

Para más información acerca de este escenario de implementación, vea cómo [integrar Azure AD Domain Services con su implementación de RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Clústeres de HDInsight unidos a dominio (versión preliminar)
Puede configurar un clúster de HDInsight de Azure que está unido a un dominio administrado de Azure AD Domain Services con Apache Ranger habilitado. Crear y aplicar directivas de Hive a través de Apache Ranger y permitir a los usuarios (por ejemplo, los científicos de datos) conectarse a Hive con herramientas basadas en ODBC, por ejemplo, Excel, Tableau, etc. Microsoft está trabajando para incluir otras cargas de trabajo, como HBase, Spark y Storm, a HDInsight unido a un dominio en breve.

Para más información acerca de este escenario de implementación, consulte cómo [configurar clústeres de HDInsight unidos a un dominio](../hdinsight/domain-joined/apache-domain-joined-configure.md)
