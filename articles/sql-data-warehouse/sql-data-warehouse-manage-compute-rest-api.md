---
title: "Pausa, reanudación y escalado con REST en Azure SQL Data Warehouse | Microsoft Docs"
description: Administre la potencia de proceso en SQL Data Warehouse mediante las API REST.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/13/2018
ms.author: barbkess
ms.openlocfilehash: cb5b6221a5fc1d02ed1d93d56fd3db4858923307
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>API REST para Azure SQL Data Warehouse
API REST para administrar los procesos en Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Escalado de proceso
Para cambiar las unidades de almacenamiento de datos, use la API REST para [crear o actualizar la base de datos](/rest/api/sql/databases/createorupdate). En el ejemplo siguiente se establecen las unidades de almacenamiento de datos en DW1000 para la base de datos MySQLDW, que está hospedada en el servidor MyServer. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pausa del proceso

Para pausar una base de datos, use la API REST para [pausar la base de datos](/rest/api/sql/databases/pause). El siguiente ejemplo pausa una base de datos denominada Database02 que está hospedada en un servidor llamado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Reanudación del proceso

Para iniciar una base de datos, use la API REST para [reanudar la base de datos](/rest/api/sql/databases/resume). El siguiente ejemplo inicia una base de datos denominada Database02 que está hospedada en un servidor llamado Server01. El servidor está en un grupo de recursos de Azure denominado ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Comprobar el estado de la base de datos

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>pasos siguientes
Para otras tareas de administración, consulte [Información general de administración](./sql-data-warehouse-overview-manage.md).

