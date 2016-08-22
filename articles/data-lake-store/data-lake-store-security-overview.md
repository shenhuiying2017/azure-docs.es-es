<properties
   pageTitle="Información general sobre seguridad en Almacén de Data Lake | Microsoft Azure"
   description="Descripción de cómo Azure Data Lake Store es un almacén de macrodatos más seguro"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Seguridad en el Almacén de Azure Data Lake

Muchas empresas están aprovechando el análisis de macrodatos para obtener conocimientos empresariales que les ayuden a tomar decisiones inteligentes. Es posible que una organización tenga un entorno regulado y complejo, con un número cada vez mayor de distintos tipos de usuarios. Es esencial para una empresa asegurarse de que los datos empresariales más importantes se almacenaron de la forma más segura, y que se concedió a los usuarios individuales el nivel adecuado de acceso. Azure Data Lake Store está diseñado para ayudar a cumplir estos requisitos de seguridad. En este artículo aprenderá las funcionalidades de seguridad de Data Lake Store, como:

* Autenticación
* Autorización
* Aislamiento de red
* Protección de datos
* Auditoría

## Autenticación y administración de identidades

La autenticación es el proceso por el que se comprueba una identidad de usuario cuando el usuario interactúa con Data Lake Store o con cualquier servicio que se conecte al mismo. Para la autenticación y administración de identidades, Data Lake Store usa [Azure Active Directory](../active-directory/active-directory-whatis.md), una completa solución en la nube de administración de identidades y acceso que simplifica la administración de usuarios y grupos.

Cada una de las suscripciones de Azure puede asociarse a una instancia de Azure Active Directory. Solo los usuarios y las identidades de servicio definidas en el servicio de Azure Active Directory pueden tener acceso a la cuenta de Data Lake Store mediante el Portal de Azure, las herramientas de línea de comandos, o mediante aplicaciones cliente que la organización compila con el SDK de Azure Data Lake Store. Las principales ventajas del uso de Azure Active Directory como un mecanismo de control de acceso centralizado son:

* Administración simplificada del ciclo de vida de las identidades. La identidad de un usuario o un servicio (una identidad principal del servicio) se puede crear y revocar rápidamente; basta con eliminar o deshabilitar la cuenta en el directorio.

* Multi-factor authentication. [Multi-factor authentication](../multi-factor-authentication/multi-factor-authentication.md) proporciona un nivel de seguridad adicional para los inicios de sesión y las transacciones del usuario.

* Autenticación desde cualquier cliente mediante un protocolo estándar abierto, como OAuth u OpenID.

* Federación con los servicios de directorio de empresa y los proveedores de identidades en la nube.

## Control de autorización y acceso

Una vez que un usuario se autentica mediante Azure Active Directory para acceder a Azure Data Lake Store, la autorización controla los permisos de acceso a Data Lake Store. Data Lake Store separa la autorización para las actividades relacionadas con cuentas y las relacionadas con datos de la siguiente manera:

* [Control de acceso basado en rol](../active-directory/role-based-access-control-what-is.md) (RBAC) proporcionado por Azure para la administración de cuentas
* ACL de POSIX para el acceso a datos en el almacén


### RBAC para la administración de cuentas

Se definen cuatro roles básicos para Data Lake Store de forma predeterminada. Estos roles permiten diferentes operaciones en una cuenta de Data Lake Store mediante el Portal de Azure, los cmdlets de PowerShell y las API de REST. Los roles Propietario y Colaborador pueden realizar diversas funciones de administración en la cuenta. Puede asignar el rol Lector a los usuarios que solo interactúan con datos.

