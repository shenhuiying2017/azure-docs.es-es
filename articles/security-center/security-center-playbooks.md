---
title: Playbook de seguridad en Azure Security Center | Microsoft Docs
description: Este documento le ayuda a usar los playbooks de seguridad de Azure Security Center para automatizar la respuesta a incidentes de seguridad.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: 15257e6ee8744b11fd3965e365cf4fb0e1d429ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Playbook de seguridad de Azure Security Center (versión preliminar)
Este documento le ayuda a usar los playbooks de seguridad de Azure Security Center para responder a problemas relacionados con la seguridad.

## <a name="what-is-security-playbook-in-security-center"></a>¿Qué son los playbooks de seguridad de Security Center?
Los playbooks de seguridad son una colección de procedimientos que se pueden ejecutar desde Security Center cuando un determinado playbook se desencadena desde la alerta seleccionada. Pueden ayudarle a automatizar y orquestar la respuesta a determinadas alertas de seguridad detectadas por Security Center. Los playbooks de seguridad de Security Center se basan en [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), lo que significa que puede usar las plantillas que se proporcionan en la categoría de seguridad de las plantillas de Logic Apps, puede modificarlas en función de sus necesidades o puede crear nuevos playbooks mediante el [flujo de trabajo de Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) y usar Security Center como desencadenador. 

> [!NOTE]
> Los playbooks aprovechan Azure Logic Apps, por lo tanto, se aplican cargos. Visite la página de precios de [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para más información. 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>¿Cómo se crea un playbook de seguridad desde Security Center?
Para crear un nuevo playbook de seguridad desde Security Center, siga estos pasos:

1.  Abra el panel **Security Center**.
2.  En la sección **Automation & Orchestration** (Automatización y orquestación) del panel izquierdo, haga clic en **Playbooks (Preview)** (Playbooks [versión preliminar]).

    ![Aplicación lógica](./media/security-center-playbooks/security-center-playbooks-fig1.png)
 
3. En la página **Security Center - Playbooks (Preview)** (Security Center - Playbooks [versión preliminar]), haga clic en el botón **Agregar**.

    ![Creación de la aplicación lógica](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. En la página **Crear aplicación lógica**, escriba la información solicitada para crear la nueva aplicación lógica y haga clic en el botón **Crear**. Finalizada la operación, el nuevo playbook aparece en la lista. Si no aparece, haga clic en el botón **Actualizar**. Cuando lo vea, haga clic en él para empezar a editarlo.

    ![Creación de la aplicación lógica](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Aparece el **Diseñador de aplicación lógica**. Haga clic en **Aplicación lógica en blanco** para crear un nuevo playbook. También puede seleccionar **Seguridad** en las categorías y usar una de las plantillas.
    
    ![Diseñador de aplicación lógica](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. En el campo **Buscar todos los conectores y desencadenadores**, escriba *Azure Security Center* y seleccione **Cuando se desencadena una respuesta a una alerta de Azure Security Center**.

    ![Desencadenador](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Ahora puede definir lo que ocurre cuando se desencadena el playbook. Puede agregar una acción, una condición lógica, condiciones de casos de conmutador o bucles.

    ![Diseñador de aplicación lógica](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>¿Cómo se ejecuta un playbook de seguridad en Security Center?

Ejecute un playbook de seguridad en Security Center cuando quiera orquestar los procesos, obtener más información de otros servicios o aplicar correcciones. Para acceder a los playbooks, siga estos pasos:

1.  Abra el panel **Security Center**.
2.  En el panel izquierdo, en **Detección de amenazas**, haga clic en **Security incidents & alerts** (Incidentes y alertas de seguridad).

    ![Alertas](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Haga clic en la alerta que quiere investigar.
4.  En la parte superior de la página de la alerta, haga clic en el botón **Ejecutar playbooks**.

    ![Ejecución de playbooks](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. En la página Playbooks, seleccione el playbook que quiere ejecutar y haga clic en el botón **Ejecutar**. Si quiere ver el playbook antes de desencadenarlo, puede hacer clic en él y se abrirá el diseñador.

    ![Playbooks](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Historial

Después de ejecutar el playbook, también puede acceder a ejecuciones anteriores y a pasos que contienen más información sobre el estado de los playbooks ejecutados anteriormente. El historial se contextualiza por alerta, lo que significa que el historial de playbooks que ve en esta página se correlaciona con la alerta que desencadenó este playbook. 

![Historial](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Para ver más información sobre la ejecución de un determinado playbook, haga clic en el playbook en cuestión y aparecerá la página de ejecución de aplicaciones lógicas con el flujo de trabajo entero.

![Detalles](./media/security-center-playbooks/security-center-playbooks-fig14.png)

En este flujo de trabajo puede ver el tiempo que tardó en ejecutarse cada tarea y puede expandir cada tarea para ver el resultado. 

### <a name="changing-an-existing-playbook"></a>Cambio de un playbook existente

Puede cambiar un playbook existente en Security Center para agregar una acción o condiciones. Para ello, solo debe hacer clic en el nombre del playbook que quiere cambiar, en la pestaña Playbooks, y se abrirá el Diseñador de aplicación lógica.

> [!NOTE]
> Para más información sobre cómo crear su propio playbook mediante una aplicación lógica de Azure, lea [Creación de su primer flujo de trabajo de aplicación lógica para automatizar los procesos entre aplicaciones de nube y servicios en la nube](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger).


## <a name="see-also"></a>Otras referencias
En este documento, ha aprendido a usar playbooks en Azure Security Center. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

