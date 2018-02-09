---
title: "Activación de suscripciones y cuentas de Azure | Microsoft Docs"
description: Habilite el acceso mediante las API de Azure Resource Manager tanto en las cuentas nuevas como en las existentes y resuelva los problemas comunes de las cuentas.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/29/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: c2728545aeb202bdd216e00fd4a7e464e483b1da
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="activate-azure-subscriptions-and-accounts-with-azure-cost-management"></a>Activación de suscripciones y cuentas de Azure con Azure Cost Management

La adición o actualización de las credenciales de Azure Resource Manager permiten a Azure Cost Management detectar todas las cuentas y suscripciones dentro de su inquilino de Azure. Si también está habilitada la extensión de Azure Diagnostics en las máquinas virtuales, Azure Cost Management podrá recopilar más métricas, como la CPU y la memoria. En este artículo se describe cómo habilitar el acceso mediante las API de Azure Resource Manager tanto en las cuentas nuevas como en las existentes. También se describe cómo resolver los problemas comunes de las cuentas.

Cuando la suscripción a Azure _no está activada_, Azure Cost Management no puede acceder a la mayor parte de los datos de la misma. Para que Azure Cost Management pueda acceder a las cuentas _sin activar_ es preciso editarlas.

## <a name="required-azure-permissions"></a>Permisos de Azure necesarios

Para completar los procedimientos de este artículo se requieren determinados permisos. Usted o el Administrador de inquilinos deben tener los dos permisos siguientes:

- Permiso para registrar la aplicación CloudynCollector en su inquilino de Azure AD.
- La capacidad para asignar la aplicación a un rol en las suscripciones a Azure.

