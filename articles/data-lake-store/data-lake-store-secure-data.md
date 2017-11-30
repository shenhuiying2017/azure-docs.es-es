---
title: "Protección de los datos almacenados en Azure Data Lake Store | Microsoft Docs"
description: "Aprenda a proteger los datos del almacén de Azure Data Lake mediante grupos y listas de control de acceso"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e246abaf8d1d7939765e1c878f6dfaf6375294f6
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protección de los datos almacenados en el Almacén de Azure Data Lake
Para proteger los datos en el Almacén de Azure Data Lake, se adopta un enfoque de tres pasos.

1. Comience creando grupos de seguridad en Azure Active Directory (AAD). Estos grupos de seguridad se usan para implementar el control de acceso basado en roles (RBAC) en Azure Portal. Para más información, vea [Control de acceso basado en roles en Microsoft Azure](../active-directory/role-based-access-control-configure.md).
2. Asigne los grupos de seguridad de AAD a la cuenta de Almacén de Azure Data Lake. Esto controla el acceso a la cuenta de Almacén de Data Lake desde el portal y las operaciones de administración desde el portal o las API.
3. Asigne los grupos de seguridad de AAD como listas de control de acceso (ACL) en el sistema de archivos del Almacén de Data Lake.
4. Además, también puede establecer un intervalo de direcciones IP para los clientes que pueden acceder a los datos en el Almacén de Data Lake.

