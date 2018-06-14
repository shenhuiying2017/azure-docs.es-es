---
title: Información general sobre seguridad en Data Lake Store | Microsoft Docs
description: Descripción de cómo Azure Data Lake Store es un almacén de macrodatos más seguro
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: f4014d304d1e0a7d841e797c3e3bdfe31918c151
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198164"
---
# <a name="security-in-azure-data-lake-store"></a>Seguridad en el Almacén de Azure Data Lake
Muchas empresas están aprovechando el análisis de macrodatos para obtener conocimientos empresariales que les ayuden a tomar decisiones inteligentes. Es posible que una organización tenga un entorno regulado y complejo, con un número cada vez mayor de distintos tipos de usuarios. Es esencial para una empresa asegurarse de que los datos empresariales más importantes se almacenaron de la forma más segura, y que se concedió a los usuarios individuales el nivel adecuado de acceso. Azure Data Lake Store está diseñado para ayudar a cumplir estos requisitos de seguridad. En este artículo aprenderá las funcionalidades de seguridad de Data Lake Store, como:

* Autenticación
* Autorización
* Aislamiento de red
* Protección de datos
* Auditoría

## <a name="authentication-and-identity-management"></a>Autenticación y administración de identidades
La autenticación es el proceso por el que se comprueba una identidad de usuario cuando el usuario interactúa con Data Lake Store o con cualquier servicio que se conecte al mismo. Para la autenticación y administración de identidades, Data Lake Store usa [Azure Active Directory](../active-directory/active-directory-whatis.md), una completa solución en la nube de administración de identidades y acceso que simplifica la administración de usuarios y grupos.

Cada una de las suscripciones de Azure puede asociarse a una instancia de Azure Active Directory. Solo los usuarios y las identidades de servicio definidas en el servicio de Azure Active Directory pueden tener acceso a la cuenta de Data Lake Store mediante el Portal de Azure, las herramientas de línea de comandos, o mediante aplicaciones cliente que la organización compila con el SDK de Azure Data Lake Store. Las principales ventajas del uso de Azure Active Directory como un mecanismo de control de acceso centralizado son:

* Administración simplificada del ciclo de vida de las identidades. La identidad de un usuario o un servicio (una identidad principal del servicio) se puede crear y revocar rápidamente; basta con eliminar o deshabilitar la cuenta en el directorio.
* Multi-factor authentication. [Multi-factor authentication](../active-directory/authentication/multi-factor-authentication.md) proporciona un nivel de seguridad adicional para los inicios de sesión y las transacciones del usuario.
* Autenticación desde cualquier cliente mediante un protocolo estándar abierto, como OAuth u OpenID.
* Federación con los servicios de directorio de empresa y los proveedores de identidades en la nube.

## <a name="authorization-and-access-control"></a>Control de autorización y acceso
Una vez que un usuario se autentica mediante Azure Active Directory para acceder a Azure Data Lake Store, la autorización controla los permisos de acceso a Data Lake Store. Data Lake Store separa la autorización para las actividades relacionadas con cuentas y las relacionadas con datos de la siguiente manera:

* [Control de acceso basado en rol](../role-based-access-control/overview.md) (RBAC) proporcionado por Azure para la administración de cuentas
* ACL de POSIX para el acceso a datos en el almacén

### <a name="rbac-for-account-management"></a>RBAC para la administración de cuentas
Se definen cuatro roles básicos para Data Lake Store de forma predeterminada. Estos roles permiten diferentes operaciones en una cuenta de Data Lake Store mediante el Portal de Azure, los cmdlets de PowerShell y las API de REST. Los roles Propietario y Colaborador pueden realizar diversas funciones de administración en la cuenta. Puede asignar el rol Lector a los usuarios que solo ven los datos de administración de cuentas.

