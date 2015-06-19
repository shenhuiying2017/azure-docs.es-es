<properties
   pageTitle="Autenticación de una entidad de servicio con el Administrador de recursos de Azure"
   description="Describe cómo conceder acceso a una entidad de servicio a través del control de acceso basado en rol y autenticarla. Muestra cómo realizar estas tareas con PowerShell y la CLI de Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Autenticación de una entidad de servicio con el Administrador de recursos de Azure

En este tema se muestra cómo permitir que una entidad de servicio (por ejemplo, un proceso, una aplicación o un servicio automatizado) tenga acceso a otros recursos de la suscripción. Con el Administrador de recursos de Azure, puede usar el control de acceso basado en rol para conceder las acciones permitidas a una entidad de servicio y autenticar a esa entidad de servicio. En este tema se muestra cómo usar PowerShell y la CLI de Azure para asignar un rol a una entidad de servicio y autenticar la entidad de servicio.


## Conceptos
1. Azure Active Directory (AAD): un servicio de administración de identidades y acceso para la nube. Para obtener más información, consulte [¿Qué es Azure Active Directory?](./active-directory-whatis.md)
2. Entidad de servicio: una instancia de una aplicación en un directorio.
3. Aplicación de AD: un registro de directorio que identifica una aplicación en AAD. Para obtener más información, consulte [Conceptos básicos sobre autenticación en Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Concesión de acceso a una entidad de servicio y su autenticación con PowerShell

Si no tiene instalado Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](./powershell-install-configure.md).

Comenzará creando una entidad de servicio. Para ello, debemos crear una aplicación en el directorio. Esta sección le guiará a través de la creación de una nueva aplicación en el directorio.

1. Cree una nueva aplicación de AAD ejecutando el comando **New-AzureADApplication**. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        PS C:> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

   Se devuelve a la aplicación de Azure AD:

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


>[AZURE.NOTE]La propiedad **ApplicationId** es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens JWT. Guarde la salida o la captura en una variable.

3. Cree a una entidad de servicio para la aplicación.

        PS C:> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

   Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

4. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, proporcione el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para obtener más información sobre el control de acceso basado en rol, consulte [Administración y auditoría de acceso a recursos](./resource-group-rbac.md).

        PS C:> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

5. Recupere la suscripción en la que se creó la asignación de roles. Esta suscripción se utilizará más adelante para obtener el valor de **TenantId** del inquilino en el que reside la asignación de rol de la entidad de servicio.

        PS C:> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

   Si ha creado la asignación de rol en una suscripción que no sea la suscripción seleccionada actualmente, puede especificar los parámetros **SubscriptoinId** o **SubscriptionName**  para recuperar una suscripción diferente.

6. Cree un nuevo objeto **PSCredential** que contiene las credenciales mediante la ejecución del comando **Get-Credential**.

        PS C:> $creds = Get-Credential

   Se le pedirá que escriba sus credenciales.

   ![][1]

   Para el nombre de usuario, utilice el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

7. Utilice las credenciales que especificó para el cmdlet **Add-AzureAccount**, que firmará la entidad de servicio en:

        PS C:> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

   Ahora debe autenticarse como la entidad de servicio para la aplicación de AAD que ha creado.


## Concesión de acceso a una entidad de servicio y su autenticación con la CLI de Azure

Si no tiene la CLI de Azure para Mac, Linux y Windows instalada, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](xplat-cli-install.md).

Ya debe tener una aplicación de AD y la entidad de servicio para realizar estos pasos. Para obtener información acerca de cómo configurar una aplicación de AD y la entidad de servicio a través del portal clásico de Azure, consulte [Creación de una nueva entidad de servicio de Azure mediante el portal clásico](./resource-group-create-service-principal-portal.md).

1. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, proporcione el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para obtener más información sobre el control de acceso basado en rol, consulte [Administración y auditoría de acceso a recursos](./resource-group-rbac.md).

        azure role assignment create --objectId {service-principal-object-id} -o Reader -c /subscriptions/{subscriptionId}/

2. Determine el valor **TenantId** del inquilino en el que reside la asignación del rol de la entidad de servicio mostrando las cuentas y buscando la propiedad **TenantId** en la salida.

        azure account list

3. Inicie sesión utilizando la entidad de servicio como su identidad. Para el nombre de usuario, utilice el valor de **ApplicationId** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

  Ahora debe autenticarse como la entidad de servicio para la aplicación de AAD que ha creado.

## Pasos siguientes
Introducción

- [Información general del Administrador de recursos de Azure](./resource-group-overview.md)  
- [Uso de Azure PowerShell con el Administrador de recursos de Azure](./powershell-azure-resource-manager.md)
- [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Uso del Portal de Azure para administrar los recursos de Azure](./resource-group-portal.md)  
  
Creación e implementación de aplicaciones
  
- [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md)  
- [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](./resource-group-template-deploy.md)  
- [Solución de problemas de implementaciones de grupo de recursos en Azure](virtual-machines/resource-group-deploy-debug.md)  
- [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md)  
- [Operaciones avanzadas de plantilla](./resource-group-advanced-template.md)  
- [Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla](virtual-machines/arm-template-deployment.md)
  
Organización de los recursos
  
- [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md)  
  
Administración y auditoría del acceso
  
- [Administración y auditoría del acceso a los recursos](./resource-group-rbac.md)  
- [Creación de una nueva entidad de servicio de Azure mediante el portal de Azure](./resource-group-create-service-principal-portal.md)  
  


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!--HONumber=52-->
