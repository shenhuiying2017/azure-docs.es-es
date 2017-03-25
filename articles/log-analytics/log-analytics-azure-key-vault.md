---
title: "Solución Azure Key Vault en Log Analytics | Microsoft Docs"
description: "Puede utilizar la solución Azure Key Vault en Log Analytics para revisar los registros de Azure Key Vault."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 6cf48883d397a4b04e707111306d4596cd7af683
ms.lasthandoff: 03/11/2017


---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Solución de Azure Key Vault Analytics en Log Analytics

Puede utilizar la solución Azure Key Vault en Log Analytics para revisar los registros AuditEvents de Azure Key Vault.

Para usar la solución, debe habilitar el registro de diagnósticos de Azure Key Vault y dirigir los diagnósticos a un área de trabajo de Log Analytics. No se requiere escribir los registros en Azure Blob Storage.

> [!NOTE]
> En enero de 2017, cambia la forma de enviar registros de Key Vault a Log Analytics. Si la solución Key Vault que usa muestra *(en desuso)* en el título, consulte [Migración desde la solución Key Vault antigua](#migrating-from-the-old-key-vault-solution) para conocer los pasos que debe seguir.
>
>

## <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Para instalar y configurar la solución de Azure Key Vault, siga estas instrucciones:

1. Habilite la solución Azure Key Vault desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md). 
2. Habilite el registro de diagnósticos para que se supervisen los recursos de Key Vault usando el [Portal](#enable-key-vault-diagnostics-in-the-portal) o [PowerShell](#enable-key-vault-diagnostics-using-powershell) 

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Habilitación de los diagnósticos de Key Vault en el portal

1. En Azure Portal, navegue hasta el recurso de Key Vault que se va a supervisar.
2. Seleccione *Registros de diagnósticos* para abrir la página siguiente.

   ![imagen del icono de Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Haga clic en *Activar diagnósticos* para abrir la página siguiente.

   ![imagen del icono de Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Para activar los diagnósticos, haga clic en *Activar* en *Estado*.
5. Haga clic en la casilla *Send to Log Analytics* (Enviar a Log Analytics).
6. Seleccione un área de trabajo de Log Analytics existente o cree un área de trabajo.
7. Para habilitar registros *AuditEvent*, haga clic en la casilla bajo Registro.
8. Haga clic en *Guardar* para habilitar el registro de diagnósticos en Log Analytics.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Habilitación de los diagnósticos de Key Vault mediante PowerShell
El siguiente script de PowerShell proporciona un ejemplo de cómo utilizar `Set-AzureRmDiagnosticSetting` para habilitar el registro de diagnósticos para Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```
 
 

## <a name="review-azure-key-vault-data-collection-details"></a>Revisión de los detalles de recopilación de datos de Azure Key Vault
La solución Azure Key Vault recopila registros de diagnóstico desde Key Vault.
No es necesario escribir los registros en Azure Blob Storage y no se requiere ningún agente para la recopilación de datos.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos para Azure Key Vault.

| Plataforma | Agente directo | Agente System Center Operations Manager | Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Las tablas de Azure |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Sí](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) | a la llegada |

## <a name="use-azure-key-vault"></a>Uso de Azure Key Vault
Después de [instalar la solución](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), vea los datos de Key Vault haciendo clic en icono de **Azure Key Vault** desde la página **Introducción** de Log Analytics.

![imagen del icono de Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Tras hacer clic en el icono de **Overview** (Información general), puede ver resúmenes de los registros y desplazarse hasta los detalles de las categorías siguientes:

* Volumen de todas las operaciones de almacén de claves a través del tiempo
* Volúmenes de operaciones fallidas a través del tiempo
* Promedio de latencia operacional por operación
* Calidad de servicio para las operaciones con el número de operaciones que requieren más de 1000 ms y una lista de las operaciones que tardan más de 1000 ms

![imagen del panel de Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![imagen del panel de Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Visualización de detalles de cualquier operación
1. En la página **Overview** (Información general), haga clic en el icono de **Azure Key Vault**.
2. En el panel **Azure Key Vault**, revise la información de resumen en una de las hojas y haga clic en una para obtener información detallada acerca de esta en la página de búsqueda de registros.
   
    En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.

## <a name="log-analytics-records"></a>Registros de Log Analytics
La solución de Azure Key Vault analiza los registros que tienen un tipo de **KeyVaults** que se recopilan desde los [registros AuditEvent](../key-vault/key-vault-logging.md) de Diagnóstico de Azure.  Las propiedades de estos registros se muestran en la tabla siguiente:  

| Propiedad | Descripción |
|:--- |:--- |
| Escriba |*AzureDiagnostics* |
| SourceSystem |*Las tablas de Azure* |
| CallerIpAddress |Dirección IP del cliente que realizó la solicitud. |
| Categoría | *AuditEvent* |
| CorrelationId |Un GUID opcional que el cliente puede pasar para correlacionar los registros del lado cliente con los registros del lado servicio (el Almacén de claves). |
| DurationMs |Tiempo que tardó en atender la solicitud de API de REST, en milisegundos. Este tiempo no incluye la latencia de red, por lo que el tiempo que se mide en el cliente podría no coincidir con este tiempo. |
| httpStatusCode_d |Código de estado HTTP devuelto por la solicitud (por ejemplo, *200*) |
| id_s |Identificador único de la solicitud. |
| identity_claim_appid_g | GUID para el identificador de aplicación |
| nombreOperación |Nombre de la operación, como se documenta en el [registro de Azure Key Vault](../key-vault/key-vault-logging.md). |
| OperationVersion |Versión de API de REST solicitada por el cliente (por ejemplo, *2015-06-01*) |
| requestUri_s |URI de la solicitud. |
| Recurso |Nombre del almacén de claves. |
| ResourceGroup |Grupo de recursos del almacén de claves. |
| ResourceId |Identificador de recursos del Administrador de recursos de Azure Para los registros de Key Vault, siempre es el identificador de recurso de Key Vault. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *ALMACENES* |
| ResultSignature |Código de estado HTTP (por ejemplo, *OK*) |
| ResultType |Resultado de solicitud de API de REST (por ejemplo, *Correcto*) |
| SubscriptionId |Identificador de la suscripción de Azure que contiene el almacén de claves. |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migración desde la solución Key Vault antigua
En enero de 2017, cambia la forma de enviar registros de Key Vault a Log Analytics. Estos cambios proporcionan las siguientes ventajas:
+ Los registros se escriben directamente en Log Analytics sin necesidad de usar una cuenta de almacenamiento
+ Menor latencia desde el momento en el que los registros se generan hasta que están disponibles en Log Analytics
+ Menos pasos de configuración
+ Un formato común para todos los tipos de diagnósticos de Azure

Para usar la solución actualizada:

1. [Configure los diagnósticos que se van a enviar directamente a Log Analytics desde Key Vault](#enable-key-vault-diagnostics-in-the-portal).  
2. Habilite la solución Azure Key Vault mediante el proceso que se describe en [Incorporación de soluciones de administración de Log Analytics](log-analytics-add-solutions.md).
3. Actualice cualquier consulta guardada, panel o alerta para usar el nuevo tipo de datos.
  + El tipo se cambia de KeyVaults a AzureDiagnostics. Puede usar ResourceType para filtrar registros de Key Vault.
  - En lugar de `Type=KeyVaults`, use `Type=AzureDiagnostics ResourceType=VAULTS`.
  + Campos: (Los nombres de campo distinguen entre mayúsculas y minúsculas).
  - Para todos los campos que tengan un sufijo \_s, \_d o \_g en el nombre, cambie el primer carácter a minúsculas.
  - Para todos los campos que tengan un sufijo \_o en el nombre, los datos se dividen en campos individuales según los nombres de campo anidados. Por ejemplo, el UPN del llamador se almacena en un campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`.
   - El campo CallerIpAddress cambió a CallerIPAddress.
   - El campo RemoteIPCountry ya no está presente.
4. Quite la solución *Key Vault Analytics (en desuso)*. Si usa PowerShell, utilice `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`. 

Los datos recopilados antes del cambio no aparecen en la nueva solución. Puede seguir consultando estos datos con el tipo y los nombres de campo anteriores.

## <a name="troubleshooting"></a>Solución de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados sobre los datos de Azure Key Vault.


