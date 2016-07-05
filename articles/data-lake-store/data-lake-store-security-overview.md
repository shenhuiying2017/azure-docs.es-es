<properties 
   pageTitle="Información general sobre seguridad en Almacén de Data Lake | Microsoft Azure" 
   description="Descripción de cómo el Almacén de Azure Data Lake es un almacén de macrodatos seguro" 
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
   ms.date="06/22/2016"
   ms.author="nitinme"/>

# Seguridad en el Almacén de Azure Data Lake

Un gran número de empresas está aprovechando el análisis de macrodatos para obtener puntos de vista empresariales para tomar decisiones inteligentes. Estas organizaciones pueden tener un entorno regulado y complejo, con un número cada vez mayor de distintos tipos de usuarios. Por lo tanto, es vital para las empresas asegurarse de que los datos críticos del negocio se almacenan de forma segura con el nivel adecuado de acceso concedido a los usuarios apropiados. El Almacén de Azure Data Lake está diseñado teniendo en cuenta estos requisitos de seguridad. En este artículo aprenderá las funcionalidades de seguridad del Almacén de Azure Data Lake (ADLS), como:

* Autenticación
* Autorización
* Aislamiento de red
* Protección de datos
* Auditoría

 
## Autenticación y administración de identidades

La autenticación es el proceso mediante el cual los usuarios demuestran su identidad al interactuar con el Almacén de Data Lake o cualquier servicio que se conecta a dicho almacén. Para la autenticación y administración de identidades, el Almacén de Data Lake usa [Azure Active Directory](../active-directory/active-directory-whatis.md) (AAD), una completa solución en la nube de administración de identidades y acceso que simplifica la administración de usuarios y grupos.

En la actualidad, cada una de las suscripciones de Azure puede asociarse a una instancia de Azure Active Directory. Solo las identidades de servicio y usuarios definidas en este directorio pueden tener acceso a su Almacén de Data Lake mediante el Portal de Azure, herramientas de la línea de comandos o aplicaciones cliente compiladas con el SDK del Almacén de Data Lake. Las principales ventajas del uso de Azure Active Directory como un mecanismo de control de acceso centralizado son:

* Simplifica la administración del ciclo de vida de las identidades. La identidad de un usuario o un servicio (identidad principal del servicio) se puede crear y revocar rápidamente; basta con eliminar o deshabilitar la cuenta en el directorio.

* Admite la [autenticación multifactor](../multi-factor-authentication/multi-factor-authentication.md), que proporciona un nivel de seguridad adicional para los inicios de sesión y las transacciones del usuario.

* Permite a los usuarios autenticarse desde cualquier cliente mediante un protocolo abierto estándar, como OAuth y OpenID.

* Permite la federación con los servicios de directorio de empresa y los proveedores de identidades en la nube.

## Control de autorización y acceso

Una vez que un usuario se autentica mediante AAD para acceder al Almacén de Azure Data Lake, la autorización controla los permisos de acceso al Almacén de Data Lake. El Almacén de Data Lake separa la autorización para las actividades relacionadas con los datos y la cuenta de la siguiente manera: administración de cuenta a partir de administración de datos [control de acceso basado en roles](../active-directory/role-based access control-what-is.md) (RBAC) proporcionado por Azure para la administración de cuentas; es compatible con ACL de POSIX para acceder a los datos del almacén.

### Uso de RBAC para la administración de cuentas

Hay cuatro roles básicos definidos de forma predeterminada. Dichos roles permiten diferentes operaciones en una cuenta del Almacén de Data Lake mediante el portal, los cmdlets de PowerShell y las API de REST. Los roles **Propietario** y **Colaborador** permiten diversas funciones de administración en la cuenta. Para los usuarios que solo interactúan con datos, puede agregarlos al rol **Lector**.

