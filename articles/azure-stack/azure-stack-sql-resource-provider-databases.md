---
title: Uso de las bases de datos proporcionadas por el RP del adaptador de SQL en Azure Stack | Microsoft Docs
description: "Cómo crear y administrar las bases de datos SQL aprovisionadas con el proveedor de recursos del adaptador de SQL"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 0cc08c37e879b00f8cd9a4046a4c81c55dab167c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-databases"></a>Creación de bases de datos SQL

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Las bases de datos de autoservicio se proporcionan a través del portal de usuarios. Un usuario necesita una suscripción que tenga una oferta, que contiene el servicio de base de datos.

1. Inicie sesión en el portal de usuarios de [Azure Stack](azure-stack-poc.md) (los administradores de servicios también pueden usar el portal de administración).

2. Haga clic en **+ Nuevo** &gt; **Datos y almacenamiento "** &gt; **SQL Server Datbase (versión preliminar)** &gt; **Agregar**.

3. Rellene el formulario con los detalles de la base de datos, incluidos el **Nombre de base de datos** y el **Tamaño máximo**, y cambie los otros parámetros según sea necesario. Se le pide que elija una SKU para la base de datos. A medida que se agregan servidores de hospedaje, se les asigna una SKU. Las bases de datos se crean en ese grupo de servidores de hospedaje que componen la SKU.

  ![Nueva base de datos](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > El tamaño de la base de datos debe ser de al menos 64 MB. Puede aumentarse con la configuración.

4. Rellene la configuración de inicio de sesión: **Inicio de sesión de base de datos** y **Contraseña**. Estas opciones de configuración son la credencial de Autenticación de SQL que se crea solo para el acceso a esta base de datos. El nombre de usuario de inicio de sesión debe ser único globalmente. Cree una nueva configuración de inicio de sesión o seleccione una existente. Puede volver a usar la configuración de inicio de sesión para otras bases de datos con la misma SKU.

    ![Creación de un nuevo inicio de sesión en la base de datos](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Envíe el formulario y espere a que la implementación se complete.

    En la hoja resultante, observe el campo "Cadena de conexión". Puede usar esa cadena en cualquier aplicación que requiera acceso a SQL Server (por ejemplo, una aplicación web) en Azure Stack.

    ![Recuperación de la cadena de conexión](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>Eliminación de bases de datos SQL
En el portal:

>[!NOTE]
>
>Cuando se elimina una base de datos SQL AlwaysOn desde el RP, se elimina correctamente del grupo de disponibilidad AlwaysOn y principal, pero, por diseño, el grupo de disponibilidad SQL coloca la base de datos en estado de restauración en cada réplica y no la elimina a menos que se desencadene. Si no se elimina una base de datos, las réplicas secundarias pasan al estado Sin sincronizar. Sigue siendo posible volver a agregar una base de datos nueva al grupo de disponibilidad con el mismo RP. Consulte ![Eliminación de una base de datos secundaria](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Administración de las credenciales de base de datos
Puede actualizar las credenciales de base de datos (configuración de inicio de sesión).

## <a name="verify-sql-alwayson-databases"></a>Comprobación de las bases de datos SQL AlwaysOn
Las bases de datos AlwaysOn deben mostrarse como sincronizadas y disponibles en todas las instancias y en el grupo de disponibilidad. Después de la conmutación por error, la base de datos debe conectarse sin problemas. Puede utilizar SQL Server Management Studio para comprobar que una base de datos está sincronizando:

![Comprobación de AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