![Roles RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Roles RBAC")

Tenga en cuenta que aunque se asignen roles para la administración de cuentas, algunos roles afectarán al acceso a los datos. Para las operaciones que el usuario puede realizar en el sistema de archivos, deberá utilizar listas de control de acceso (ACL). La tabla siguiente muestra un resumen de los derechos de administración y los derechos de acceso a datos para los roles predeterminados.

| Roles | Derechos de administración | Derechos de acceso a datos | Explicación |
| --- | --- | --- | --- |
| Ningún rol asignado |None |Regido por ACL |El usuario no puede utilizar el Portal de Azure ni los cmdlets de Azure PowerShell para explorar Data Lake Store. El usuario solo puede utilizar las herramientas de línea de comandos. |
| Propietario |Todo |Todo |El rol de Propietario es un superusuario. Este rol puede administrar todo y tiene acceso completo a los datos. |
| Lector |Solo lectura |Regido por ACL |El rol Lector puede ver todo lo relacionado con la administración de cuentas, como qué usuario está asignado a qué rol. Pero no puede realizar ningún cambio. |
| Colaborador |Todos, excepto agregar y quitar roles |Regido por ACL |El rol Colaborador puede administrar algunos aspectos de una cuenta, como las implementaciones y la creación y administración de alertas. El rol Colaborador no puede agregar ni quitar roles. |
| Administrador de acceso de usuario |Agregar y quitar roles |Regido por ACL |El rol Administrador de acceso de usuarios puede administrar el acceso de los usuarios a las cuentas. |

Para ver instrucciones, consulte [Asignación de grupos de seguridad o usuarios a cuentas de Azure Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Uso de ACL para operaciones en sistemas de archivos
Data Lake Store es un sistema de archivos jerárquico como el sistema de archivos distribuido de Hadoop (HDFS) que admite [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controla los permisos de lectura (r), escritura (w) y ejecución (x) para los recursos del rol Propietario, para el grupo de propietarios y para otros usuarios y grupos. En Data Lake Store, las ACL se pueden habilitar en la carpeta raíz, en las subcarpetas y en los archivos individuales. Para más información sobre cómo funcionan las ACL en el contexto de Data Lake Store, consulte [Control de acceso en Data Lake Store](data-lake-store-access-control.md).

Se recomienda definir las ACL para varios usuarios mediante [grupos de seguridad](../active-directory/active-directory-groups-create-azure-portal.md). Agregue usuarios a un grupo de seguridad y asigne las ACL de los archivos o carpetas a ese grupo de seguridad. Esto es útil cuando desea proporcionar permisos asignados, porque está limitado a un máximo de 28 entradas de permisos asignados. Para más información acerca de cómo proteger los datos almacenados en Data Lake Store mediante grupos de seguridad de Azure Active Directory, consulte [Asignación de usuarios o grupos de seguridad como ACL al sistema de archivos de Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Lista de permisos de acceso](./media/data-lake-store-security-overview/adl.acl.2.png "Lista de permisos de acceso")

## <a name="network-isolation"></a>Aislamiento de red
Utilice Data Lake Store para ayudar a controlar el acceso a su almacén de datos en el nivel de red. Puede establecer firewalls y definir un intervalo de direcciones IP para los clientes de confianza. Con un intervalo de direcciones IP, solo los clientes que tengan una dirección IP que esté dentro del intervalo definido podrán conectarse a Data Lake Store.

![Configuración del firewall y acceso a IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Configuración del firewall y dirección IP")

## <a name="data-protection"></a>Protección de datos
Azure Data Lake Store protege los datos durante todo su ciclo de vida. Para los datos en tránsito, Data Lake Store utiliza el protocolo Seguridad de la capa de transporte estándar del sector (TLS) para proteger los datos de la red.

![Cifrado en Data Lake Store](./media/data-lake-store-security-overview/adls-encryption.png "Cifrado en Data Lake Store")

Data Lake Store también proporciona el cifrado de los datos que se almacenan en la cuenta. Puede elegir si cifrar o no los datos. Si se decide por el cifrado, los datos almacenados en Data Lake Store se cifrarán antes de almacenarlos en medios persistentes. En tal caso, Data Lake Store cifrará automáticamente los datos antes de la persistencia y los descifrará antes de la recuperación, por lo que resulta un proceso completamente transparente para el cliente que accede a los datos. No se requiere ningún cambio de código en el lado del cliente para cifrar o descifrar datos.

Para la administración de claves, Data Lake Store ofrece dos modos de administrar las claves de cifrado maestras (MEK) que son necesarias para descifrar los datos que se almacenan en Data Lake Store. Se puede dejar que Data Lake Store administre las MEK en su lugar o decidir conservar la propiedad de estas mediante su cuenta de Azure Key Vault. Puede especificar el modo de administración de claves durante la creación de una cuenta de Data Lake Store. Para más información sobre cómo proporcionar una configuración relacionada con el cifrado, consulte [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Registros de actividad y diagnóstico
Puede usar los registros de actividad o diagnóstico en función de si desea consultar registros de actividades relacionadas con la administración de cuentas o con los datos.

* Las actividades relacionadas con la administración de cuentas utilizan las API de Azure Resource Manager y se exponen en Azure Portal mediante los registros de actividad.
* Las actividades relacionadas con los datos usan las API de REST de WebHDFS y se exponen en el Portal de Azure mediante los registros de diagnóstico.

### <a name="activity-log"></a>Registro de actividades
Para cumplir las normativas, las organizaciones pueden requerir pistas de auditoría adecuadas de las actividades de administración de cuentas si necesitan profundizar en un incidente concreto. Data Lake Store cuenta con supervisión integrada y registra todas las actividades de administración de cuentas.

Para las trazas de auditoría de administración de cuentas, vea y elija las columnas que desea registrar. También puede exportar los registros de actividad a Azure Storage.

![Registro de actividad](./media/data-lake-store-security-overview/activity-logs.png "Registro de actividad")

Para obtener más información sobre cómo trabajar con los registros de actividad, consulte [Visualización de registros de actividad para auditar las acciones sobre los recursos](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Registros de diagnóstico
Puede habilitar el registro de auditoría y diagnóstico de acceso a los datos en Azure Portal, y enviar los registros a una cuenta de Azure Blob Storage, un centro de eventos o Log Analytics.

![Registros de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Registros de diagnóstico")

Para obtener más información sobre cómo trabajar con registros de diagnóstico con Azure Data Lake Store, consulte [Acceso a los registros de diagnóstico de Azure Data Lake Store](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumen
Los clientes empresariales demandan una plataforma en la nube de análisis de datos segura y fácil de usar. Azure Data Lake Store se ha diseñado para satisfacer estos requisitos con la administración de identidades y la autenticación mediante la integración de Azure Active Directory, la autorización basada en ALC, el aislamiento de red, el cifrado de datos en tránsito y en reposo, así como la auditoría.

Si desea ver las nuevas características incluidas en Data Lake Store, envíenos sus comentarios al [foro de Uservoice de Data Lake Store](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Otras referencias
* [Información general de Azure Data Lake Store](data-lake-store-overview.md)
* [Introducción a Data Lake Store](data-lake-store-get-started-portal.md)
* [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)

