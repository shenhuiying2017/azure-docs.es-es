---
title: Arquitectura de conectividad de Azure SQL Database | Microsoft Docs
description: En este documento se explica la arquitectura de conectividad de Azure SQLDB, desde dentro o desde fuera de Azure.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/05/2017
ms.author: carlrab
ms.openlocfilehash: eda6e19d27afbf07df853dd4cef5ece1a745034d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="azure-sql-database-connectivity-architecture"></a>Arquitectura de conectividad de Azure SQL Database 

En este artículo se explica la arquitectura de conectividad de Azure SQL Database y cómo funcionan los distintos componentes para dirigir el tráfico a una instancia de Azure SQL Database. La funcionalidad de estos componentes de la conectividad de Azure SQL Database consiste en dirigir el tráfico de red a la base de datos de Azure con clientes que se conectan desde dentro y desde fuera de Azure. En este artículo también se proporcionan ejemplos de script para cambiar cómo se establece la conectividad y, además, se incluyen consideraciones sobre la modificación de la configuración predeterminada de la conectividad. 

## <a name="connectivity-architecture"></a>Arquitectura de conectividad

En el siguiente diagrama se proporciona una descripción general de la arquitectura de conectividad de Azure SQL Database.

![Descripción general de la arquitectura](./media/sql-database-connectivity-architecture/architecture-overview.png)


En los pasos siguientes se describe cómo se establece una conexión a una base de datos SQL de Azure a través del equilibrador de carga de software (SLB) de Azure SQL Database y de la puerta de enlace de Azure SQL Database.

- Los clientes, tanto desde dentro como desde fuera de Azure, se conectan a la SLB, que tiene una dirección IP pública y escucha en el puerto 1433.
- El SLB dirige el tráfico a la puerta de enlace de Azure SQL Database.
- La puerta de enlace redirige el tráfico al middleware del proxy correcto.
- El middleware del proxy redirige el tráfico a la base de datos SQL de Azure adecuada.

> [!IMPORTANT]
> Cada uno de estos componentes integra protección frente a ataques de denegación de servicio distribuido (DDoS) en la red y en el nivel de aplicación.
>

## <a name="connectivity-from-within-azure"></a>Conectividad desde dentro de Azure

Si va a conectarse desde dentro de Azure, las conexiones tienen una directiva de conexión predeterminada basada en el **redireccionamiento**. Una directiva de **redireccionamiento** establece que las conexiones después de la sesión TCP se realicen a la base de datos SQL de Azure; a continuación, la sesión del cliente se redirige al middleware del proxy, pero se cambia la IP virtual de destino de la puerta de enlace de Azure SQL Database por la del middleware del proxy. Por lo tanto, todos los paquetes posteriores fluyen directamente a través del middleware del proxy, de tal forma que omiten la puerta de enlace de Azure SQL Database. En el siguiente diagrama, se ilustra este flujo de tráfico.

