---
title: "Creación de la identidad de la aplicación de Azure en el portal | Microsoft Docs"
description: "Describe cómo crear una nueva aplicación de Azure Active Directory y una entidad de servicio que puede utilizarse con el control de acceso basado en rol en Azure Resource Manager para administrar el acceso a los recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 56b73ff30e7fdaa3c21bc1e5528e2f6118597ef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos

Si tiene una aplicación que necesita tener acceso a ciertos recursos o modificarlos, puede configurar una aplicación de Azure Active Directory (AD) y asignarle los permisos requeridos. Este enfoque es preferible a ejecutar la aplicación con sus propias credenciales por los siguientes motivos:

* Puede asignar permisos a la identidad de aplicación que sean diferentes a los suyos propios. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
* No es necesario cambiar las credenciales de la aplicación si las responsabilidades cambian. 
* Puede usar un certificado para automatizar la autenticación al ejecutar un script desatendido.

En este tema se muestra cómo realizar tales pasos a través del portal. Se centra en una aplicación de un único inquilino donde la aplicación está diseñada para ejecutarse en una sola organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización.

## <a name="required-permissions"></a>Permisos necesarios

Para completar este tema, debe tener permisos suficientes para registrar una aplicación en su inquilino de Azure AD y asignar la aplicación a un rol en su suscripción de Azure. Vamos a asegurarnos de que tiene los permisos adecuados para realizar esos pasos.

### <a name="check-azure-active-directory-permissions"></a>Comprobación de los permisos de Azure Active Directory

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).

