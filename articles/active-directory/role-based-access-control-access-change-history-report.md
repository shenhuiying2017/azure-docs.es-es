<properties
	pageTitle="Creación de un informe del historial de cambios de acceso | Microsoft Azure"
	description="Utilice Control de acceso basado en rol para generar un informe con todos los cambios en el acceso a las suscripciones de Azure en los últimos 90 días."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/03/2016"
	ms.author="kgremban"/>

# Creación de un informe del historial de cambios de acceso

Cada vez que alguien concede o revoca el acceso dentro de sus suscripciones, los cambios se registran en los eventos de Azure. Puede crear informes de historial de cambios de acceso para ver todos los cambios de los últimos 90 días.

## Creación de un informe con Azure PowerShell
Para crear un informe de historial de cambios de acceso en PowerShell, utilice el siguiente comando `Get-AzureRMAuthorizationChangeLog`. Puede encontrar más información sobre este cmdlet en la [galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Al llamar a este comando, puede especificar qué propiedad de las asignaciones desea mostrar, incluidas las siguientes:

| Propiedad | Description |
| -------- | ----------- |
| **Acción** | Si se ha concedido o revocado el acceso. |
| **Autor de llamada** | El propietario responsable del cambio de acceso. |
| **Date** | La fecha y la hora en que se modificó el acceso. |
| **DirectoryName** | El directorio de Azure Active Directory. |
| **PrincipalName** | El nombre del usuario, el grupo o la aplicación. |
| **PrincipalType** | Si la asignación ha sido para un usuario, un grupo o una aplicación. |
| **RoleId** | El GUID del rol que se ha concedido o revocado. |
| **RoleName** | El rol que se ha concedido o revocado. |
| **ScopeName** | El nombre de la suscripción, el grupo de recursos o el recurso. |
| **ScopeType** | Si la asignación era en el ámbito de la suscripción, el grupo de recursos o el recurso. |
| **SubscriptionId** | El GUID de la suscripción de Azure. |
| **SubscriptionName** | El nombre de la suscripción de Azure. |

En el siguiente comando de ejemplo se muestran todos los cambios de acceso de la suscripción que tuvieron lugar en los últimos 7 días:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get:AzureRMAuthorizationChangeLog (captura de pantalla)](./media/role-based-access-control-configure/access-change-history.png)

## Creación de un informe con la CLI de Azure
Para crear un informe de historial de cambios de acceso en la interfaz de la línea de comandos (CLI) de Azure, use el comando siguiente `azure role assignment changelog list`.

## Exportación a una hoja de cálculo
Para guardar el informe o manipular los datos, exporte los cambios de acceso a un archivo .csv. Así podrá ver el informe en una hoja de cálculo para revisarlo.

![Changelog visto como una hoja de cálculo ( captura de pantalla)](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Otras referencias
- Introducción al [control de acceso basado en roles de Azure](role-based-access-control-configure.md)
- Uso de [roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0810_2016-->