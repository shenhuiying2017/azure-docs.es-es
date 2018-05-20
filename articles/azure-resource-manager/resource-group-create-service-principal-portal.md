---
title: Creación de la identidad de la aplicación de Azure en el portal | Microsoft Docs
description: Describe cómo crear una nueva aplicación de Azure Active Directory y una entidad de servicio que puede utilizarse con el control de acceso basado en rol en Azure Resource Manager para administrar el acceso a los recursos.
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
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: e5d93963dddb4acb1147042ae338b32cb5d7646f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos

Si tiene código que necesita tener acceso a ciertos recursos o modificarlos, debe configurar una aplicación de Azure Active Directory (AD). Asigne los permisos necesarios a la aplicación de AD. Se prefiere este enfoque a la ejecución de la aplicación con credenciales propias, porque así se pueden asignar permisos a la identidad de la aplicación que difieran de los propios permisos. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.

En este artículo se muestra cómo realizar esos pasos en el portal. Se centra en una aplicación de un único inquilino donde la aplicación está diseñada para ejecutarse en una sola organización. Normalmente, utiliza aplicaciones de inquilino único para aplicaciones de línea de negocio que se ejecutan dentro de su organización.

> [!IMPORTANT]
> En lugar de crear una entidad de servicio, considere el uso de la identidad de servicio administrada de Azure AD para la identidad de la aplicación. La identidad de servicio administrada de Azure AD es una característica pública en versión preliminar de Azure Active Directory que simplifica la creación de una identidad para el código. Si el código se ejecuta en un servicio que admite la a identidad de servicio administrada de Azure AD y tiene acceso a recursos que admiten la autenticación de Azure Active Directory, la identidad de servicio administrada de Azure AD es una opción mejor para usted. Para obtener más información sobre la identidad de servicio administrada de Azure AD, incluidos los servicios que actualmente lo admiten, consulte [Managed Service Identity for Azure resources](../active-directory/managed-service-identity/overview.md) (Identidad de servicio administrada para recursos de Azure).

## <a name="required-permissions"></a>Permisos necesarios

Para completar este artículo, debe tener permisos suficientes para registrar una aplicación en su inquilino de Azure AD y asignar la aplicación a un rol en su suscripción de Azure. Vamos a asegurarnos de que tiene los permisos adecuados para realizar esos pasos.

### <a name="check-azure-active-directory-permissions"></a>Comprobación de los permisos de Azure Active Directory

1. Seleccione **Azure Active Directory**.

   ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. En su instancia de Azure Active Directory, seleccione **Configuración de usuario**.

   ![seleccionar configuración de usuario](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Compruebe la configuración de **App registrations** (Registros de aplicaciones). Si está establecida en **Sí**, los usuarios que no sean administradores pueden registrar aplicaciones de AD. Esta configuración significa que ningún usuario en el inquilino de Azure Active Directory puede registrar una aplicación. Puede continuar con [Check Azure subscription permissions](#check-azure-subscription-permissions) (Comprobar permisos de suscripción de Azure).

   ![ver registros de aplicaciones](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Si la opción Registros de aplicaciones está establecida en **No**, los [administradores globales](../active-directory/active-directory-assign-admin-roles-azure-portal.md) serán los únicos que podrán registrar aplicaciones. Compruebe si la cuenta es un administrador del inquilino de Active Directory. Seleccione **Información general** y eche un vistazo a la información del usuario. Si la cuenta está asignada al rol Usuario pero la opción Registros de aplicaciones (del paso anterior) está limitada a los usuarios administradores, pida al administrador que le asigne un rol de administrador global o que permita a los usuarios registrar las aplicaciones.

   ![encontrar usuario](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Comprobación de los permisos de suscripción de Azure

En su suscripción de Azure, su cuenta debe tener acceso a `Microsoft.Authorization/*/Write` para asignar una aplicación de AD a un rol. Esta acción se concede mediante el rol [Propietario](../role-based-access-control/built-in-roles.md#owner) o el rol [Administrador de acceso de usuario](../role-based-access-control/built-in-roles.md#user-access-administrator). Si su cuenta está asignada al rol **Colaborador**, no tiene los permisos adecuados. Al intentar asignar la entidad de servicio a un rol, se muestra un error.

Para comprobar los permisos de su suscripción:

1. Seleccione su cuenta en la esquina superior derecha y luego seleccione **Mis permisos**.

   ![Seleccionar permisos de usuario](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Seleccione una suscripción en la lista desplegable. Seleccione **Click here to view complete access details for this subscription** (Haga clic aquí para ver los detalles de acceso completos para esta suscripción).

   ![encontrar usuario](./media/resource-group-create-service-principal-portal/view-details.png)

1. Vea los roles asignados y determine si tiene los permisos adecuados para asignar una aplicación de AD a un rol. En caso contrario, pida al administrador de suscripciones que le agregue al rol Administrador de acceso de usuario. En la siguiente imagen, el usuario está asignado al rol Propietario, lo que significa que el usuario tiene los permisos adecuados.

   ![mostrar permisos](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Creación de una aplicación de Azure Active Directory

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
1. Seleccione **Azure Active Directory**.

   ![seleccionar azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Seleccione **App registrations** (Registros de aplicaciones).

   ![seleccionar registros de aplicaciones](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Seleccione **Nuevo registro de aplicaciones**.

   ![agregar aplicación](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Proporcione un nombre y una dirección URL para la aplicación. Seleccione **Aplicación web o API** para indicar el tipo de aplicación que desea crear. No se pueden crear credenciales para una aplicación [nativa](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); por consiguiente, ese tipo no funciona en una aplicación automatizada. Después de configurar los valores, seleccione **Crear**.

   ![aplicación de nombre](./media/resource-group-create-service-principal-portal/create-app.png)

Ha creado la aplicación.

## <a name="get-application-id-and-authentication-key"></a>Obtención del id. y la clave de autenticación de la aplicación

Al iniciar sesión mediante programación, necesitará el identificador de la aplicación y una clave de autenticación. Para obtener estos valores, use los pasos siguientes:

1. En **Registros de aplicaciones**, en Azure Active Directory, seleccione su aplicación.

   ![seleccionar aplicación](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. En algunas [aplicaciones de ejemplo](#log-in-as-the-application), este valor se conoce como identificador de cliente.

   ![Identificador de cliente](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Para generar una clave de autenticación, seleccione **Configuración**.

   ![Seleccionar Configuración](./media/resource-group-create-service-principal-portal/select-settings.png)

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

Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](../role-based-access-control/built-in-roles.md).

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

1. De manera predeterminada, las aplicaciones de Azure Active Directory no se muestran en las opciones disponibles. Para buscar la aplicación, debe proporcionar su nombre en el campo de búsqueda. Selecciónelo.

   ![buscar aplicación](./media/resource-group-create-service-principal-portal/search-app.png)

1. Haga clic en **Guardar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

## <a name="next-steps"></a>Pasos siguientes
* Para configurar una aplicación multiinquilino, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para obtener información sobre cómo especificar directivas de seguridad, consulte [Control de acceso basado en roles de Azure](../role-based-access-control/role-assignments-portal.md).  
* Para obtener una lista de las acciones que puede conceder o denegar a los usuarios, consulte [Operaciones del proveedor de recursos de Azure Resource Manager](../role-based-access-control/resource-provider-operations.md).