1. Seleccione **Azure Active Directory**.

   ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. En su instancia de Azure Active Directory, seleccione **Configuración de usuario**.

   ![seleccionar configuración de usuario](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Compruebe la configuración de **App registrations** (Registros de aplicaciones). Si está establecida en **Sí**, los usuarios que no sean administradores pueden registrar aplicaciones de AD. Esta configuración significa que ningún usuario en el inquilino de Azure Active Directory puede registrar una aplicación. Puede continuar con [Check Azure subscription permissions](#check-azure-subscription-permissions) (Comprobar permisos de suscripción de Azure).

   ![ver registros de aplicaciones](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Si la configuración de registros de aplicaciones está establecida en **No**, solo los usuarios administradores pueden registrar aplicaciones. Compruebe si la cuenta es un administrador del inquilino de Active Directory. Seleccione **Introducción** y **Buscar un usuario** en Quick tasks (Tareas rápidas).

   ![encontrar usuario](./media/resource-group-create-service-principal-portal/find-user.png)

1. Busque la cuenta y selecciónela cuando la encuentre.

   ![buscar usuario](./media/resource-group-create-service-principal-portal/show-user.png)

1. Para la cuenta, seleccione **Directory role** (Rol de directorio).

   ![rol de directorio](./media/resource-group-create-service-principal-portal/select-directory-role.png)

1. Vea el rol de directorio asignado en Azure AD. Si su cuenta está asignada al rol Usuario pero la configuración del registro de aplicaciones (de los pasos anteriores) está limitada a los usuarios administradores, pida a su administrador que le asigne un rol de administrador o que permita a los usuarios registrar aplicaciones.

   ![rol de vista](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Comprobación de los permisos de suscripción de Azure

En su suscripción de Azure, su cuenta debe tener acceso a `Microsoft.Authorization/*/Write` para asignar una aplicación de AD a un rol. Esta acción se concede mediante el rol [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) o el rol [Administrador de acceso de usuario](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Si su cuenta está asignada al rol **Colaborador**, no tiene los permisos adecuados. Al intentar asignar la entidad de servicio a un rol, se muestra un error.

Para comprobar los permisos de su suscripción:

1. Si los pasos anteriores no le han llevado a su cuenta de Azure AD, seleccione **Azure Active Directory** en el panel izquierdo.

1. Encuentre la cuenta de Azure AD. Seleccione **Introducción** y **Buscar un usuario** en Quick tasks (Tareas rápidas).

   ![encontrar usuario](./media/resource-group-create-service-principal-portal/find-user.png)

1. Busque la cuenta y selecciónela cuando la encuentre.

   ![buscar usuario](./media/resource-group-create-service-principal-portal/show-user.png)

1. Seleccione **Azure resources** (Recursos de Azure).

   ![seleccionar recursos](./media/resource-group-create-service-principal-portal/select-azure-resources.png)

1. Vea los roles asignados y determine si tiene los permisos adecuados para asignar una aplicación de AD a un rol. En caso contrario, pida al administrador de suscripciones que le agregue al rol Administrador de acceso de usuario. En la siguiente imagen, el usuario está asignado al rol Propietario en dos suscripciones, lo que significa que el usuario tiene los permisos adecuados.

   ![mostrar permisos](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-azure-active-directory-application"></a>Creación de una aplicación de Azure Active Directory

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
1. Seleccione **Azure Active Directory**.

   ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Seleccione **App registrations** (Registros de aplicaciones).

   ![seleccionar registros de aplicaciones](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Seleccione **Nuevo registro de aplicaciones**.

   ![agregar aplicación](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Proporcione un nombre y una dirección URL para la aplicación. Seleccione either **Web app / API** (Aplicación web/API) o **Nativa** para el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Crear**.

   ![aplicación de nombre](./media/resource-group-create-service-principal-portal/create-app.png)

Ha creado la aplicación.

## <a name="get-application-id-and-authentication-key"></a>Obtención del id. y la clave de autenticación de la aplicación

Al iniciar sesión mediante programación, necesitará el identificador de la aplicación y una clave de autenticación. Para obtener estos valores, use los pasos siguientes:

1. En **Registros de aplicaciones**, en Azure Active Directory, seleccione su aplicación.

   ![seleccionar aplicación](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones de la sección de [aplicaciones de ejemplo](#sample-applications) hacen referencia a este valor como el identificador de cliente.

   ![Identificador de cliente](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Para generar una clave de autenticación, seleccione **Claves**.

   ![seleccionar claves](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Guardar**.

   ![guardar clave](./media/resource-group-create-service-principal-portal/save-key.png)

   Después de guardar la clave, se muestra el valor de la clave. Copie este valor porque no podrá recuperarlo más adelante. Proporcione el valor de clave junto con el id. de aplicación para iniciar sesión con la aplicación. Guarde el valor de clave donde la aplicación pueda recuperarlo.

   ![clave guardada](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Obtención del identificador de inquilino

Al iniciar sesión mediante programación, deberá pasar el id. de inquilino con la solicitud de autenticación.

1. Seleccione **Azure Active Directory**.

   ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Para obtener el identificador de inquilino, seleccione **Propiedades** en el inquilino de Azure AD.

   ![Selección de las propiedades de Azure AD](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Copie el **id. de directorio**. Este valor es el id. de inquilino.

   ![tenant ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Asignación de aplicación a un rol

Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).

Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga.

1. Desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**. También puede seleccionar un grupo de recursos o un recurso.

   ![seleccionar suscripción](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Seleccione la suscripción específica (grupo de recursos o recurso) a la que quiere asignar la aplicación.

   ![seleccionar suscripción para la asignación](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Seleccione **Access Control (IAM)**.

   ![seleccionar acceso](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Seleccione **Agregar**.

   ![seleccionar agregar](./media/resource-group-create-service-principal-portal/select-add.png)

1. Seleccione el rol que quiere asignar a la aplicación. En la imagen siguiente se muestra el rol **Lector**.

   ![seleccionar rol](./media/resource-group-create-service-principal-portal/select-role.png)

1. Busque la aplicación y selecciónela.

   ![buscar aplicación](./media/resource-group-create-service-principal-portal/search-app.png)

1. Haga clic en **Guardar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

## <a name="log-in-as-the-application"></a>Inicio de sesión con la aplicación

La aplicación está ahora configurada en Azure Active Directory. Tiene un id. y una clave para usar en el inicio de sesión con la aplicación. A la aplicación se le asigna un rol que le proporciona determinadas acciones que puede realizar. Para información sobre el inicio de sesión como en la aplicación a través de distintas plataformas, consulte:

* [PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)
* [CLI de Azure](resource-group-authenticate-service-principal-cli.md#provide-credentials-through-azure-cli)
* [REST](/rest/api/#create-the-request)
* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Pasos siguientes
* Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para obtener información sobre cómo especificar directivas de seguridad, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).  
* Para obtener una lista de las acciones que puede conceder o denegar a los usuarios, consulte [Operaciones del proveedor de recursos de Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).
