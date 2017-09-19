---
title: "Supervisión de identidad y acceso en Azure Security Center | Microsoft Docs"
description: Este documento le ayuda a usar la funcionalidad de identidad y acceso de Azure Security Center para supervisar los problemas relacionados con la actividad de acceso e identidad de los usuarios.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Supervisión de identidad y acceso en Azure Security Center
Este documento le ayuda a usar Azure Security Center para supervisar la identidad del usuario y la actividad de acceso.

## <a name="why-monitor-identity-and-access"></a>¿Por qué supervisar la identidad y el acceso?
Identidad debe ser el plano de control de su empresa, ya que la protección de la identidad debe ser la máxima prioridad. Mientras que en el pasado existían perímetros alrededor de las organizaciones y esos perímetros eran una de las principales limitaciones defensivas, en la actualidad, con más datos y más aplicaciones que pasan a la nube, la identidad se ha convertido en el nuevo perímetro.

Al supervisar las actividades de identidad, podrá realizar acciones proactivas antes de que se produzca un incidente o adoptar acciones reactivas para detener un intento de ataque. El panel Identidad y acceso proporciona una descripción general del estado de la identidad, incluido el número de intentos erróneos de inicio de sesión, la cuenta de usuario que se ha usado durante esos intentos, las cuentas que se han bloqueado, las cuentas en las que se ha cambiado o restablecido la contraseña y el número de cuentas que tienen una sesión iniciada actualmente.

## <a name="how-to-monitor-identity-and-access-activities"></a>¿Cómo supervisar la identidad y las actividades de acceso?
Siga los pasos siguientes para visualizar las actividades actuales relacionadas con la identidad y el acceso, para lo que debe abrir el panel **Identidad y acceso**:

1.  Abra el panel **Security Center**.
2.  En el panel izquierdo, en **Prevención**, haga clic en **Identidad y acceso**. Si tiene varias áreas de trabajo, aparece el selector de áreas de trabajo.

    ![selección de área de trabajo](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Si la última columna muestra **ACTUALIZAR PLAN** se debe a que este área de trabajo está usando la suscripción gratuita y debe actualizar a la estándar para utilizar esta característica. Si se muestra REQUIERE ACTUALIZACIÓN, debe actualizar [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esta característica. Para más información sobre el plan de precios, consulte Precios de Azure Security Center. 
    > 
3. Si tiene más de un área de trabajo para investigar, puede dar prioridad a la investigación de acuerdo con la columna **INICIOS DE SESIÓN ERRÓNEOS** que muestra el número actual de intentos de inicio de sesión erróneos en este área de trabajo. Seleccione el área de trabajo que desea usar y, a continuación, se muestra el panel **Identidad y acceso**.

    ![identidad y acceso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. La información disponible en este panel puede ayudarle de inmediato a identificar una posible actividad sospechosa. Este panel se divide en tres áreas principales:
    * **Postura de identidad**: resume las actividades relacionadas con la identidad que tienen lugar en este área de trabajo.
    * **Inicios de sesión erróneos**: le ayuda a identificar rápidamente la causa principal del intento de inicio de sesión erróneo y muestra una lista de las diez principales cuentas con más errores al intentar iniciar sesión.
    * **Inicios de sesión en el tiempo**: le ayuda a identificar rápidamente la cantidad de inicios de sesión en el tiempo y muestra una lista de los equipos principales en intentos de inicio de sesión.
    
Independientemente de qué icono se seleccione, el panel que aparece se basa en la [consulta de búsqueda de registros](https://docs.microsoft.com/azure/security-center/security-center-search) y la única diferencia es el tipo de consulta y el resultado. También puede seleccionar un elemento, por ejemplo un equipo, hacer clic en él y ver los datos pertinentes. 

## <a name="see-also"></a>Otras referencias
En este documento, ha aprendido a supervisar la identidad y el acceso en Azure Security Center. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.


