---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206185"
---
# <a name="remove-the-sql-resource-provider"></a>Eliminación del proveedor de recursos de SQL

Para eliminar el proveedor de recursos de SQL, es esencial eliminar antes todas las dependencias:

1. Asegúrese de tener el paquete de implementación original que descargó para esta versión del adaptador del proveedor de recursos de SQL.

2. Todas las bases de datos de usuario deben eliminarse del proveedor de recursos. (Eliminar las bases de datos de usuario no elimina los datos). Los propios usuarios deben hacer esta tarea.

3. El administrador debe eliminar los servidores de hospedaje del adaptador del proveedor de recursos de SQL.

4. El administrador debe eliminar los planes que hagan referencia al adaptador del proveedor de recursos de SQL.

5. El administrador debe eliminar cualquier SKU y cuota asociadas con el adaptador del proveedor de recursos de SQL.

6. Vuelva a ejecutar el script de implementación con los siguientes elementos:
    - Parámetro -Uninstall
    - Puntos de conexión de Azure Resource Manager
    - DirectoryTenantID
    - Credenciales para la cuenta de administrador del servicio

