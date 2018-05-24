---
title: Creación de una entidad de servicio de Azure Stack | Microsoft Docs
description: Describe cómo crear una entidad de servicio que puede usarse con el control de acceso basado en roles en Azure Resource Manager para administrar el acceso a los recursos.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: mabrigg
ms.openlocfilehash: de5712fd7b48a759b366f5b9808bbbefc6e305cd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Otorgue a las aplicaciones acceso a los recursos de Azure Stack mediante la creación de entidades de servicio

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede permitir que una aplicación tenga acceso a los recursos de Azure Stack mediante la creación de un servicio principal que use Azure Resource Manager. Una entidad de servicio le permite delegar permisos específicos mediante el [control de acceso basado en roles](azure-stack-manage-permissions.md).

Como práctica recomendada, debe usar entidades de servicio para las aplicaciones. Las entidades de servicio se prefieren a la ejecución de una aplicación con sus propias credenciales por los siguientes motivos:

* Puede asignar permisos a la entidad de servicio diferentes a los de su cuenta. Normalmente, los permisos de una entidad de servicio están restringidos a exactamente aquello que la aplicación debe hacer.
* No es necesario cambiar las credenciales de la aplicación si los roles o las responsabilidades cambian.
* Puede usar un certificado para automatizar la autenticación al ejecutar un script desatendido.

## <a name="example-scenario"></a>Escenario de ejemplo

Tiene una aplicación de administración de configuración que necesita hacer un inventario de los recursos de Azure mediante Azure Resource Manager. Puede crear una entidad de servicio y asignarle el rol Lector. Este rol da a la aplicación acceso de solo lectura a los recursos de Azure.

## <a name="getting-started"></a>Introducción

Siga los pasos de este artículo como guía para:

* Crear una entidad de servicio para la aplicación.
* Registrar la aplicación y crear una clave de autenticación.
* Asignar la aplicación a un rol.

La manera en que ha configurado Active Directory para Azure Stack determina cómo crear a una entidad de servicio. Elija una de las siguientes opciones:

* Creación de una entidad de servicio para [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Creación de una entidad de servicio para [Servicios de federación de Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Los pasos para asignar una entidad de servicio a un rol son los mismos para Azure AD y AD FS. Después de crear la entidad de servicio, puede [delegar permisos](azure-stack-create-service-principals.md#assign-role-to-service-principal) mediante la asignación a un rol.

## <a name="create-a-service-principal-for-azure-ad"></a>Creación de una entidad de servicio para Azure AD

Si Azure Stack usa Azure AD como almacén de identidades, puede crear una entidad de servicio con los mismos pasos que en Azure, mediante Azure Portal.

>[!NOTE]
Compruebe que tiene los [permisos de Azure AD requeridos](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) antes de empezar a crear una entidad de servicio.

### <a name="create-service-principal"></a>Creación de una entidad de servicio

Para crear una entidad de servicio para la aplicación:

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
2. Seleccione **Azure Active Directory** > **Registros de aplicaciones** > **Agregar**.
3. Proporcione un nombre y una dirección URL para la aplicación. Seleccione either **Web app / API** (Aplicación web/API) o **Nativa** para el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Crear**.

### <a name="get-credentials"></a>Obtener credenciales

Al iniciar sesión mediante programación, deberá usar el identificador de la aplicación y una clave de autenticación. Para obtener estos valores:

1. En **App registrations** (Registros de aplicaciones), en Active Directory, seleccione su aplicación.

2. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones en el [ejemplo de aplicaciones](#sample-applications) usan el **identificador de cliente** al hacer referencia al **Id. de la aplicación**.

     ![Identificador de la aplicación para la aplicación](./media/azure-stack-create-service-principal/image12.png)
3. Para generar una clave de autenticación, seleccione **Claves**.

4. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Guardar**.

>[!IMPORTANT]
Después de guardar la clave, se muestra la clave **VALUE**. Escriba este valor porque no podrá recuperar la clave más adelante. Guarde el valor de clave donde la aplicación pueda recuperarlo.

![Advertencia del valor de clave para la clave guardada.](./media/azure-stack-create-service-principal/image15.png)

El paso final es [asignar un rol a la aplicación](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Crear una entidad de servicio para AD FS

Si ha implementado Azure Stack mediante AD FS como almacén de identidades, puede usar PowerShell para las siguientes tareas:

* Crear una entidad de servicio.
* Asignar un rol a la entidad de servicio.
* Iniciar sesión mediante la identidad de la entidad de servicio.

### <a name="before-you-begin"></a>Antes de empezar

[Descargue las herramientas necesarias de Azure Stack en el equipo local.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importar el módulo de Identity PowerShell

Vaya a la carpeta de descarga de las herramientas de Azure Stack e importe el módulo de Identity PowerShell mediante el comando siguiente:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Al importar el módulo de Identity, puede recibir un mensaje de error: "AzureStack.Connect.psm1 no está firmado digitalmente. El script no se ejecutará en el sistema".

Para corregir este problema, debe configurar la directiva de ejecución para permitir la ejecución del script. Para establecer la directiva de ejecución, ejecute el siguiente comando en una sesión de PowerShell con privilegios elevados:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Creación de la entidad de servicio

Puede crear una entidad de servicio mediante la ejecución del comando siguiente. Para ello, asegúrese de actualizar el parámetro **DisplayName**:

```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose

```

### <a name="assign-a-role"></a>Asignar un rol

Una vez que ha creado la entidad de servicio, debe [asignarle un rol](azure-stack-create-service-principals.md#assign-role-to-service-principal).

### <a name="sign-in-using-powershell"></a>Inicio de sesión con PowerShell

Para iniciar sesión en Azure Stack, puede ejecutar el comando siguiente y asegurarse de actualizar el parámetro **EnvironmentName** con el nombre de la aplicación:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId `
 -TenantId $directoryTenantId
```

## <a name="assign-the-service-principal-to-a-role"></a>Asignación de un rol a la entidad de servicio

Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Puede establecer el ámbito de un rol en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, una aplicación con el rol Lector para un grupo de recursos significa que la aplicación puede leer cualquiera de los recursos del grupo de recursos.

Siga estos pasos como guía para asignar un rol a una entidad de servicio.

1. En el portal de Azure Stack, desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**.

2. Seleccione la suscripción que se asignará a la aplicación. En este ejemplo, la suscripción es Visual Studio Enterprise.

     ![Selección de la suscripción de Visual Studio Enterprise para asignación](./media/azure-stack-create-service-principal/image16.png)

3. Seleccione **Control de acceso (IAM)** para la suscripción.

     ![Selección de Control de acceso](./media/azure-stack-create-service-principal/image17.png)

4. Seleccione **Agregar**.

5. Seleccione el rol que quiere asignar a la aplicación.

6. Busque la aplicación y selecciónela.

7. Seleccione **Aceptar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

Ahora que ha creado una entidad de servicio y le ha asignado un rol, la aplicación puede tener acceso a los recursos de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

[Administración de permisos de usuario](azure-stack-manage-permissions.md)
