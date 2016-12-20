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
ms.date: 07/12/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 90ef2d32a00744decdb5a50ae1f707820e87f513


---
# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Solución de Azure Key Vault (versión preliminar) en Log Analytics
> [!NOTE]
> Se trata de una [solución preliminar](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).
> 
> 

Puede utilizar la solución Azure Key Vault en Log Analytics para revisar los registros AuditEvents de Azure Key Vault.

Puede habilitar el registro de eventos de auditoría para Azure Key Vault. Estos registros se escriben en Azure Blob Storage, donde Log Analytics los puede indexar para la búsqueda y el análisis.

## <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Para instalar y configurar la solución de Azure Key Vault, siga estas instrucciones:

1. Habilite el [registro de diagnóstico para Key Vault](../key-vault/key-vault-logging.md) de los recursos que desea supervisar.
2. Configure Log Analytics para leer los registros de Blob Storage mediante el proceso que se describe en el artículo sobre [archivos JSON en Blob Storage](log-analytics-azure-storage-json.md).
3. Habilite la solución de Azure Key Vault mediante el proceso que se describe en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Revisión de los detalles de recopilación de datos de Azure Key Vault
La solución de Azure Key Vault recopila registros de diagnóstico desde Azure Blob Storage para Azure Key Vault.
Para la recopilación de datos no se necesita ningún agente.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos para Azure Key Vault.

| Plataforma | Agente directo | Agente System Center Operations Manager (SCOM) | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Las tablas de Azure |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Sí](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![No](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |10 minutos |

## <a name="use-azure-key-vault"></a>Uso de Azure Key Vault
Después de instalar la solución, puede ver el resumen de los estados de las solicitudes a través del tiempo para los almacenes de claves supervisados mediante el icono de **Azure Key Vault** de la página **Overview** (Información general) de Log Analytics.

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
La solución de Azure Key Vault analiza los registros que tienen un tipo de **KeyVaults** que se recopilan desde los [registros AuditEvent](../key-vault/key-vault-logging.md) de Diagnóstico de Azure.  Las propiedades de estos registros se muestran en la tabla siguiente.  

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*KeyVaults* |
| SourceSystem |*AzureStorage* |
| CallerIpAddress |Dirección IP del cliente que realizó la solicitud. |
| Categoría |Para los registros del Almacén de claves, AuditEvent es el único valor disponible. |
| CorrelationId |Un GUID opcional que el cliente puede pasar para correlacionar los registros del lado cliente con los registros del lado servicio (el Almacén de claves). |
| DurationMs |Tiempo que tardó en atender la solicitud de API de REST, en milisegundos. Esto no incluye la latencia de red, por lo que el tiempo que se mide del cliente podría no coincidir con este tiempo. |
| HttpStatusCode_d |Código de estado HTTP devuelto por la solicitud. |
| Id_s |Identificador único de la solicitud. |
| Identity_o |Identidad del token que se ha presentado al realizar la solicitud de API de REST. Suele ser un "usuario", una "entidad de servicio" o una combinación de "usuario + appId", como en el caso de una solicitud procedente de un cmdlet de Azure PowerShell. |
| nombreOperación |Nombre de la operación, como se documenta en el [registro de Azure Key Vault](../key-vault/key-vault-logging.md). |
| OperationVersion |Versión de API de REST solicitada por el cliente. |
| RemoteIPLatitude |Latitud del cliente que realizó la solicitud. |
| RemoteIPLongitude |Longitud del cliente que realizó la solicitud. |
| RemoteIPCountry |País del cliente que realizó la solicitud. |
| RequestUri_s |URI de la solicitud. |
| Recurso |Nombre del almacén de claves. |
| ResourceGroup |Grupo de recursos del almacén de claves. |
| ResourceId |Identificador de recursos del Administrador de recursos de Azure Para los registros del Almacén de claves, siempre es el identificador de recurso de Almacén de claves. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResultSignature |Estado de HTTP. |
| ResultType |Resultado de la solicitud de API de REST. |
| SubscriptionId |Identificador de la suscripción de Azure que contiene el almacén de claves. |

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados sobre los datos de Azure Key Vault.




<!--HONumber=Nov16_HO3-->


