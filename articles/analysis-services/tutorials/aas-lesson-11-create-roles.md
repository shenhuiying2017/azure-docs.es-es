---
title: "Lección 11 del tutorial de Azure Analysis Services: Creación de roles | Microsoft Docs"
description: "Describe cómo crear roles en el proyecto del tutorial de Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: 0dfcb9d9fc8cd32f95c5097ec653864364b27bcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-11-create-roles"></a>Lección 11: Creación de roles

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

En esta lección, creará roles. Los roles proporcionan seguridad para los objetos y los datos de la base de datos modelo, ya que solo permiten el acceso a los usuarios que son miembros del rol. Cada rol se define con un permiso único: Ninguno, Lectura, Lectura y procesamiento, Procesamiento o Administrador. Los roles se pueden definir durante la creación del modelo mediante el Administrador de roles. Una vez implementado un modelo, puede administrar roles mediante SQL Server Management Studio (SSMS). Para obtener más información, vea [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> No es necesario crear roles para completar este tutorial. De forma predeterminada, la cuenta en la que ha iniciado sesión actualmente tiene privilegios de administrador en el modelo. Pero para que otros usuarios de la organización naveguen mediante el uso de un cliente de creación de informes, debe crear al menos un rol con permisos de lectura y agregar a los usuarios como miembros.  
  
Se crean tres roles:  
  
-   **Director de ventas**: este rol puede incluir a los usuarios de la organización que quiere que tengan permiso de lectura en todos los objetos y datos del modelo.  
  
-   **Analista de ventas de EE. UU.**: este rol puede incluir a los usuarios de la organización que quiere que solo puedan examinar los datos relacionados con las ventas en Estados Unidos. Para este rol se usa una fórmula DAX para definir un *filtro de fila* que hace que los miembros solo puedan examinar los datos de Estados Unidos.  
  
-   **Administrador**: este rol puede incluir a los usuarios que quiere que tengan permisos de administrador, lo que permite un acceso ilimitado y permisos para realizar tareas administrativas en la base de datos modelo.  
  
Dado que las cuentas de usuario y de grupo de Windows de la organización son únicas, puede agregar cuentas de su propia organización a los miembros, Pero para este tutorial también puede dejar los miembros en blanco. Más adelante en la Lección 12: Analizar en Excel se prueba el efecto de cada rol.  
  
Tiempo estimado para completar esta lección: **15 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 10: Creación de particiones](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Creación de roles  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Para crear el rol de usuario Director de ventas  
  
1.  En el Explorador de modelos tabulares, haga clic con el botón derecho en **Roles** > **Roles**.  
  
2.  En el Administrador de roles, haga clic en **Nuevo**.  
  
3.  Haga clic en el nuevo rol y en la columna **Nombre**, cambie el nombre del rol a **Jefe de ventas**.  
  
4.  En la columna **Permisos**, haga clic en la lista desplegable y, luego, seleccione el permiso **Lectura**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Opcional: Haga clic en la pestaña **Miembros** y, luego, haga clic en **Agregar**. En el cuadro de diálogo **Seleccionar usuarios o grupos**, escriba los usuarios o grupos de Windows de la organización que quiere incluir en el rol.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Para crear el rol de usuario Analista de ventas de EE. UU.  
  
1.  En el Administrador de roles, haga clic en **Nuevo**.    
  
2.  Cambie el nombre del rol a **Analista de ventas de EE. UU.**  
  
3.  Asigne a este rol el permiso **Lectura**.  
  
4.  Haga clic en la pestaña Filtros de fila y, únicamente para la tabla **DimGeography**, escriba la fórmula siguiente en la columna Filtro DAX:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Una fórmula de filtro de fila debe resolverse en un valor booleano (TRUE/FALSE). Con esta fórmula se especifica que el usuario solo puede ver las filas con el valor "US" (EE. UU.) para el código de país o región.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Opcional: Haga clic en la pestaña **Miembros** y, luego, haga clic en **Agregar**. En el cuadro de diálogo **Seleccionar usuarios o grupos**, escriba los usuarios o grupos de Windows de la organización que quiere incluir en el rol.  
  
#### <a name="to-create-an-administrator-user-role"></a>Para crear el rol de usuario Administrador  
  
1.  Haga clic en **Nuevo**.  
  
2.  Cambie el nombre del rol a **Administrador**.  
  
3.  Asigne a este rol el permiso **Administrador**.  
  
4.  Opcional: Haga clic en la pestaña **Miembros** y, luego, haga clic en **Agregar**. En el cuadro de diálogo **Seleccionar usuarios o grupos**, escriba los usuarios o grupos de Windows de la organización que quiere incluir en el rol. 
  
  
## <a name="whats-next"></a>Pasos siguientes
[Lección 12: Analizar en Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)

  
  
