---
title: "Supervisión de identidad y acceso en Azure Security Center | Microsoft Docs"
description: Aprenda a usar la funcionalidad de identidad y acceso de Azure Security Center para supervisar los problemas relacionados con la actividad de acceso e identidad de los usuarios.
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Supervisión de identidad y acceso en Azure Security Center
Este artículo le ayuda a usar Azure Security Center para supervisar la identidad y la actividad de acceso del usuario.

## <a name="why-monitor-identity-and-access"></a>¿Por qué supervisar la identidad y el acceso?
La identidad debe ser el plano de control de la empresa y protegerla debe ser la prioridad máxima. En el pasado, había perímetros alrededor de las organizaciones y esos perímetros eran uno de los principales límites defensivos. Hoy en día, donde cada vez más datos y aplicaciones se mueven a la nube, la identidad se convierte en el nuevo perímetro.

Mediante la supervisión de las actividades de identidad, puede realizar acciones proactivas antes de que se produzca un incidente o acciones reactivas para detener un intento de ataque. El panel de identidad y acceso proporciona una visión general de su estado de identidad, lo que incluye:

* El número de intentos infructuosos de inicio de sesión. 
* Las cuentas de los usuarios que se usaron durante esos intentos.
* Las cuentas que se han bloqueado.
* Las cuentas con la contraseña cambiada o restablecida. 
* El número actual de cuentas en las que se ha iniciado sesión.

## <a name="monitor-identity-and-access-activities"></a>Supervisión de las actividades de identidad y acceso
Para ver las actividades actuales relacionadas con la identidad y el acceso, es necesario acceder al panel **Identity & Access** (Identidad y acceso).

1. Abra el panel **Security Center**.

2. En el panel izquierdo, en **Prevención**, seleccione **Identity & Access** (Identidad y acceso). Si tiene varias áreas de trabajo, aparece el selector de áreas de trabajo.

    ![Selección del área de trabajo](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Si en la columna de la derecha se muestra **UPGRADE PLAN** (ACTUALIZAR PLAN), esta área de trabajo está usando la suscripción gratuita. Para usar esta característica, actualice a la suscripción estándar. Si en columna de la derecha se muestra **REQUIRES UPDATE** (REQUIERE ACTUALIZACIÓN), actualice [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esta característica. Para más información sobre el plan de precios, consulte Precios de Security Center. 
    > 
3. Si tiene más de un área de trabajo para investigar, puede priorizar la investigación de acuerdo con la columna **INICIOS DE SESIÓN CON ERRORES**. En ella se muestra el número actual de intentos de inicio de sesión infructuosos en esta área de trabajo. Seleccione el área de trabajo que quiere usar y el panel **Identity & Access** (Identidad y acceso) aparecerá a continuación.

    ![Identidad y acceso](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. La información disponible en este panel puede ayudarle de inmediato a identificar una posible actividad sospechosa. El panel se divide en tres áreas principales:

    a. **Posición de identidad**. Resume las actividades relacionadas con la identidad que tienen lugar en esta área de trabajo.

    b. **Inicios de sesión con errores**. Le ayuda a identificar rápidamente la causa principal de los intentos de inicio de sesión con errores. Muestra una lista de las 10 cuentas principales con error en la mayoría de los intentos de inicio de sesión.

    c. **Inicios de sesión a lo largo del tiempo**. Le ayuda a identificar rápidamente el número de inicios de sesión a lo largo del tiempo. Muestra una lista de los principales intentos de inicio de sesión en la cuenta del equipo.
    
Con independencia del icono que seleccione, el panel que aparece se basa en la consulta de búsqueda de registros. La única diferencia es el tipo de consulta y el resultado. También puede seleccionar un elemento, por ejemplo un equipo, y ver los datos de interés. 

## <a name="see-also"></a>Otras referencias
En este documento, ha aprendido a supervisar la identidad y el acceso en Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Aprenda a administrar las alertas y a responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Obtenga respuestas a las preguntas más frecuentes acerca de Security Center.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