![Funciones RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Funciones RBAC")

Tenga en cuenta que aunque se asignen roles para la administración de cuentas, algunos roles afectarán al acceso a los datos. Para las operaciones que el usuario puede realizar en el sistema de archivos, deberá utilizar listas de control de acceso (ACL). La tabla siguiente muestra un resumen de los derechos de administración y los derechos de acceso a datos para los roles predeterminados.

| Roles | Derechos de administración | Derechos de acceso a datos | Explicación |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Ningún rol asignado | None | Regido por ACL | El usuario no puede utilizar el Portal de Azure ni los cmdlets de Azure PowerShell para explorar Data Lake Store. El usuario solo puede utilizar las herramientas de línea de comandos.
| Propietario | Todo | Todo | El rol de Propietario es un superusuario. Este rol puede administrar todo y tiene acceso completo a los datos.
| Lector | Solo lectura | Regido por ACL | El rol Lector puede ver todo lo relacionado con la administración de cuentas, como qué usuario está asignado a qué rol. Pero no puede realizar ningún cambio. |
| Colaborador | Todos, excepto agregar y quitar roles | Regido por ACL | El rol Colaborador puede administrar algunos aspectos de una cuenta, como las implementaciones y la creación y administración de alertas. El rol Colaborador no puede agregar ni quitar roles.
| Administrador de acceso de usuario | Agregar y quitar roles | Regido por ACL | El rol Administrador de acceso de usuarios puede administrar el acceso de los usuarios a las cuentas. |

Para ver instrucciones, consulte [Asignación de grupos de seguridad o usuarios a cuentas de Azure Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### Uso de ACL para operaciones en sistemas de archivos

Data Lake Store es un sistema de archivos jerárquico como el sistema de archivos distribuido de Hadoop (HDFS) que admite [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controla los permisos de lectura (r), escritura (w) y ejecución (x) para los recursos del rol Propietario, para el grupo de propietarios y para otros usuarios y grupos. En la versión preliminar pública de Data Lake Store (la versión actual), las ACL se habilitan en la carpeta raíz, en las subcarpetas y en los archivos individuales. Las ACL que se aplican a la carpeta raíz también se aplican a todos los archivos y carpetas secundarios.

Se recomienda definir las ACL para varios usuarios mediante [grupos de seguridad](../active-directory/active-directory-accessmanagement-manage-groups.md). Agregue usuarios a un grupo de seguridad y asigne las ACL de los archivos o carpetas a ese grupo de seguridad. Esto es útil cuando desea proporcionar acceso personalizado, porque está limitado a la adición de un máximo de nueve entradas de acceso personalizado. Para más información acerca de cómo proteger los datos almacenados en Data Lake Store mediante grupos de seguridad de Azure Active Directory, consulte [Asignación de usuarios o grupos de seguridad como ACL al sistema de archivos de Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Mostrar acceso estándar y personalizado](./media/data-lake-store-security-overview/adl.acl.2.png "Mostrar acceso estándar y personalizado")

## Aislamiento de red

Utilice Data Lake Store para ayudar a controlar el acceso a su almacén de datos en el nivel de red. Puede establecer firewalls y definir un intervalo de direcciones IP para los clientes de confianza. Con un intervalo de direcciones IP, solo los clientes que tengan una dirección IP que esté dentro del intervalo definido podrán conectarse a Data Lake Store.

![Configuración del firewall y el acceso a IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Configuración del firewall y la dirección IP")

## Protección de datos

Las organizaciones desean garantizar que los datos críticos de negocio estén protegidos a lo largo de su ciclo de vida. Para los datos en tránsito, Data Lake Store utiliza el protocolo Seguridad de la capa de transporte estándar del sector (TLS) para proteger los datos que se mueven entre el cliente y Data Lake Store.

En futuras versiones estará disponible la protección de datos para datos en reposo.

## Registros de auditoría y diagnóstico

Puede usar los registros de auditoría o diagnóstico en función de si desea consultar registros de actividades relacionadas con la administración o con los datos.

*  Las actividades relacionadas con la administración utilizan las API de Azure Resource Manager y se exponen en el Portal de Azure mediante los registros de auditoría.
*  Las actividades relacionadas con los datos usan las API de REST de WebHDFS y se exponen en el Portal de Azure mediante los registros de diagnóstico.

### Registros de auditoría

Para cumplir las normativas, las organizaciones pueden requerir trazas de auditoría adecuadas si necesitan profundizar en un incidente concreto. Data Lake Store integra la auditoría y la supervisión y registra todas las actividades de administración de cuentas.

Para las trazas de auditoría de administración de cuentas, vea y elija las columnas que desea registrar. También puede exportar los registros de auditoría al Almacenamiento de Azure.

![Registros de auditoría](./media/data-lake-store-security-overview/audit-logs.png "Registros de auditoría")

### Registros de diagnóstico

Puede establecer las trazas de auditoría de acceso a datos desde el Portal de Azure (en Configuración de diagnóstico) y crear una cuenta de Almacenamiento de blobs de Azure donde se almacenarán los registros.

![Registros de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Registros de diagnóstico")

Después de configurar las opciones de diagnóstico, puede ver los registros en la pestaña **Registros de diagnóstico**.

Para más información sobre cómo trabajar con registros de diagnóstico con Azure Data Lake Store, consulte [Acceso a los registros de diagnóstico de Azure Data Lake](data-lake-store-diagnostic-logs.md).

## Resumen

Los clientes empresariales demandan una plataforma en la nube de análisis de datos segura y fácil de usar. Azure Data Lake Store se ha diseñado para satisfacer estos requisitos con la administración de identidades y la autenticación mediante la integración de Azure Active Directory, la autorización basada en ALC, el aislamiento de red, el cifrado de datos en tránsito y en reposo (en el futuro), así como la auditoría.

Si desea ver las nuevas características incluidas en Data Lake Store, envíenos sus comentarios al [foro de Uservoice de Data Lake Store](https://feedback.azure.com/forums/327234-data-lake).

## Otras referencias

- [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
- [Introducción a Data Lake Store](data-lake-store-get-started-portal.md)
- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0810_2016-->