---
title: Cifrado de datos transparente en SQL Data Warehouse (Portal)| Microsoft Docs
description: Cifrado de datos transparente (TDE) en SQL Data Warehouse
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 9c4abb0416acc656a4cfae332377c398260191de
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524234"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Introducción al cifrado de datos transparente (TDE) en SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Información general sobre seguridad](sql-data-warehouse-overview-manage-security.md)
> * [Autenticación](sql-data-warehouse-authentication.md)
> * [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Permisos necesarios
Para habilitar el Cifrado de datos transparente (TDE), debe ser un administrador o un miembro del rol dbmanager.

## <a name="enabling-encryption"></a>Habilitar el cifrado
Para habilitar TDE para un SQL Data Warehouse, siga estos pasos:

1. Abra la base de datos en el [Portal de Azure](https://portal.azure.com)
2. En la hoja de la base de datos, haga clic en el botón **Configuración**
3. Seleccione la opción **Cifrado de datos transparente** . ![][1]
4. Seleccione el valor **Activado**.![][2]
5. Seleccione **Guardar**.
   ![][3]  

## <a name="disabling-encryption"></a>Deshabilitar el cifrado
Para deshabilitar TDE para un SQL Data Warehouse, siga estos pasos:

1. Abra la base de datos en el [Portal de Azure](https://portal.azure.com)
2. En la hoja de la base de datos, haga clic en el botón **Configuración**
3. Seleccione la opción **Cifrado de datos transparente** . ![][1]
4. Seleccione el valor **Desactivado**.![][4]
5. Seleccione **Guardar**.
   ![][5]  

## <a name="encryption-dmvs"></a>DMV de cifrado
El cifrado se puede confirmar con las siguientes DMV:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
