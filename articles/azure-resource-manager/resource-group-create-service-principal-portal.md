---
title: "Creación de la identidad de la aplicación de Azure en el portal | Microsoft Docs"
description: "Describe cómo crear una nueva aplicación de Active Directory y una entidad de servicio que puede utilizarse con el control de acceso basado en roles en el Administrador de recursos de Azure para administrar el acceso a los recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 3b132bbc89f64928f971f92365691d40c1aab420


---
# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
>
>

Si tiene una aplicación que necesita tener acceso a ciertos recursos o modificarlos, puede configurar una aplicación de Active Directory (AD) y asignarle los permisos requeridos. Este enfoque es preferible a ejecutar la aplicación con sus propias credenciales por los siguientes motivos:

* Puede asignar permisos a la identidad de aplicación que sean diferentes a los suyos propios. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
* No es necesario cambiar las credenciales de la aplicación si las responsabilidades cambian. 
* Puede usar un certificado para automatizar la autenticación al ejecutar un script desatendido.

En este tema se muestra cómo realizar tales pasos a través del portal. Se centra en una aplicación de un único inquilino donde la aplicación está diseñada para ejecutarse en una sola organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización.
 
## <a name="required-permissions"></a>Permisos necesarios
Para completar este tema, debe tener permisos suficientes para registrar una aplicación en su instancia de Active Directory y asignar la aplicación a un rol en su suscripción de Azure. Vamos a asegurarnos de que tiene los permisos adecuados para realizar esos pasos.

