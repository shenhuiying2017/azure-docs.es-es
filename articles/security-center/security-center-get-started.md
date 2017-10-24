---
title: "Guía de inicio rápido de Azure Security Center | Microsoft Docs"
description: "Este artículo le ayuda a empezar a trabajar rápidamente con Azure Security Center, guiándole en los componentes de supervisión de la seguridad y administración de directivas y ofreciendo vínculos a los pasos siguientes."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Guía de inicio rápido de Azure Security Center
Este artículo le ayuda a comenzar rápidamente con Azure Security Center guiándole por los componentes de supervisión de la seguridad y administración de directivas de Security Center.

## <a name="prerequisites"></a>Requisitos previos
Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

El nivel Gratis de Security Center se habilita automáticamente en todas las suscripciones de Azure y proporciona recomendaciones de directivas de seguridad, de evaluación continua de la seguridad y de seguridad que requieren acción para que pueda proteger sus recursos de Azure.

Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Para más información sobre Azure Portal, consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Permisos
En Security Center, solo se muestra información relacionada con un recurso cuando tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o grupo de recursos al que pertenece un recurso. Consulte [Permisos en Azure Security Center](security-center-permissions.md) para más información sobre los roles y las acciones permitidas en Security Center.

## <a name="data-collection"></a>Colección de datos
Security Center recopila datos de las máquinas virtuales de Azure y de los equipos que no son de Azure para supervisar las amenazas y vulnerabilidades de seguridad. Los datos se recopilan con Microsoft Monitoring Agent, que lee distintas configuraciones relacionadas con la seguridad y distintos registros de eventos de la máquina y copia los datos en el área de trabajo para analizarlos. Security Center aprovisiona Microsoft Monitoring Agent en todas las máquinas virtuales de Azure compatibles y en las que se creen. Consulte el artículo sobre cómo [habilitar la recopilación de datos](security-center-enable-data-collection.md) para obtener más información acerca de cómo funciona esta característica.

El aprovisionamiento automático es muy recomendable y es necesario para las suscripciones del nivel Estándar de Security Center. La deshabilitación del aprovisionamiento automático limita la supervisión de seguridad de los recursos.

Consulte los [planes de tarifa de Security Center](security-center-pricing.md) para obtener más información sobre los planes de tarifa Gratis y Estándar.

Los pasos siguientes describen cómo acceder a los componentes de Security Center y usarlos.

> [!NOTE]
> Este artículo es una introducción al servicio mediante una implementación de ejemplo. No es una guía paso a paso.
>
>

## <a name="access-security-center"></a>Acceso al Centro de seguridad
En el portal, siga estos pasos para acceder al Centro de seguridad:

1. En el menú de **Microsoft Azure**, seleccione **Security Center**.

   ![Menú de Azure][1]
2. Si es la primera vez que accede a Security Center, se abre la hoja de **bienvenida**. Seleccione **Iniciar Security Center** para abrir **Security Center**.
   ![Pantalla principal][10]
3. Después de iniciar Security Center desde la hoja de bienvenida o seleccionarlo desde el menú de Microsoft Azure, se abre **Security Center**. Para facilitar el acceso a la hoja **Security Center** en el futuro, seleccione la opción **Anclar hoja al panel** (parte superior derecha).
   ![Opción Anclar hoja al panel][2]

## <a name="use-security-center"></a>Uso del Centro de seguridad
Puede configurar directivas de seguridad para los grupos de recursos y las suscripciones de Azure. Vamos a configurar una directiva de seguridad para su suscripción:

1. En el menú principal de Security Center, seleccione **Directiva de seguridad**.
2. En **Security Center - Directiva de seguridad**, seleccione una suscripción.
3. En **Directiva de seguridad - Colección de datos**, está activado **Aprovisionamiento automático**. Security Center aprovisiona Microsoft Monitoring Agent en todas las máquinas virtuales de Azure compatibles y en las que se creen.

    ![Directiva de seguridad][12]

4. En la hoja **Directiva de seguridad**, seleccione el componente de directiva **Directiva de seguridad**.

     ![Directiva de seguridad][11]

5. En **Mostrar recomendaciones para**, active las recomendaciones que quiere ver como parte de la directiva de seguridad. Ejemplos:

   * Al establecer **Actualizaciones del sistema** en **Activar**, se examinan todas las máquinas virtuales compatibles para comprobar si faltan actualizaciones del sistema operativo.
   * Al establecer **Vulnerabilidades del sistema operativo** en **Activar**, se examinan todas las máquinas virtuales compatibles para identificar las configuraciones del sistema operativo que podrían hacer que la máquina virtual resultara más vulnerable a ataques.

