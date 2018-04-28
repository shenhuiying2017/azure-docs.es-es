---
title: Conexión a Azure Analysis Services con Excel | Microsoft Docs
description: Aprenda a conectarse a un servidor de Azure Analysis Services mediante Excel.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8414597c2c394e0b642ff47cba79c87488f56b24
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="connect-with-excel"></a>Conexión con Excel

Una vez que se ha creado un servidor en Azure y se ha implementado en él un modelo tabular, está preparado para conectarse y comenzar a explorar los datos.


## <a name="connect-in-excel"></a>Conexión en Excel

La conexión a un servidor en Excel se admite mediante Obtener datos en Excel 2016. No se admite la conexión mediante el Asistente para importar tablas en Power Pivot. 

**Conexión en Excel 2016**

1. En Excel 2016, en la cinta de opciones **Datos**, haga clic en **Obtener datos externos** > **De otros orígenes** > **Desde Analysis Services**.

2. En el Asistente para conexión de datos, en **Nombre del servidor**, escriba el nombre del servidor incluidos el protocolo y el URI. Luego, en **Credenciales de inicio de sesión**, seleccione **Utilizar el nombre de usuario y la contraseña siguientes** y escriba el nombre de usuario de la organización (por ejemplo, nancy@adventureworks.com) y la contraseña.

    > [!NOTE]
    > Si inicia sesión mediante una cuenta Microsoft, Windows Live ID, Yahoo, Gmail, etc. o le piden que utilice la autenticación multifactor para iniciar sesión, deje el campo de la contraseña en blanco. Le pedirán la contraseña cuando haga clic en Siguiente.

    ![Conexión desde el inicio de sesión de Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. En **Seleccionar base de datos y tabla**, seleccione la base de datos y el modelo o la perspectiva y haga clic en **Finalizar**.
   
    ![Conexión desde el modelo de selección de Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Otras referencias
[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Administración del servidor](analysis-services-manage.md)     


