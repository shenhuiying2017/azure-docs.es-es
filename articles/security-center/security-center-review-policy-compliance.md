---
title: Revisar el cumplimiento de la directiva de Security Center con la API REST de Azure | Microsoft Docs
description: Obtenga información sobre cómo usar las API REST de Azure para consultar el cumplimiento actual de las directivas de Security Center.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077770"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Revisar el cumplimiento de directivas de Security Center mediante las API REST

Security Center valida periódicamente los recursos de Azure con las directivas de seguridad definidas. Security Center también proporciona una API REST que le permite consultar el cumplimiento de sus propias aplicaciones; puede consultar el servicio directamente o importar los resultados JSON en otras aplicaciones. 

Aquí, aprenderá a recuperar el conjunto actual de recomendaciones de todos los recursos de Azure asociados a una suscripción.

Para recuperar el conjunto actual de recomendaciones:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Compilar la solicitud  

El parámetro `{subscription-id}` es necesario y debe contener el identificador de la suscripción de Azure que define las directivas. Si tiene varias suscripciones, consulte [Trabajo con varias suscripciones](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

El parámetro `api-version` es obligatorio. En este momento, estos puntos de conexión solo son compatibles con `api-version=2015-06-01-preview`. 

Los siguientes encabezados son obligatorios: 

|Encabezado de solicitud|DESCRIPCIÓN|  
|--------------------|-----------------|  
|*Content-Type:*|Necesario. Establézcalo en `application/json`.|  
|*Authorization:*|Necesario. Establézcalo en un [token de acceso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` válido. |  

## <a name="response"></a>Response  

Se devuelve el código de estado 200 (OK) en el caso de una respuesta correcta, que contiene una lista de tareas recomendadas para proteger los recursos de Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Cada elemento de **value** representa una recomendación:

|Propiedad Response|DESCRIPCIÓN|
|----------------|----------|
|**state** | Indica si la recomendación está `active` o `resolved`. |
|**creationTimeUtc** | Fecha y hora, en UTC, que muestra cuándo se creó la recomendación. |
|**lastStateChangeUtc** | Fecha y hora, en UTV, del último cambio de estado, si lo hay. |
|**securityTaskParameters** | Detalles de la recomendación; las propiedades varían según la recomendación subyacente. |
||
  
Para obtener recomendaciones actualmente compatibles, consulte [Implementar recomendaciones de seguridad](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Otros códigos de estado indican condiciones de error. En estos casos, el objeto de respuesta incluye una descripción que explica por qué se ha producido un error en la solicitud.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Respuesta de ejemplo  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Esta respuesta muestra dos recomendaciones; cada elemento de la lista corresponde a una recomendación concreta. La primera recomienda cifrar el almacenamiento en una máquina virtual Linux y la segunda sugiere que habilite la auditoría para un servidor SQL Server.

Las recomendaciones varían según las directivas que haya habilitado. Para obtener más información, incluidas las recomendaciones disponibles actualmente, consulte [Administración de recomendaciones de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Otras referencias  
- [Configuración de directivas de seguridad](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Azure Security Resource provider REST API](https://msdn.microsoft.com/library/azure/mt704034.aspx) (API REST del proveedor de recursos de seguridad de Azure)   
- [Get started with Azure REST API](https://docs.microsoft.com/rest/api/azure/) (Introducción a la API REST de Azure)   
- [Azure Security Center PowerShell module](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22) (Módulo de PowerShell de Azure Security Center)