6. Seleccione **Guardar**.

### <a name="view-recommendations"></a>Ver recomendaciones
1. Vuelva a la hoja **Security Center** y haga clic en el icono **Recomendaciones**. El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, muestra recomendaciones en la hoja **Recomendaciones**.
   ![Recomendaciones en Azure Security Center][5]
2. Seleccione una recomendación en la hoja **Recomendaciones** para ver más información o realizar una acción para resolver el problema.

### <a name="view-the-security-state-of-your-resources"></a>Visualización del estado de seguridad de los recursos
1. Vuelva a la hoja **Centro de seguridad** . La sección **Prevención** del panel contiene indicadores del estado de seguridad de máquinas virtuales, redes, datos y aplicaciones.
2. Seleccione **Proceso** para más información. La hoja **Proceso** se abre y muestra tres pestañas:

  - **Información general**: contiene recomendaciones de supervisión y de máquinas virtuales.
  - **VM y equipos**: muestra el estado de seguridad actual de todas las máquinas virtuales y equipos.
  - **Cloud Services**: lista de todos los roles web y de trabajo que supervisa Security Center.

    ![Cálculo del estado de seguridad][6]

3. En la pestaña **Información general**, seleccione una recomendación para ver más información o realizar una acción para configurar los controles necesarios.
4. En la pestaña **VM y equipos**, seleccione un recurso para ver detalles adicionales.

### <a name="view-security-alerts"></a>Ver alertas de seguridad
1. Vuelva a la hoja **Security Center** y haga clic en el icono **Alertas de seguridad**. Se abre la hoja **Alertas de seguridad** con una lista de alertas. Las alertas se generan mediante el análisis que Security Center hace de los registros de seguridad y la actividad de la red. También se incluyen alertas de soluciones de asociados integradas.
   ![Alertas de seguridad en el Centro de seguridad de Azure][7]

2. Seleccione una alerta para ver información adicional. En este ejemplo, vamos a seleccionar **Modified system binary discovered in dump filter** (Binario del sistema modificado detectado en filtro de volcado de memoria). Al hacerlo, se abren varias hojas que proporcionan detalles adicionales sobre la alerta.
   ![Detalles de alertas de seguridad en Azure Security Center][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Ver el estado de sus soluciones de asociados
1. Vuelva a la hoja **Centro de seguridad** . El icono **Soluciones de seguridad** permite supervisar de un solo vistazo el estado de mantenimiento de las soluciones de asociados integradas en su suscripción de Azure.
2. Seleccione el icono **Soluciones de seguridad**. Se abre una hoja con una lista de todas las soluciones de asociados conectadas a Security Center.
   ![Soluciones de asociados][9]
3. Seleccione una solución de asociado. Se abre una hoja que muestra el estado de la solución de asociados y de los recursos asociados de la solución. Seleccione **Consola de soluciones** para abrir la experiencia de administración de asociados de esta solución.

   ![Soluciones de socios][13]

## <a name="next-steps"></a>Pasos siguientes
En este artículo se han presentado los componentes de supervisión de la seguridad y administración de directivas de Security Center. Ahora que está familiarizado con Security Center, pruebe los siguientes pasos:

* Configure una directiva de seguridad para su suscripción de Azure mediante los pasos que se describen en [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md).
* Use las recomendaciones de Security Center para proteger sus recursos de Azure mediante los pasos que se describen en [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md).
* Revise y administre sus alertas de seguridad actuales mediante los pasos que se describen en [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md).
- Aprenda más sobre la integración con asociados para mejorar la seguridad general en [Integración de asociados y soluciones en Azure Security Center](security-center-partner-integration.md).
- Aprenda cómo se administran y protegen los datos en Security Center en [Seguridad de datos de Azure Security Center](security-center-data-security.md).
* Aprenda más sobre las [características avanzadas de detección de amenazas](security-center-detection-capabilities.md) que vienen con el [nivel estándar](security-center-pricing.md) de Security Center. El nivel Estándar se ofrece gratis para los primeros 60 días.
* Si tiene preguntas acerca de cómo usar Security Center, consulte las [P+F de Azure Security Center](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