![Descripción general de la arquitectura](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividad desde fuera de Azure

Si va a conectarse desde fuera de Azure, las conexiones tienen una directiva de conexión predeterminada basada en el **proxy**. Una directiva de **proxy** establece que la sesión TCP se realice a través de la puerta de enlace de Azure SQL Database y que todos los paquetes posteriores fluyan a través de la puerta de enlace. En el siguiente diagrama, se ilustra este flujo de tráfico.

![Descripción general de la arquitectura](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Direcciones IP de la puerta de enlace de Azure SQL Database

Para conectarse a una base de datos SQL de Azure desde recursos locales, debe permitir el tráfico de red saliente a la puerta de enlace de Azure SQL Database en su región de Azure. Las conexiones solo se establecen a través de la puerta de enlace al conectarse en modo de proxy, que es el valor predeterminado para conexiones desde recursos locales.

En la tabla siguiente se enumeran las direcciones IP principales y secundarias de la puerta de enlace de Azure SQL Database para todas las regiones de datos. En algunas regiones, hay dos direcciones IP. En estas regiones, la dirección IP principal es la dirección IP actual de la puerta de enlace y la dirección IP secundaria es una dirección IP de conmutación por error. La dirección de conmutación por error es la dirección a la que se puede mover el servidor para mantener la alta disponibilidad del servicio. En estas regiones, se recomienda que permita el tráfico saliente a ambas direcciones IP. La dirección IP secundaria es propiedad de Microsoft y no escucha en ningún servicio hasta que Azure SQL Database la activa para aceptar conexiones.

| Nombre de región | Dirección IP principal | Dirección IP secundaria |
| --- | --- |--- |
| Australia Oriental | 191.238.66.109 | 13.75.149.87 |
| Sudeste de Australia | 191.239.192.109 | 13.73.109.251 |
| Sur de Brasil | 104.41.11.5 | |
| Centro de Canadá | 40.85.224.249 | |
| Este de Canadá | 40.86.226.166 | |
| Central EE. UU.: | 23.99.160.139 | 13.67.215.62 |
| Asia oriental | 191.234.2.139 | 52.175.33.150 |
| Este de EE. UU. 1 | 191.238.6.43 | 40.121.158.30 |
| Este de EE. UU. 2 | 191.239.224.107 | 40.79.84.180 |
| India central | 104.211.96.159  | |
| Sur de India | 104.211.224.146  | |
| India occidental | 104.211.160.80 | |
| Este de Japón | 191.237.240.43 | 13.78.61.196 |
| Oeste de Japón | 191.238.68.11 | 104.214.148.156 |
| Corea Central | 52.231.32.42 | |
| Corea del Sur | 52.231.200.86 |  |
| Centro-Norte de EE. UU | 23.98.55.75 | 23.96.178.199 |
| Europa del Norte | 191.235.193.75 | 40.113.93.91 |
| Centro-Sur de EE. UU | 23.98.162.75 | 13.66.62.124 |
| Sudeste de Asia | 23.100.117.95 | 104.43.15.0 |
| Norte del Reino Unido | 13.87.97.210 | |
| Sur de Reino Unido 1 | 51.140.184.11 | |
| Sur del Reino Unido 2 | 13.87.34.7 | |
| Oeste de Reino Unido | 51.141.8.11  | |
| Centro occidental de EE.UU. | 13.78.145.25 | |
| Europa occidental | 191.237.232.75 | 40.68.37.158 |
| Oeste de EE. UU. 1 | 23.99.34.75 | 104.42.238.205 |
| Oeste de EE. UU. 2 | 13.66.226.202  | |
||||

## <a name="change-azure-sql-database-connection-policy"></a>Cambio de la directiva de conexión de Azure SQL Database

Para cambiar la directiva de conexión de Azure SQL Database de un servidor de Azure SQL Database, use la [API de REST](https://msdn.microsoft.com/library/azure/mt604439.aspx).

- Si la directiva de conexión se establece en **Proxy**, todos los paquetes de red fluyen a través de la puerta de enlace de Azure SQL Database. Para esta configuración, debe permitir el tráfico saliente solo a la dirección IP de la puerta de enlace de Azure SQL Database. El uso de la configuración de **proxy** ofrece más latencia que la de **redireccionamiento**.
- Si la configuración de la directiva de conexión está basada en el **redireccionamiento**, todos los paquetes de red fluyen directamente al proxy del middleware. Para esta configuración, debe permitir el tráfico saliente a varias direcciones IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para cambiar la configuración de la conexión a través de PowerShell

> [!IMPORTANT]
> Este script requiere el [módulo Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

El siguiente script de PowerShell muestra cómo cambiar la directiva de conexión.

```powershell
Add-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Script para cambiar la configuración de la conexión a través de CLI de Azure 2.0

> [!IMPORTANT]
> Este script requiere [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
>

El siguiente script de CLI muestra cómo cambiar la directiva de conexión.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo cambiar la directiva de conexión de Azure SQL Database de un servidor de Azure SQL Database, vea [Creación o actualización de la directiva de conexión de un servidor mediante la API de REST](https://msdn.microsoft.com/library/azure/mt604439.aspx).
- Para obtener información sobre el comportamiento de conexión de Azure SQL Database para clientes que usan ADO.NET 4.5 o una versión posterior, vea [Puertos más allá de 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obtener información general sobre el desarrollo de aplicaciones, vea [Introducción al desarrollo de aplicaciones en SQL Database](sql-database-develop-overview.md).