### <a name="check-active-directory-permissions"></a>Comprobación de los permisos de Active Directory
1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
2. Seleccione **Azure Active Directory**.

     ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. En su instancia de Active Directory, seleccione **Configuración de usuario**.

     ![seleccionar configuración de usuario](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. Compruebe la configuración de **App registrations** (Registros de aplicaciones). Si está establecida en **Sí**, los usuarios que no sean administradores pueden registrar aplicaciones de AD. Esta configuración significa que ningún usuario de Active Directory puede registrar una aplicación. Puede continuar con [Check Azure subscription permissions](#check-azure-subscription-permissions) (Comprobar permisos de suscripción de Azure).

     ![ver registros de aplicaciones](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. Si la configuración de registros de aplicaciones está establecida en **No**, solo los usuarios administradores pueden registrar aplicaciones. Debe comprobar si la cuenta es un administrador de Active Directory. Seleccione **Introducción** y **Buscar un usuario** en Quick tasks (Tareas rápidas).

     ![encontrar usuario](./media/resource-group-create-service-principal-portal/find-user.png)
6. Busque la cuenta y selecciónela cuando la encuentre.

     ![buscar usuario](./media/resource-group-create-service-principal-portal/show-user.png)
7. Para la cuenta, seleccione **Directory role** (Rol de directorio). 

     ![rol de directorio](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Vea el rol asignado para Active Directory. Si su cuenta está asignada al rol Usuario pero la configuración del registro de aplicaciones (de los pasos anteriores) está limitada a los usuarios administradores, pida a su administrador que le asigne un rol de administrador o que permita a los usuarios registrar aplicaciones.

     ![rol de vista](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Comprobación de los permisos de suscripción de Azure
En su suscripción de Azure, su cuenta debe tener acceso a `Microsoft.Authorization/*/Write` para asignar una aplicación de AD a un rol. Esta acción se concede mediante el rol [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) o el rol [Administrador de acceso de usuario](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si su cuenta está asignada al rol **Colaborador**, no tiene los permisos adecuados. Al intentar asignar la entidad de servicio a un rol, se muestra un error. 

Para comprobar los permisos de su suscripción:

1. Si los pasos anteriores no le han llevado a su cuenta de Active Directory, seleccione **Azure Active Directory** en el panel izquierdo.

2. Busque su cuenta de Active Directory. Seleccione **Introducción** y **Buscar un usuario** en Quick tasks (Tareas rápidas).

     ![encontrar usuario](./media/resource-group-create-service-principal-portal/find-user.png)
2. Busque la cuenta y selecciónela cuando la encuentre.

     ![buscar usuario](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. Seleccione **Azure resources** (Recursos de Azure).

     ![seleccionar recursos](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. Vea los roles asignados y determine si tiene los permisos adecuados para asignar una aplicación de AD a un rol. En caso contrario, pida al administrador de suscripciones que le agregue al rol Administrador de acceso de usuario. En la siguiente imagen, el usuario está asignado al rol Propietario en dos suscripciones, lo que significa que el usuario tiene los permisos adecuados. 

     ![mostrar permisos](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-active-directory-application"></a>Creación de una aplicación de Active Directory
1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
2. Seleccione **Azure Active Directory**.

     ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. Seleccione **App registrations** (Registros de aplicaciones).   

     ![seleccionar registros de aplicaciones](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. Seleccione **Agregar**.

     ![agregar aplicación](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. Proporcione un nombre y una dirección URL para la aplicación. Seleccione either **Web app / API** (Aplicación web/API) o **Nativa** para el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Crear**.

     ![aplicación de nombre](./media/resource-group-create-service-principal-portal/create-app.png)

Ha creado la aplicación.

## <a name="get-application-id-and-authentication-key"></a>Obtención del id. y la clave de autenticación de la aplicación
Al iniciar sesión mediante programación, necesitará el identificador de la aplicación y una clave de autenticación. Para obtener estos valores, use los pasos siguientes:

1. En **App registrations** (Registros de aplicaciones), en Active Directory, seleccione su aplicación.

     ![seleccionar aplicación](./media/resource-group-create-service-principal-portal/select-app.png)
2. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones de la sección de [aplicaciones de ejemplo](#sample-applications) hacen referencia a este valor como el id. de cliente.

     ![ID. DE CLIENTE](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. Para generar una clave de autenticación, seleccione **Claves**.

     ![seleccionar claves](./media/resource-group-create-service-principal-portal/select-keys.png)
4. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Guardar**.

     ![guardar clave](./media/resource-group-create-service-principal-portal/save-key.png)

     Después de guardar la clave, se muestra el valor de la clave. Copie este valor porque no podrá recuperarlo más adelante. Proporcione el valor de clave junto con el id. de aplicación para iniciar sesión con la aplicación. Guarde el valor de clave donde la aplicación pueda recuperarlo.

     ![clave guardada](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obtención del identificador de inquilino
Al iniciar sesión mediante programación, deberá pasar el id. de inquilino con la solicitud de autenticación. 

1. Para obtener el id. de inquilino, seleccione **Propiedades** en su instancia de Active Directory. 

     ![seleccionar propiedades de active directory](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. Copie el **id. de directorio**. Este valor es el id. de inquilino.

     ![id. de inquilino](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Asignación de aplicación a un rol
Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).

Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga.

1. Desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**. También puede seleccionar un grupo de recursos o un recurso.

     ![seleccionar suscripción](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. Seleccione la suscripción específica (grupo de recursos o recurso) a la que quiere asignar la aplicación.

     ![seleccionar suscripción para la asignación](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. Seleccione **Access Control (IAM)**.

     ![seleccionar acceso](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. Seleccione **Agregar**.

     ![seleccionar agregar](./media/resource-group-create-service-principal-portal/select-add.png)
6. Seleccione el rol que quiere asignar a la aplicación. En la imagen siguiente se muestra el rol **Lector**.

     ![seleccionar rol](./media/resource-group-create-service-principal-portal/select-role.png)

8. Busque la aplicación y selecciónela.

     ![buscar aplicación](./media/resource-group-create-service-principal-portal/search-app.png)
9. Seleccione **Aceptar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

## <a name="log-in-as-the-application"></a>Inicio de sesión con la aplicación

La aplicación está ahora configurada en Active Directory. Tiene un id. y una clave para usar en el inicio de sesión con la aplicación. A la aplicación se le asigna un rol que le proporciona determinadas acciones que puede realizar. 

Para iniciar sesión a través de PowerShell, consulte [Proporcione credenciales a través de PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell).

Para iniciar sesión a través de la CLI de Azure, consulte [Proporcionar credenciales a través de la CLI de Azure](resource-group-authenticate-service-principal-cli.md#provide-credentials-through-azure-cli).

Para obtener el token de acceso para operaciones REST, consulte [Creación de la solicitud](/rest/api/#create-the-request).

Examine las siguientes aplicaciones de ejemplo para aprender a iniciar sesión mediante el código de la aplicación.

### <a name="sample-applications"></a>Aplicaciones de ejemplo
Las aplicaciones de ejemplo siguientes muestran cómo iniciar con la aplicación de AD.

**.NET**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introducción a recursos - Implementación mediante la plantilla de Azure Resource Manager - en Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introducción a recursos - Administración de grupo de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Pasos siguientes
* Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para obtener información sobre cómo especificar directivas de seguridad, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).  




<!--HONumber=Jan17_HO4-->