![Funciones RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Funciones RBAC")

Tenga en cuenta que si bien el objetivo de la asignación de estas funciones es la administración de cuentas, también puede tener implicaciones en los permisos de acceso a los datos en función de un rol específico. Para las operaciones que el usuario puede realizar en el sistema de archivos, deberá utilizar listas de control de acceso (ACL). A continuación se muestra un resumen de los derechos de administración y los derechos de acceso a datos para estos roles.

| Roles | Derechos de administración | Derechos de acceso a datos | Explicación |
|--------------------------|---------------------------------|--------------------|-------------|
| Ningún rol asignado | None | Regido por ACL | En tales casos, los usuarios no pueden utilizar el Portal de Azure o los cmdlets de Azure PowerShell para consultar el Almacén de Data Lake. Dichos usuarios solo pueden usar las herramientas de línea de comandos. |
| Propietario | Todo | Todo | El propietario es un superusuario, por tanto el rol Propietario permite administrar todo y tiene acceso total a los datos | 
| Lector | Solo lectura | Regido por ACL | El lector puede ver todo lo relacionado con la administración de cuentas, como qué usuario está asignado a qué rol, pero no puede realizar ningún cambio. |
| Colaborador | Todos, excepto agregar y quitar roles | Regido por ACL | El colaborador puede administrar otros aspectos de una cuenta, como crear y administrar alertas, realizar implementaciones, etc. Un colaborador no puede agregar ni quitar roles. |
| Administrador de acceso de usuarios | Agregar y quitar roles | Regido por ACL | El administrador de acceso de usuarios puede administrar el acceso de los usuarios a las cuentas. |

Para ver instrucciones, consulte [Asignación de grupos de seguridad o usuarios a cuentas de Almacén de Azure Data Lake](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### Uso de ACL para operaciones en sistemas de archivos

El Almacén de Azure Data Lake es un sistema de archivos jerárquico, como HDFS, que admite [ACL de POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists); permite derechos de acceso de lectura (r), escritura (w) y ejecución (x) a recursos concedidos al propietario, el grupo propietario y otros grupos y usuarios. En la versión preliminar pública del Almacén de Data Lake (versión actual), las ACL solo están habilitadas en la carpeta raíz, lo que significa que las ACL que se aplican a la carpeta raíz también son aplicables también a todos los archivos y carpetas secundarias. En las versiones futuras, podrá establecer las ACL en cualquier archivo o carpeta.

Es un procedimiento recomendado para definir la ACL para muchos usuarios mediante los [grupos de seguridad](../active-directory/active-directory-accessmanagement-manage-groups.md). Agrupe los usuarios en un grupo de seguridad y asigne las ACL de los archivos y carpetas a ese grupo de seguridad. Esto es útil al proporcionar un acceso personalizado, ya que hay un límite que solo permite agregar un máximo de nueve entradas como parte del acceso personalizado. Consulte [Asignación de grupos de seguridad o usuarios a cuentas de Almacén de Azure Data Lake](data-lake-store-secure-data.md#filepermissions) para más información acerca de cómo proteger los datos almacenados en el Almacén de Data Lake mediante grupos de seguridad de AAD.

![Mostrar acceso estándar y personalizado](./media/data-lake-store-security-overview/adl.acl.2.png "Mostrar acceso estándar y personalizado")

## Aislamiento de red

El Almacén de Azure Data Lake permite bloquear aún más el acceso a su almacén de datos en el nivel de red. Puede habilitar el firewall y definir un intervalo de direcciones IP para los clientes de confianza. Una vez habilitado, solo los clientes que tienen las direcciones IP dentro del intervalo definido pueden conectarse al almacén.

![Configuración del firewall y el acceso a IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Configuración del firewall y la dirección IP")

## Protección de datos

Las organizaciones desean garantizar que sus datos críticos del negocio estén protegidos a lo largo de su ciclo de vida. Para los datos en tránsito, el Almacén de Data Lake utiliza el TLS (protocolo Seguridad de la capa de transporte) estándar del sector para proteger los datos entre el cliente y la seguridad de Data Lake.

En futuras versiones también estará disponible la protección de datos para datos en reposo.

## Registros de auditoría y diagnóstico

Puede usar los registros de auditoría o diagnóstico en función de si desea consultar registros de actividades relacionadas con la administración o con los datos.

*  Las actividades relacionadas con la administración utilizan las API de Azure Resource Manager y se exponen en el Portal de Azure mediante los registros de auditoría.
*  Las actividades relacionadas con los datos usa las API de WebHDFS y se exponen en el Portal de Azure mediante los registros de diagnóstico.

### Registros de auditoría

Para cumplir las normativas, las organizaciones pueden requerir trazas de auditoría adecuados si necesitan profundizar en un incidente. El Almacén de Data Lake integra la auditoría y la supervisión que registran todas las actividades de administración de cuentas.

En el caso de las trazas de auditoría para la administración de cuentas, puede ver y elegir las columnas que desea registrar, así como exportar los registros de auditoría a Almacenamiento de Azure.

![Registros de auditoría](./media/data-lake-store-security-overview/audit-logs.png "Registros de auditoría")

### Registros de diagnóstico

Puede habilitar las trazas de auditoría de acceso a datos desde el Portal de Azure (**Configuración de diagnóstico**) y proporcionar una cuenta de Almacenamiento de blobs de Azure donde se almacenarán los registros.

![Registros de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Registros de diagnóstico")

Una vez habilitada la configuración de diagnóstico, puede ver los registros en la pestaña **Registros de diagnóstico**.

## Resumen

Los clientes empresariales demandan una plataforma en la nube de análisis de datos segura y fácil de usar. El Almacén de Azure Data Lake se ha diseñado para satisfacer estos requisitos con la administración de identidades y la autenticación mediante la integración de Azure Active Directory, la autorización basada en ALC, el aislamiento de red, el cifrado de datos en tránsito y en reposo (en el futuro), así como la auditoría.

Si desea ver las nuevas características incluidas en el Almacén de Data Lake, envíenos sus comentarios al [foro de Uservoice](https://feedback.azure.com/forums/327234-data-lake).

## Consulte también

- [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
- [Introducción al Almacén de Data Lake](data-lake-store-get-started-portal.md)
- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0622_2016-->