---
title: Configuración de ubicaciones con nombre en Azure Active Directory | Microsoft Docs
description: Aprenda a configurar ubicaciones con nombre.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3b7bd6f4bea111815f647af09ebaa868696b25bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Configuración de ubicaciones con nombre en Azure Active Directory

Con las ubicaciones con nombre, puede etiquetar intervalos de direcciones IP de confianza en su organización. Azure Active Directory usa las ubicaciones con nombre en el contexto de:

- La detección de [eventos de riesgo](active-directory-reporting-risk-events.md) para reducir el número de falsos positivos notificados.  

- El [acceso condicional basado en la ubicación](active-directory-conditional-access-locations.md).


En este artículo se explica cómo puede configurar las ubicaciones con nombre en su entorno.


## <a name="entry-points"></a>Puntos de entrada

Puede tener acceso a la página de configuración de la ubicación con nombre en la sección **Seguridad** de la página de Azure Active Directory haciendo clic en:

![Puntos de entrada](./media/active-directory-named-locations/34.png)

- **Acceso condicional:**

    - En la sección **Administrar**, haga clic en **Ubicaciones con nombre**.
    
        ![El comando Ubicaciones con nombre](./media/active-directory-named-locations/06.png)

- **Inicios de sesión no seguros:**

    - En la barra de herramientas de la parte superior, haga clic en **Agregar intervalo de direcciones IP conocidas**.

       ![El comando Ubicaciones con nombre](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Ejemplo de configuración

**Para configurar una ubicación con nombre:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global.

2. En el panel izquierdo, haga clic en **Azure Active Directory**.

    ![El vínculo Azure Active Directory en el panel izquierdo](./media/active-directory-named-locations/01.png)

3. En la página **Azure Active Directory**, en la sección **Seguridad**, haga clic en **Acceso condicional**.

    ![El comando Acceso condicional](./media/active-directory-named-locations/05.png)


4. En la página **Acceso condicional**, en la sección **Administrar**, haga clic en **Ubicaciones con nombre**.

    ![El comando Ubicaciones con nombre](./media/active-directory-named-locations/06.png)


5. En la página **Ubicaciones con nombre**, haga clic en **Nueva ubicación**.

    ![El comando Nueva ubicación](./media/active-directory-named-locations/07.png)


6. En la página **Nuevo**, haga lo siguiente:

    ![La hoja Nuevo](./media/active-directory-named-locations/56.png)

    a. En el cuadro **Nombre**, escriba el nombre de la ubicación con nombre.

    b. En el cuadro **Intervalo IP**, escriba un intervalo IP. El intervalo de IP debe estar en el formato *Enrutamiento de interdominios sin clases*  (CIDR).  

    c. Haga clic en **Create**(Crear).



## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- [Condiciones de ubicación del acceso condicional de Azure Active Directory](active-directory-conditional-access-locations.md)

- [Eventos de riesgo de Azure Active Directory](active-directory-reporting-risk-events.md).

- [Informe de inicios de sesión poco seguros del portal de Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md).  
