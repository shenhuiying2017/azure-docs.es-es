---
title: "Creación de una entidad de servicio de Azure Stack | Microsoft Docs"
description: "Describe cómo crear una nueva entidad de servicio que puede usarse con el control de acceso basado en roles en Azure Resource Manager para administrar el acceso a los recursos."
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: helaw
ms.openlocfilehash: 96d5cdfc28759fd516eab5fd97c6cf444af08cf6
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>Proporcionar a las aplicaciones acceso a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Cuando una aplicación necesite acceso para implementar o configurar recursos a través de Azure Resource Manager en Azure Stack, cree una entidad de servicio, que es una credencial para la aplicación.  A continuación, puede delegar únicamente los permisos necesarios para esa entidad de servicio.  

Por ejemplo, puede tener una herramienta de administración de configuración que use Azure Resource Manager para hacer un inventario de los recursos de Azure.  En este escenario, puede crear una entidad de servicio, concederle el rol de lector y limitar la herramienta de administración de configuración al acceso de solo lectura. 

Es preferible que las entidades de servicio ejecuten la aplicación con sus propias credenciales por los siguientes motivos:

* Puede asignar permisos a la entidad de servicio diferentes a los de su cuenta. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
* No es necesario cambiar las credenciales de la aplicación si las responsabilidades cambian.
* Puede usar un certificado para automatizar la autenticación al ejecutar un script desatendido.  

## <a name="getting-started"></a>Introducción

Dependiendo de cómo ha implementado Azure Stack, primero debe crear una entidad de servicio.  Este documento le guía a través del proceso de creación de una entidad de servicio para [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) y [Servicios de federación de Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Una vez que haya creado la entidad de servicio, se usarán un conjunto de pasos comunes para AD FS y Azure Active Directory a fin de [delegar permisos](azure-stack-create-service-principals.md#assign-role-to-service-principal) al rol.     

## <a name="create-service-principal-for-azure-ad"></a>Crear una entidad de servicio para Azure AD

Si ha implementado Azure Stack con Azure AD como el almacén de identidades, puede crear entidades de servicio igual que hace para Azure.  En este tema se muestra cómo realizar estos pasos a través del portal.  Compruebe que tiene los [permisos de Azure AD necesarios](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) antes de comenzar.

### <a name="create-service-principal"></a>Creación de una entidad de servicio
En esta sección, creará una aplicación (entidad de servicio) en Azure AD que representará su aplicación.

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
2. Seleccione **Azure Active Directory** > **Registros de aplicaciones** > **Agregar**.   
3. Proporcione un nombre y una dirección URL para la aplicación. Seleccione either **Web app / API** (Aplicación web/API) o **Nativa** para el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Crear**.

Creó una entidad de servicio para la aplicación.

### <a name="get-credentials"></a>Obtener credenciales
Al iniciar sesión mediante programación, deberá usar el identificador de la aplicación y una clave de autenticación. Para obtener estos valores, use los pasos siguientes:

1. En **App registrations** (Registros de aplicaciones), en Active Directory, seleccione su aplicación.

2. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones de la sección de [aplicaciones de ejemplo](#sample-applications) hacen referencia a este valor como el identificador de cliente.

     ![ID. DE CLIENTE](./media/azure-stack-create-service-principal/image12.png)
3. Para generar una clave de autenticación, seleccione **Claves**.

4. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Guardar**.

Después de guardar la clave, se muestra el valor de la clave. Copie este valor porque no podrá recuperarlo más adelante. Proporcione el valor de clave junto con el identificador de la aplicación para firmar en nombre de la aplicación. Guarde el valor de clave donde la aplicación pueda recuperarlo.

![clave guardada](./media/azure-stack-create-service-principal/image15.png)


Una vez que haya finalizado, [asigne un rol a la aplicación](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Crear una entidad de servicio para AD FS
Si ha implementado Azure Stack con AD FS, puede usar PowerShell para crear una entidad de servicio, asignar un rol para el acceso e iniciar sesión en Powershell con dicha identidad.

El script se ejecuta desde el punto de conexión con privilegios de una máquina virtual ERCS.


Requisitos:
- Se requiere un certificado.

**Parámetros**

Se requiere la siguiente información como entrada para los parámetros de automatización:


|Parámetro|Description|Ejemplo|
|---------|---------|---------|
|Nombre|Nombre de la cuenta SPN|MyAPP|
|ClientCertificates|Matriz de objetos de certificado|Certificado X509|
|ClientRedirectUris<br>(Opcional)|URI de redireccionamiento de aplicación|         |

**Ejemplo**

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes comandos:

   > [!NOTE]
   > En este ejemplo se crea un certificado autofirmado. Al ejecutar estos comandos en una implementación de producción, use Get-Certificate para recuperar el objeto de certificado del certificado que desee usar.

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. Una vez finalizada la automatización, esta muestra los detalles necesarios para usar el SPN. 

   Por ejemplo:

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```
### <a name="assign-a-role"></a>Asignar un rol
Una vez que se crea la entidad de servicio, debe [asignarla a un rol](azure-stack-create-service-principals.md#assign-role-to-service-principal).

### <a name="sign-in-through-powershell"></a>Iniciar sesión a través de PowerShell
Una vez que haya asignado un rol, puede iniciar sesión en Azure Stack a través de la entidad de servicio con el comando siguiente:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Asignar un rol a la entidad de servicio
Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).

Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga.

1. En el portal de Azure Stack, desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**. También puede seleccionar un grupo de recursos o un recurso.

2. Seleccione la suscripción específica (grupo de recursos o recurso) a la que quiere asignar la aplicación.

     ![seleccionar suscripción para la asignación](./media/azure-stack-create-service-principal/image16.png)

3. Seleccione **Access Control (IAM)**.

     ![seleccionar acceso](./media/azure-stack-create-service-principal/image17.png)

4. Seleccione **Agregar**.

5. Seleccione el rol que quiere asignar a la aplicación.

6. Busque la aplicación y selecciónela.

7. Seleccione **Aceptar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

Ahora que ha creado una entidad de servicio y le ha asignado un rol, puede empezar a usarla dentro de la aplicación para tener acceso a los recursos de Azure Stack.  

## <a name="next-steps"></a>Pasos siguientes

[Add users for ADFS](azure-stack-add-users-adfs.md) (Agregar usuarios para ADFS)
[Manage user permissions](azure-stack-manage-permissions.md) (Administrar permisos de usuario)