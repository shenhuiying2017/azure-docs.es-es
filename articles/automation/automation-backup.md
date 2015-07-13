<properties 
   pageTitle="Copia de seguridad de Automatización de Azure"
   description="Describe cómo crear una copia de seguridad de los contenidos de una cuenta de automatización para poder conservarlos después de eliminar una cuenta de automatización."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Copia de seguridad de Automatización de Azure

Cuando se elimina una cuenta de automatización en Microsoft Azure, se eliminan todos los objetos de la cuenta, incluidos runbooks, módulos, configuraciones, trabajos y recursos. No es posible recuperar los objetos una vez eliminada la cuenta. Puede usar la información siguiente para crear una copia de seguridad de los contenidos de la cuenta de automatización antes de eliminarla.

## Runbooks

Puede exportar los runbooks a archivos de script con el Portal de administración de Azure o el cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) en Windows PowerShell. Es posible importar estos archivos de script a otra cuenta de automatización, tal como se describe en [Creación o importación de un runbook](https://msdn.microsoft.com/library/dn643637.aspx).


## Módulos de integración

No es posible exportar módulos de integración desde Automatización de Azure. Debe asegurarse de que se encuentren disponibles fuera de la cuenta de automatización.

## Recursos

No es posible exportar [recursos](https://msdn.microsoft.com/library/dn939988.aspx) desde Automatización de Azure. Si utiliza el Portal de administración de Azure, debe anotar los detalles de las variables, las credenciales, los certificados, las conexiones y las programaciones. Luego debe crear manualmente todos los recursos utilizados por los runbooks que importa a otra automatización.

Puede usar [cmdlets de Azure](https://msdn.microsoft.com/library/dn690262.aspx) para recuperar los detalles de recursos no cifrados y guardarlos para una referencia futura o crear recursos equivalentes en otra cuenta de automatización.

No es posible recuperar el valor de variables cifradas o del campo de contraseña de las credenciales mediante el uso de cmdlets. Si no conoce estos valores, puede recuperarlos desde un runbook con las actividades [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) y [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

No es posible exportar certificados desde Automatización de Azure. Debe asegurarse de que todos los certificados estén disponibles fuera de Azure.

## Artículos relacionados

- [Creación o importación de un runbook](https://msdn.microsoft.com/library/dn643637.aspx)
- [Recursos de automatización](https://msdn.microsoft.com/library/dn939988.aspx)
- [Cmdlets de Azure](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=62-->