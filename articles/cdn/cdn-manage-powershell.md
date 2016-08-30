<properties
	pageTitle="Administración de la red CDN de Azure con PowerShell | Microsoft Azure"
	description="Aprenda a usar los cmdlets de Azure PowerShell para administrar la red CDN de Azure."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="casoper"/>


# Administración de la red CDN de Azure con PowerShell

PowerShell proporciona uno de los métodos más flexibles para administrar los perfiles y puntos de conexión de la red CDN de Azure. Puede usar PowerShell interactivamente o mediante la escritura de scripts para automatizar tareas de administración. Este tutorial muestra algunas de las tareas más comunes que puede realizar con PowerShell para administrar los perfiles y puntos de conexión de la red CDN de Azure.

## Requisitos previos

Para usar PowerShell para administrar los perfiles y puntos de conexión de la red CDN de Azure debe tener instalado el módulo de Azure PowerShell. Para obtener información sobre cómo instalar Azure PowerShell y conectarlo a Azure mediante el cmdlet `Login-AzureRmAccount` consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] Debe iniciar sesión con `Login-AzureRmAccount` para poder ejecutar los cmdlets de Azure PowerShell.

## Lista de los cmdlets de la red CDN de Azure

Puede enumerar todos los cmdlets de la red CDN de Azure mediante el cmdlet `Get-Command`.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## Ayuda

Puede obtener ayuda para cualquiera de estos cmdlets mediante el cmdlet `Get-Help`. `Get-Help` proporciona el uso y la sintaxis y, opcionalmente, muestra ejemplos.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## Listado de perfiles existentes de la red CDN de Azure

El cmdlet `Get-AzureRmCdnProfile` sin parámetros permite recuperar todos los perfiles de red CDN existentes.

```powershell
Get-AzureRmCdnProfile
```

Esta salida se puede canalizar a cmdlets para la enumeración.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

También puede devolver un solo perfil especificando el grupo de recursos y el nombre del perfil.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

>[AZURE.TIP] Es posible tener varios perfiles de red CDN con el mismo nombre, siempre y cuando estén en distintos grupos de recursos. Si se omite el parámetro `ResourceGroupName` se devolverán todos los perfiles con un nombre coincidente.

## Listado de los puntos de conexión existentes de la red CDN

`Get-AzureRmCdnEndpoint` puede recuperar un punto de conexión individual o todos los puntos de conexión de un perfil.

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## Creación de perfiles y puntos de conexión de red CDN

`New-AzureRmCdnProfile` y `New-AzureRmCdnEndpoint` se utilizan para crear perfiles y puntos de conexión de red CDN.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## Comprobación de la disponibilidad del nombre de un punto de conexión

`Get-AzureRmCdnEndpointNameAvailability` devuelve un objeto que indica si un nombre de punto de conexión está disponible.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## Incorporación de un dominio personalizado

`New-AzureRmCdnCustomDomain` agrega un nombre de dominio personalizado a un punto de conexión existente.

>[AZURE.IMPORTANT] Debe configurar el CNAME con su proveedor de DNS como se describe en [Cómo asignar un dominio personalizado al punto de conexión de la Red de entrega de contenido (CDN)](./cdn-map-content-to-custom-domain.md). Puede probar la asignación antes de modificar el punto de conexión mediante `Test-AzureRmCdnCustomDomain`.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## Modificación de un punto de conexión

`Set-AzureRmCdnEndpoint` modifica un punto de conexión ya existente.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## Purga o precarga de los recursos de red CDN

`Unpublish-AzureRmCdnEndpointContent` purga los recursos de la caché, mientras que `Publish-AzureRmCdnEndpointContent` precarga los recursos en puntos de conexión compatibles.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## Inicio y detención de puntos de conexión de red CDN
`Start-AzureRmCdnEndpoint` y `Stop-AzureRmCdnEndpoint` se pueden utilizar para iniciar y detener los puntos de conexión individuales o los grupos de estos.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## Eliminación de recursos de red CDN

`Remove-AzureRmCdnProfile` y `Remove-AzureRmCdnEndpoint` se pueden utilizar para quitar perfiles y puntos de conexión.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## Pasos siguientes

Aprenda a automatizar la red CDN de Azure con [.NET](./cdn-app-dev-net.md) o [Node.js](./cdn-app-dev-node.md).

Para obtener información acerca de las características de la red CDN, consulte [Información general de la red CDN](./cdn-overview.md).

<!---HONumber=AcomDC_0817_2016-->