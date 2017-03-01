---
title: "API de REST: Administración de auditorías de Azure SQL Database | Microsoft Docs"
description: "Configure las auditorías de Azure SQL Database mediante la API de REST para que realicen un seguimiento de los eventos de base de datos y los escriban en un registro de auditoría en la cuenta de Azure Storage."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>Configuración y administración de auditorías de SQL Database mediante la API de REST

Este tema describe cómo configurar y administrar auditorías mediante la API de REST. Para configurar y administrar auditorías mediante Azure Portal, consulte [Configure auditing in the Azure portal](sql-database-auditing-portal.md) (Configuración de auditorías en Azure Portal). Para configurar y administrar auditorías mediante PowerShell, consulte [Configuración y administración de auditorías de SQL Database con PowerShell](sql-database-auditing-powershell.md).

Para obtener información general sobre las auditorías, consulte [SQL Database auditing](sql-database-auditing.md) (Realización de auditorías de SQL Database).

## <a name="rest-api---blob-auditing"></a>API de REST, auditoría de blobs

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx) (Creación o actualización de la directiva de auditoría de blobs de la base de datos)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx) (Creación o actualización de la directiva de audioría de blobs del servidor)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx) (Obtención de la directiva de auditoría de bobs de la base de datos)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx) (Obtención de la directiva de auditoría de blobs del servidor)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx) (Obtención de resultados de funcionamiento de la auditoría de blobs del servidor)


## <a name="rest-api---table-auditing"></a>API de REST, auditoría de tablas

   * [Create or Update Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604471.aspx) (Creación o actualización de la directiva de auditoría de la base de datos)
   * [Create or Update Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604383.aspx) (Creación o actualización de la directiva de auditoría del servidor)
   * [Get Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604381.aspx) (Obtención de la directiva de auditoría de la base de datos)
   * [Get Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604382.aspx) (Obtención de la directiva de auditoría del servidor)

## <a name="next-steps"></a>Pasos siguientes

* Para configurar y administrar auditorías mediante Azure Portal, consulte [Configure database auditing in the Azure portal](sql-database-auditing-portal.md) (Configuración de auditorías de bases de datos en Azure Portal). 
* Para configurar y administrar auditorías mediante PowerShell, consulte [Configuración y administración de auditorías de SQL Database mediante PowerShell](sql-database-auditing-powershell.md).
* Para obtener información general sobre las auditorías, consulte [Database auditing](sql-database-auditing.md) (Realización de auditorías de SQL Database).
