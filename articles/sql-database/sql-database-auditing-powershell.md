---
title: "PowerShell: Administración de auditorías de Azure SQL Database | Microsoft Docs"
description: "Configure las auditorías de Azure SQL Database con PowerShell para que realicen un seguimiento de los eventos de base de datos y los escriban en un registro de auditoría en la cuenta de Azure Storage."
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
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>Configuración y administración de auditorías de SQL Database mediante PowerShell

La siguiente sección describe cómo configurar y administrar auditorías con PowerShell. Para configurar y administrar auditorías mediante Azure Portal, consulte [Configure auditing in the Azure portal](sql-database-auditing-portal.md) (Configuración de auditorías en Azure Portal). Para configurar y administrar auditorías mediante la API de REST, consulte [Configuración y administración de auditorías de SQL Database mediante la API de REST](sql-database-auditing-rest.md).

Para obtener información general sobre las auditorías, consulte [SQL Database auditing](sql-database-auditing.md) (Realización de auditorías de SQL Database).

## <a name="powershell-cmdlets"></a>Cmdlets de PowerShell

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>Pasos siguientes

* Para configurar y administrar auditorías mediante Azure Portal, consulte [Configure database auditing in the Azure portal](sql-database-auditing-portal.md) (Configuración de auditorías de bases de datos en Azure Portal). 
* Para configurar y administrar auditorías mediante PowerShell, consulte [Configuración y administración de auditorías de SQL Database mediante la API de REST](sql-database-auditing-rest.md).
* Para obtener información general sobre las auditorías, consulte [Database auditing](sql-database-auditing.md) (Realización de auditorías de SQL Database).


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

