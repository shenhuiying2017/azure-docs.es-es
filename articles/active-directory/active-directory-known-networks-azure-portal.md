---
title: Redes designadas en Azure Active Directory | Microsoft Docs
description: "Mediante la configuración de redes designadas, puede evitar que direcciones IP que son propiedad de su organización generen la creación de falsos positivos de &quot;inicios de sesión desde varias ubicaciones geográficas&quot; e &quot;inicios de sesión desde direcciones IP con actividad sospechosa&quot;."
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
ms.date: 01/17/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Redes designadas en Azure Active Directory

> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-known-networks-azure-portal.md)
> * [Portal de Azure clásico](active-directory-known-networks.md)
>
>


Azure Active Directory crea un registro para cada [evento de riesgo](active-directory-identity-protection-risk-events.md) que se detecta. Mediante la información de eventos de riesgo disponible en los informes de seguridad de Azure Active Directory, puede conocer la probabilidad de que haya cuentas de usuario en peligro en su entorno.   

Es posible que Azure Active Directory detecte falsos positivos de los [tipos de eventos de riesgo](active-directory-reporting-risk-events.md#risk-event-types) conocidos como *viajes imposibles a ubicaciones inusuales* e *inicios de sesión desde direcciones IP con actividad sospechosa* de direcciones IP que realmente pertenecen a su organización. 

Por ejemplo, esto puede ocurrir cuando:

- Un usuario de la oficina de Boston que ha iniciado sesión de forma remota en el centro de datos en San Francisco genera un evento de riesgo del tipo *Inicios de sesión desde varias ubicaciones geográficas*.

- Un usuario de la organización que intenta iniciar sesión varias veces con una contraseña incorrecta genera un evento de riesgo del tipo *Inicios de sesión desde direcciones IP con actividad sospechosa*.

Para evitar estos casos de generación de eventos de riesgo engañosos, debe agregar intervalos de direcciones IP designadas a la lista de direcciones IP públicas de la organización.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para ello, realice los pasos siguientes:

1. Inicie sesión en el Portal de administración de Azure.

2. En el panel izquierdo, haga clic en **Active Directory**.

    ![Redes conocidas](./media/active-directory-known-networks-azure-portal/01.png)

3. En la hoja de directorio, en la sección **Administrar**, haga clic en **Named networks** (Redes designadas).

    ![Redes conocidas](./media/active-directory-known-networks-azure-portal/02.png)


4. Haga clic en **Agregar ubicación**

    ![Redes conocidas](./media/active-directory-known-networks-azure-portal/03.png)

5. En la hoja **Agregar** haga lo siguiente:

    ![Redes conocidas](./media/active-directory-known-networks-azure-portal/04.png)

    a. En el cuadro de texto **Nombre**, escriba un nombre.

    b. En el cuadro de texto **Intervalo IP**, escriba un intervalo IP. El intervalo IP debe estar en formato CIDR. Para actualizar de forma masiva, puede cargar un archivo CSV con los intervalos IP. Una carga permite agregar los intervalos IP del archivo a la lista en lugar de sobrescribir la lista.

    c. Haga clic en **Crear**.


**Recursos adicionales:**

* [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)
* [Inicios de sesión desde direcciones IP con actividad sospechosa](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Inicios de sesión desde varias ubicaciones geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)