En las suscripciones a Azure, las cuentas deben tener acceso `Microsoft.Authorization/*/Write` para asignar la aplicación CloudynCollector. Esta acción se concede mediante el rol [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) o el rol [Administrador de acceso de usuario](../active-directory/role-based-access-built-in-roles.md#user-access-administrator).

Si a la cuenta se le ha asignado el rol **Colaborador**, no tiene los permisos adecuados para asignar la aplicación. Recibe un error al intentar asignar la aplicación CloudynCollector a su suscripción a Azure.

### <a name="check-azure-active-directory-permissions"></a>Comprobación de los permisos de Azure Active Directory

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En Azure Portal, seleccione **Azure Active Directory**.
3. En su instancia de Azure Active Directory, seleccione **Configuración de usuario**.
4. Compruebe la opción **Registros de aplicaciones**.
    - Si está seleccionado **Sí**, incluso los usuarios que no sean administradores pueden registrar aplicaciones de AD. Esta configuración significa que ningún usuario en el inquilino de Azure Active Directory puede registrar una aplicación. Puede pasar a Required Azure subscription permissions (Permisos requeridos en la suscripciones a Azure).  
    ![Registros de aplicaciones](./media/activate-subs-accounts/app-register.png)
    - Si en **Registros de aplicaciones** está seleccionada la opción **No**, los usuarios administradores de inquilinos serán los únicos que puedan registrar aplicaciones de Azure Active Directory. El Administrador de inquilinos debe registrar la aplicación CloudynCollector.


## <a name="add-an-account-or-update-a-subscription"></a>Adición de una cuenta o actualización de una suscripción

Cuando se agrega una cuenta o se actualiza una suscripción, se conceder a Azure Cost Management acceso a los datos de Azure.

### <a name="add-a-new-account-subscription"></a>Adición de una cuenta nueva (suscripción)

1. En el portal de Azure Cost Management, haga clic en el símbolo del engranaje de la esquina superior derecha y seleccione **Cloud Accounts** (Cuentas en la nube).
2. Haga clic en **Add new account** (Agregar nueva cuenta) y aparecerá el cuadro **Add new account** (Agregar nueva cuenta). Escriba la información necesaria.  
    ![Cuadro Add new account (Agregar nueva cuenta)](./media/activate-subs-accounts//add-new-account.png)

### <a name="update-a-subscription"></a>Actualización de una suscripción

1. Si desea actualizar una suscripción _no activada_ que ya existe en Azure Cost Management en Accounts Management (Administración de cuentas), haga clic en el símbolo del lápiz, que se encuentra a la derecha del _identificador único global del inquilino_.
    ![Volver a detectar las suscripciones](./media/activate-subs-accounts/existing-sub.png)
2. Si es necesario, escriba el identificador del inquilino. Si no conoce el identificador del inquilino, siga estos pasos para encontrarlo:
    1. Inicie sesión en [Azure Portal](https://portal.azure.com).
    2. En Azure Portal, seleccione **Azure Active Directory**.
    3. Para obtener el identificador de inquilino, seleccione **Propiedades** en el inquilino de Azure AD.
    4. Copie el GUID del identificador del directorio. Este valor es el id. de inquilino.
    Para más información, consulte [Obtención del identificador de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
3. Si es necesario, seleccione su identificador de tasa. Si no lo conoce, siga estos pasos para encontrarlo.
    1. En la parte superior derecha de Azure Portal, haga clic en la información de su usuario y, luego, en **Ver mi factura**.
    2. En **Cuenta de facturación**, haga clic en **Suscripciones**.
    3. En **Mis suscripciones**, seleccione la suscripción.
    4. El identificador de tasa se muestra debajo de **Id. de oferta**. Copie el id. de oferta de la suscripción.
4. En los cuadros Add new account (Agregar nueva cuenta) o Edit Subscription (Editar suscripción), haga clic en **Save** (Guardar), o en **Next** (Siguiente). Se le redirigirá a Azure Portal.
5. Inicie sesión en el portal. Haga clic en **Aceptar** para autorizar que Cloudyn Azure Collector acceda a su cuenta de Azure.

    Se le redirige a la página de administración de cuentas de Azure Cost Management, y el valor de Account Status (Estado de la cuenta) pasa a ser **active**. Debe mostrar una marca de verificación verde en la columna Resource Manager.

    Si no ve dicha marca en una o varias suscripciones, significa que no tiene permisos para crear ninguna aplicación de lectura (CloudynCollector) de la suscripción. Los usuarios con permisos superiores en la suscripción deben repetir este proceso.

En el vídeo [Connecting to Azure Resource Manager with Azure Cost Management by Cloudyn](https://youtu.be/oCIwvfBB6kk) (Conexión a Azure Resource Manager con Azure Cost Management de Cloudyn) se muestra todo este proceso.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Resolución de los problemas de configuración empresariales indirectos comunes

La primera vez que use el portal de Azure Cost Management, es posible que vea los siguientes mensajes si es usuario de Contrato Enterprise o de Proveedor de soluciones en la nube (CSP):

- *The specified API key is not a top level enrollment key (La clave de API especificada no es una clave de inscripción de nivel superior)* se muestra en el **Asistente para configuración de Azure Cost Management**.
- *Direct Enrollment – No* (Inscripción directa: no) se muestra en el portal de Contrato Enterprise.
- *No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account" (No se encontraron datos de uso de los últimos 30 días. Póngase en contacto con su distribuidor para asegurarse de que se habilitó el margen de beneficio para su cuenta de Azure)* se muestra en el portal de Azure Cost Management.

Los mensajes anteriores indican que adquirió un Contrato Enterprise de Azure a través de un revendedor o CSP. El CSP o el revendedor deben habilitar la _revisión_ de su cuenta de Azure, con el fin de que pueda ver sus datos en Azure Cost Management.

Aquí se muestra cómo corregir los problemas:

1. El revendedor debe habilitar el _margen de beneficio_ para su cuenta. Para ver las instrucciones, consulte la [guía de incorporación para clientes indirectos ](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide) (en inglés).
2. Genere la clave de Contrato Enterprise de Azure para usarla con Azure Cost Management. Para ver las instrucciones, consulte [Registrar un Contrato Enterprise de Azure y ver datos de costo](https://docs.microsoft.com/en-us/azure/cost-management/quick-register-ea).

Solo un administrador de servicios de Azure puede habilitar Cost Management. Los permisos de coadministrador son insuficientes.

Para poder generar la clave de API de Contrato Enterprise de Azure para configurar Azure Cost Management, debe habilitar la API de facturación de Azure siguiendo las instrucciones que aparecen en:

- [Introducción a las API de informes para clientes de Enterprise](../billing/billing-enterprise-api.md)
- [API de informes de Microsoft Azure Enterprise Portal](https://ea.azure.com/helpdocs/reportingAPI) en **Habilitación del acceso de datos a la API**

Es posible que también deba conceder permisos a administradores de departamento, propietarios de cuentas y administradores de empresa para _ver cargos_ con la API de facturación.

## <a name="next-steps"></a>pasos siguientes

- Si aún no ha completado el primer tutorial de Cost Management, léalo en [Revisión del uso y los costos](tutorial-review-usage.md).
