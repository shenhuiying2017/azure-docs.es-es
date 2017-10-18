---
title: Reglas de alerta personalizadas en Azure Security Center  | Microsoft Docs
description: Este documento le ayuda a crear reglas de alerta personalizadas en Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: 10c5c3e6f714aac4c08cb810f54eb09c3d2bbe70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Reglas de alerta personalizadas en Azure Security Center (versión preliminar)
Este documento le ayuda a crear reglas de alerta personalizadas en Azure Security Center.

## <a name="what-are-custom-alert-rules-in-security-center"></a>¿Qué son las reglas de alerta personalizadas en Security Center?

Security Center cuenta con un conjunto de [alertas de seguridad](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) predefinidas, que se desencadenan cuando tiene lugar una amenaza o actividad sospechosa. En algunas situaciones, puede que quiera crear una alerta personalizados para satisfacer las necesidades específicas de su entorno. 

Las reglas de alerta personalizadas de Security Center le permiten definir nuevas alertas de seguridad basadas en los datos ya recopilados del entorno. Puede crear consultas y el resultado de estas consultas se puede usar como criterio para la regla personalizada, de forma que, cuando se cumplan los criterios, se ejecute la regla. Puede usar eventos de seguridad de equipos, registros de soluciones de seguridad del asociado o datos ingeridos mediante las API para crear consultas personalizadas. 

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>¿Cómo se crea una regla de alerta personalizada en Security Center?

Abra el panel **Security Center** y siga estos pasos para crear una regla de alerta personalizada:

1.  En el panel izquierdo, en **Detección**, haga clic en **Custom alert rules (Preview)** (Reglas de alerta personalizadas [versión preliminar]).
2.  En la página **Security Center – Custom alert rules (Preview)** (Security Center – Reglas de alerta personalizadas [versión preliminar]), haga clic en **Nueva regla de alertas personalizada**.

    ![Alerta personalizada](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)
    
3.  Aparece la página Creación de reglas de alertas personalizadas con las siguientes opciones:
    
    ![Crear](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Escriba el nombre de esta regla personalizada en el campo **Nombre**.
5.  Escriba una breve descripción que refleje el propósito de esta regla en el campo **Descripción**.
6.  Seleccione el nivel de gravedad (alta, media, baja) según sus necesidades en el campo **Gravedad**.
7.  Seleccione la suscripción en la que esta regla es aplicable en el campo **Suscripción**.
8.  Seleccione el área de trabajo que quiere supervisar con esta regla en el campo **Área de trabajo** y, en el campo **Consulta de búsqueda**, la consulta que se usa para obtener los resultados. El resultado de la consulta desencadena la alerta. Tenga en cuenta que cuando se escribe una consulta válida, aparece la marca de verificación verde en la esquina derecha de este campo:

    ![Consultar](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Seleccione el intervalo de tiempo en el que se ejecutará la consulta anterior en el campo **Período**. Observe que el resultado de la búsqueda de la parte inferior de este campo cambia de acuerdo con el intervalo de tiempo que seleccione.

    ![Período](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. En el campo **Evaluación**, seleccione la frecuencia con la que se debe evaluar y ejecutar esta regla.
12. En el campo **Número de resultados**, seleccione el operador (mayor que o menor que).
13. En el campo **Umbral**, escriba un número que se usará como referencia para el operador seleccionado anteriormente.
14. Habilite la opción **Suprimir alertas** si quiere establecer un tiempo de espera antes de que Security Center envíe otra alerta para esta regla.
15. Haga clic en **Aceptar** para finalizar.

Cuando termine de crear la nueva regla de alerta, aparecerá en la lista de reglas de alerta personalizadas. Una vez que se cumplen las condiciones de esa regla, se desencadena una nueva alerta, que puede ver en el panel **Alertas de seguridad**.

![Alerta](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Tenga en cuenta que los parámetros (consulta de búsqueda, umbral, etc.) que se establecieron durante la creación de la regla están disponibles en la alerta para esta regla personalizada.

## <a name="see-also"></a>Otras referencias
En este documento, ha aprendido a crear una regla de alerta personalizada en Azure Security Center. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

