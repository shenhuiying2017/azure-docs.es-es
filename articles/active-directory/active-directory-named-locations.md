---
title: Ubicaciones con nombre en Azure Active Directory | Microsoft Docs
description: "Al configurar ubicaciones con nombre, puede evitar el hecho de tener direcciones IP pertenecientes a su organización que generan falsos positivos para el tipo de evento de riesgo Viaje imposible a ubicaciones inusuales."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9637c56cf88ccf4c54fda789d4e7e3ca3e0a4fed
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="named-locations-in-azure-active-directory"></a>Ubicaciones con nombre en Azure Active Directory

Ubicaciones con nombre es una característica de Azure Active Directory que permite etiquetar intervalos de direcciones IP de confianza en sus organizaciones. En su entorno, puede usar ubicaciones con nombre en el contexto de la detección de [eventos de riesgo](active-directory-reporting-risk-events.md) a fin de reducir el número de falsos positivos notificados para el tipo de evento de riesgo *Viaje imposible a ubicaciones inusuales*. 




## <a name="configuration"></a>Configuración

**Para configurar una ubicación con nombre:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global.

2. En el panel izquierdo, haga clic en **Azure Active Directory**.

    ![Ubicaciones con nombre](./media/active-directory-named-locations/01.png)

3. En la hoja **Azure Active Directory**, en la sección **Seguridad**, haga clic en **Acceso condicional**.

    ![Ubicaciones con nombre](./media/active-directory-named-locations/05.png)


4. En la hoja **Acceso condicional**, en la sección **Administrar**, haga clic en **Ubicaciones con nombre**.

    ![Ubicaciones con nombre](./media/active-directory-named-locations/06.png)


5. En la hoja **Ubicaciones con nombre**, en el menú de la parte superior, haga clic en **New locations** (Nuevas ubicaciones).

    ![Ubicaciones con nombre](./media/active-directory-named-locations/07.png)


6. En la hoja **Nuevo**, siga estos pasos:

    ![Ubicaciones con nombre](./media/active-directory-named-locations/08.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre de la ubicación con nombre.

    b. En el cuadro de texto **Intervalo IP**, escriba un intervalo IP. El intervalo de IP debe estar en el formato *Enrutamiento de interdominios sin clases*  (CIDR).  

    c. Haga clic en **Crear**.



## <a name="what-you-should-know"></a>Qué debería saber

**Actualizaciones masivas**: al crear o actualizar ubicaciones con nombre, en el caso de las actualizaciones masivas, puede cargar o descargar un archivo CSV con los intervalos de IP. Una carga permite agregar los intervalos IP del archivo a la lista en lugar de sobrescribir la lista.

![Ubicaciones con nombre](./media/active-directory-named-locations/09.png)


**Limitaciones:** puede definir un máximo de 60 ubicaciones con nombre con un intervalo de IP asignado a cada uno de ellas. Si tiene una sola ubicación con nombre configurada, puede definir hasta 500 intervalos IP para ella.


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre:

- Para más información, consulte la página sobre [eventos de riesgo de Azure Active Directory](active-directory-reporting-risk-events.md).