En este artículo se proporcionan instrucciones sobre cómo usar el Portal de Azure para realizar las tareas anteriores. Para más información sobre cómo el almacén de Data Lake implementa la seguridad en el nivel de cuenta y datos, consulte [Security in Azure Data Lake Store](data-lake-store-security-overview.md)(Seguridad en el Almacén de Azure Data Lake). Para una información detallada acerca de cómo se implementan las ACL en Azure Data Lake Store, consulte [Información general de Access Control en Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Creación de grupos de seguridad en Azure Active Directory
Para obtener instrucciones sobre cómo crear grupos de seguridad de AAD y cómo agregar usuarios al grupo, consulte [Administración de grupos de seguridad en Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Puede agregar usuarios y otros grupos a un grupo de Azure AD mediante Azure Portal. Pero para agregar una entidad de servicio a un grupo, use el [módulo PowerShell de Azure AD](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Asignación de grupos de seguridad o usuarios a cuentas de Almacén de Azure Data Lake
Cuando asigna usuarios o grupos de seguridad a cuentas de Almacén de Azure Data Lake, controla el acceso a las operaciones de administración en la cuenta mediante el Portal de Azure y las API del Administrador de recursos de Azure. 

1. Abra una cuenta de Almacén de Azure Data Lake. En el panel izquierdo, haga clic en **Examinar** y en **Data Lake Store** y, después, en la hoja Data Lake Store, haga clic en el nombre de la cuenta a la que desee asignar un usuario o un grupo de seguridad.

2. En la hoja de configuración de su cuenta de Data Lake Store, haga clic en **Control de acceso (IAM)**. De forma predeterminada, en la hoja se indica el grupo **Administradores de suscripcione** como propietario.
   
    ![Asignar el grupo de seguridad a la cuenta de Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "Asignar el grupo de seguridad a la cuenta de Azure Data Lake Store")

    Existen dos maneras de agregar un grupo y asignar roles pertinentes.
   
    * Agregue un usuario o grupo a la cuenta y después asigne un rol o
    * Agregue un rol y después asigne usuarios o grupos al rol.
     
    En esta sección, nos centramos en el primer enfoque: agregar un grupo y después asignar roles. Puede realizar pasos similares para seleccionar primero un rol y después asignar grupos a ese rol.
4. En la hoja **Usuarios**, haga clic en **Agregar** para abrir la hoja **Agregar acceso**. En la hoja **Agregar acceso**, haga clic en **Seleccionar un rol** y después seleccione un rol para el usuario o grupo.
   
    ![Agregar un rol para el usuario](./media/data-lake-store-secure-data/adl.add.user.1.png "Agregar un rol para el usuario")
   
    Los roles **Propietario** y **Colaborador** proporcionan acceso a diversas funciones de administración en la cuenta de Data Lake. Para aquellos usuarios que interactúan con datos en Data Lake, pueden agregarlos al rol **Lector**. El ámbito de estos roles se limita a las operaciones de administración relacionadas con la cuenta de Almacén de Azure Data Lake.
   
    Para las operaciones de datos, los permisos individuales del sistema de archivos definen lo que los usuarios pueden hacer. Por lo tanto, un usuario con el rol Lector solamente ve la configuración administrativa asociada a la cuenta pero potencialmente puede leer y escribir datos en función de los permisos del sistema de archivos que tengan asignados. Los permisos del sistema de archivos del Almacén de Data Lake se describen en [Asignación de usuarios o grupos de seguridad como ACL al sistema de archivos del Almacén de Azure Data Lake](#filepermissions).
5. En la hoja **Agregar acceso**, haga clic en **Agregar usuarios** para abrir la hoja **Agregar usuarios**. En esta hoja, busque el grupo de seguridad que creó antes en Azure Active Directory. Si tiene muchos grupos en los que buscar, use el cuadro de texto en la parte superior para filtrar según el nombre del grupo. Haga clic en **Seleccionar**.
   
    ![Agregar un grupo de seguridad](./media/data-lake-store-secure-data/adl.add.user.2.png "Agregar un grupo de seguridad")
   
    Si desea agregar un grupo o usuario que no aparece, para invitarles, use el icono **Invitar** y especifique la dirección de correo electrónico para el usuario o grupo.
6. Haga clic en **Aceptar**. Debería ver el grupo de seguridad que se agregó como se muestra a continuación.
   
    ![Grupo de seguridad agregado](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de seguridad agregado")

7. Ahora el usuario o el grupo de seguridad tienen acceso a la cuenta de Almacén de Azure Data Lake. Si desea proporcionar acceso a usuarios específicos, puede agregarlos al grupo de seguridad. De forma similar, si desea revocar el acceso de un usuario, puede quitarle del grupo de seguridad. También puede asignar varios grupos de seguridad a una cuenta. 

## <a name="filepermissions"></a>Asignación de usuarios o grupos de seguridad como ACL al sistema de archivos del Almacén de Azure Data Lake
Al asignar usuarios o grupos de seguridad al sistema de archivos de Azure Data Lake, establece el control de acceso sobre los datos almacenados en el Almacén de Azure Data Lake.

1. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.
   
    ![Creación de directorios en una cuenta de Data Lake Store](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Creación de directorios en una cuenta de Data Lake Store")
2. En la hoja **Explorador de datos**, haga clic en el archivo o la carpeta para los que desea configurar la ACL y, después, haga clic en **Acceso**. Para asignar la ACL a un archivo, debe hacer clic en la opción **Acceso** de la hoja de **vista previa de archivos**.
   
    ![Configuración de ACL en el sistema de archivos de Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Configuración de ACL en el sistema de archivos de Data Lake")
3. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.
   
    ![Lista de accesos estándar y personalizados](./media/data-lake-store-secure-data/adl.acl.2.png "Lista de accesos estándar y personalizados")
   
   * **Acceso estándar** es el acceso de estilo UNIX, donde se especifican lectura, escritura y ejecución (rwx) para tres clases de usuario distintos: propietario, grupo y otros.
   * **Acceso personalizado** corresponde a las ACL de POSIX y permite establecer permisos para usuarios o grupos designados específicos y no solo para el propietario o el grupo del archivo. 
     
     Para obtener más información, consulte la página sobre las [ACL de HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para más información sobre cómo se implementan las ACL en Data Lake Store, consulte [Control de acceso en Data Lake Store](data-lake-store-access-control.md).
4. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y, después, en la hoja **Seleccionar usuario o grupo**, busque el grupo de seguridad que creó antes en Azure Active Directory. Si tiene muchos grupos en los que buscar, use el cuadro de texto en la parte superior para filtrar según el nombre del grupo. Haga clic en el grupo que desee agregar y después en **Seleccionar**.
   
    ![Incorporación de un grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Incorporación de un grupo")
5. Haga clic en **Seleccionar permisos**, seleccione los permisos y si desea asignarlos como una ACL predeterminada, ACL de acceso o ambos. Haga clic en **Aceptar**.
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-secure-data/adl.acl.4.png "Asignación de permisos a un grupo")
   
    Para obtener más información sobre los permisos de Data Lake Store y ACL y las ACL predeterminadas y de acceso, consulte [Control de acceso en Data Lake Store](data-lake-store-access-control.md).
6. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, se mostrará ahora en la hoja **Acceso** .
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-secure-data/adl.acl.5.png "Asignación de permisos a un grupo")
   
   > [!IMPORTANT]
   > En la versión actual, solo puede tener nueve entradas en **Acceso personalizado**. Si desea agregar más de 9 usuarios, debe crear grupos de seguridad, agregar usuarios a los grupos de seguridad y proporcionar acceso a esos grupos de seguridad para la cuenta de Almacén de Data Lake.
   > 
   > 
7. Si es necesario, también puede modificar los permisos de acceso después de agregar el grupo. Active o desactive la casilla para cada tipo de permiso (lectura, escritura, ejecución) en función de si desea quitarlo o asignarlo al grupo de seguridad. Haga clic en **Guardar** para guardar los cambios o en **Descartar** para deshacerlos.

## <a name="set-ip-address-range-for-data-access"></a>Configuración del intervalo de direcciones IP para el acceso a los datos
El Almacén de Azure Data Lake permite bloquear aún más el acceso a su almacén de datos en el nivel de red. Puede habilitar el firewall, especificar una dirección IP o definir un intervalo de direcciones IP para los clientes de confianza. Una vez habilitado, solo los clientes que tienen las direcciones IP dentro del intervalo definido pueden conectarse al almacén.

![Configuración del firewall y acceso a IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Configuración del firewall y dirección IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Quitar grupos de seguridad de una cuenta de Almacén de Azure Data Lake
Cuando quita usuarios o grupos de seguridad de cuentas de Azure Data Lake Store, solamente está cambiando el acceso a las operaciones de administración en la cuenta mediante Azure Portal y las API de Azure Resource Manager.

1. En la hoja de su cuenta de Data Lake Store, haga clic en **Configuración**. En la hoja **Configuración**, haga clic en **Usuarios**.
   
    ![Asignar el grupo de seguridad a la cuenta de Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Asignar el grupo de seguridad a la cuenta de Azure Data Lake")
2. En la hoja **Usuarios** , haga clic en el grupo de seguridad que desea quitar.
   
    ![Grupo de seguridad que se quitará](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de seguridad que se quitará")
3. En la hoja del grupo de seguridad, haga clic en **Quitar**.
   
    ![Grupo de seguridad quitado](./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de seguridad quitado")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Quitar las ACL de grupo de seguridad del sistema de archivos del Almacén de Azure Data Lake
Cuando quita las ACL de grupos de seguridad del sistema de archivos de Azure Data Lake Store, cambia el acceso a los datos de Data Lake Store.

1. En la hoja de su cuenta de Almacén de Data Lake, haga clic en **Explorador de datos**.
   
    ![Crear directorios en una cuenta de Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Crear directorios en una cuenta de Data Lake")
2. En la hoja **Explorador de datos**, haga clic en el archivo o la carpeta para los que desea quitar la ACL y, después, en la hoja de la cuenta, haga clic en el icono **Acceso**. Para quitar la ACL de un archivo, debe hacer clic en la opción **Acceso** de la hoja de **vista previa de archivos**.
   
    ![Configuración de ACL en el sistema de archivos de Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Configuración de ACL en el sistema de archivos de Data Lake")
3. En la hoja **Acceso**, en la sección **Acceso personalizado**, haga clic en el grupo de seguridad que desee quitar. En la hoja **Acceso personalizado**, haga clic en **Quitar** y después en **Aceptar**.
   
    ![Asignación de permisos a un grupo](./media/data-lake-store-secure-data/adl.remove.acl.png "Asignación de permisos a un grupo")

## <a name="see-also"></a>Consulte también
* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
* [Copiar datos de los blobs de Azure Storage en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introducción al Almacén de Azure Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
* [Introducción al Almacén de Azure Data Lake mediante .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Acceso a los registros de diagnóstico de Azure Data Lake Store](data-lake-store-diagnostic-logs.md)

