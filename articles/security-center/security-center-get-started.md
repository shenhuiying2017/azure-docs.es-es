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
ms.date: 05/08/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 0ca7382ed64fd548f8a086893ea2e3187dd26929
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Guía de inicio rápido de Azure Security Center
Este artículo le ayuda a comenzar rápidamente con Azure Security Center guiándole por los componentes de supervisión de la seguridad y administración de directivas de Security Center.

> [!NOTE]
> Este artículo es una introducción al servicio mediante una implementación de ejemplo. No es una guía paso a paso.
>
>

## <a name="prerequisites"></a>Requisitos previos
Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

El nivel gratuito de Security Center se habilita automáticamente con su suscripción y proporciona visibilidad sobre el estado de seguridad de los recursos de Azure. Ofrece administración básica de directivas de seguridad, recomendaciones de seguridad e integración con servicios y productos de seguridad de Azure de asociados.

Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Para más información sobre Azure Portal, consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Permisos
En Security Center, solo se muestra información relacionada con un recurso de Azure cuando tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o grupo de recursos al que pertenece un recurso. Consulte [Permisos en Azure Security Center](security-center-permissions.md) para más información sobre los roles y las acciones permitidas en Security Center.

## <a name="data-collection"></a>Colección de datos
Security Center recopila datos de las máquinas virtuales (VM) para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede a Security Center, la recopilación de datos está habilitada en todas las máquinas virtuales de la suscripción. Aunque se recomienda usar la recopilación de datos, puede desactivarla en la directiva de Security Center.

Los pasos siguientes describen cómo acceder a los componentes de Security Center y usarlos. En estos pasos, se muestra cómo desactivar la recopilación de datos, si decide excluirla.

## <a name="access-security-center"></a>Acceso al Centro de seguridad
En el portal, siga estos pasos para acceder al Centro de seguridad:

1. En el menú de **Microsoft Azure**, seleccione **Security Center**.

   ![Menú de Azure][1]
2. Si es la primera vez que accede a Security Center, se abre la hoja de **bienvenida**. Seleccione **Launch Security Center** (Iniciar Security Center) para abrir la hoja **Security Center** y habilitar la colección de datos.
   ![Pantalla principal][10]
3. Después de iniciar Security Center desde la hoja de bienvenida o seleccionarlo desde el menú de Microsoft Azure, se abre la hoja **Security Center**. Para facilitar el acceso a la hoja **Security Center** en el futuro, seleccione la opción **Anclar hoja al panel** (parte superior derecha).
   ![Opción Anclar hoja al panel][2]

## <a name="use-security-center"></a>Uso del Centro de seguridad
Puede configurar directivas de seguridad para los grupos de recursos y las suscripciones de Azure. Vamos a configurar una directiva de seguridad para su suscripción:

1. En la hoja **Security Center**, seleccione el icono **Directiva**.
   ![Directiva de seguridad][3]
2. En la hoja **Directiva de seguridad: definir directiva por suscripción o grupo de recursos**, seleccione una suscripción.
3. En la hoja **Directiva de seguridad**, **Recopilación de datos** está habilitada para que recopile registros de forma automática. La extensión de supervisión se aprovisiona en todas las máquinas virtuales actuales y nuevas de la suscripción. (Para desactivar la recopilación de datos, en **Recopilación de datos**, seleccione **Desactivar**; pero si lo hace, Security Center dejará de proporcionar alertas y recomendaciones de seguridad).
4. En la hoja **Directiva de seguridad**, seleccione **Elija una cuenta de almacenamiento por región**. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se crea automáticamente una cuenta de almacenamiento y se coloca en el grupo de recursos securitydata. Los datos recopilados se aíslan lógicamente de los datos de otros clientes por seguridad.

   > [!NOTE]
   > Se recomienda que en primer lugar habilite la recopilación de datos y elija una cuenta de almacenamiento en el nivel de suscripción. Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la configuración de la recopilación de datos y la cuenta de almacenamiento tiene lugar solo en el nivel de suscripción.
   >
   >
5. En la hoja **Directiva de seguridad**, seleccione **Directiva de prevención**. Se abre la hoja **Directiva de prevención**.
   ![Directiva de prevención][4]
6. En la hoja **Directiva de prevención**, active las recomendaciones que quiere ver como parte de la directiva de seguridad. Ejemplos:

   * Al establecer **Actualizaciones del sistema** en **Activado**, se examinan todas las máquinas virtuales compatibles para comprobar si faltan actualizaciones del sistema operativo.
   * Al establecer **Vulnerabilidades del sistema operativo** en **Activado**, se examinan todas las máquinas virtuales compatibles para identificar todas las configuraciones del sistema operativo que podrían hacer que la máquina virtual resultara más vulnerable a ataques.

### <a name="view-recommendations"></a>Ver recomendaciones
1. Vuelva a la hoja **Security Center** y haga clic en el icono **Recomendaciones**. El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, muestra recomendaciones en la hoja **Recomendaciones**.
   ![Recomendaciones en Azure Security Center][5]
2. Seleccione una recomendación en la hoja **Recomendaciones** para ver más información o realizar una acción para resolver el problema.

### <a name="view-the-security-state-of-your-resources"></a>Visualización del estado de seguridad de los recursos
1. Vuelva a la hoja **Centro de seguridad** . La sección **Prevención** del panel contiene indicadores del estado de seguridad de máquinas virtuales, redes, datos y aplicaciones.
2. Seleccione **Proceso** para más información. La hoja **Proceso** se abre y muestra tres pestañas:

  - **Overview** (Información general): contiene recomendaciones de supervisión y de la máquina virtual.
  - **Virtual Machines** (Máquinas virtuales): lista de todas las máquinas virtuales y su estado de seguridad actual.
  - **Cloud Services**: lista de todos los roles web y de trabajo que supervisa Security Center.

    ![El icono Estado de los recursos en el Centro de seguridad de Azure][6]

3. En la pestaña **Overview** (Información general), seleccione una recomendación en **VIRTUAL MACHINES RECOMMENDATIONS** (RECOMENDACIONES DE MÁQUINAS VIRTUALES) para ver más información o seguir los pasos para configurar los controles necesarios.
4. En la pestaña **Máquinas virtuales**, seleccione una máquina virtual para ver detalles adicionales.

### <a name="view-security-alerts"></a>Ver alertas de seguridad
1. Vuelva a la hoja **Security Center** y haga clic en el icono **Alertas de seguridad**. Se abre la hoja **Alertas de seguridad** con una lista de alertas. Las alertas se generan mediante el análisis que Security Center hace de los registros de seguridad y la actividad de la red. También se incluyen alertas de soluciones de asociados integradas.
   ![Alertas de seguridad en el Centro de seguridad de Azure][7]

   > [!NOTE]
   > Las alertas de seguridad solo están disponibles si se habilita el nivel Estándar de Security Center. Hay disponible una evaluación gratuita durante 60 días de nivel Estándar. Consulte [Pasos siguientes](#next-steps) para más información sobre cómo conseguir el nivel Estándar.
   >
   >
2. Seleccione una alerta para ver información adicional. En este ejemplo, vamos a seleccionar **Modified system binary discovered** (Detectado binario del sistema modificado). Al hacerlo, se abren varias hojas que proporcionan detalles adicionales sobre la alerta.
   ![Detalles de alertas de seguridad en Azure Security Center][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Ver el estado de sus soluciones de asociados
1. Vuelva a la hoja **Centro de seguridad** . El icono **Soluciones de asociados** permite supervisar de un solo vistazo el estado de mantenimiento de las soluciones de asociados integradas en su suscripción de Azure.
2. Seleccione el icono **Soluciones de asociados** . Se abre una hoja con una lista de todas las soluciones de asociados conectadas a Security Center.
   ![Soluciones de asociados][9]
3. Seleccione una solución de asociado. En este ejemplo, seleccionamos la solución **QualysVa1**.  Se abre una hoja que muestra el estado de la solución de asociados y de los recursos asociados de la solución. Seleccione **Consola de soluciones** para abrir la experiencia de administración de asociados de esta solución.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se han presentado los componentes de supervisión de la seguridad y administración de directivas de Security Center. Ahora que está familiarizado con Security Center, pruebe los siguientes pasos:

* Configure una directiva de seguridad para su suscripción de Azure. Para aprender más, consulte [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md).
* Use las recomendaciones de Security Center como ayuda para proteger los recursos de Azure. Para aprender más, consulte [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md).
* Revise y administre las alertas de seguridad actuales. Para aprender más, consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md).
* Aprenda más sobre las [características avanzadas de detección de amenazas](security-center-detection-capabilities.md) que vienen con el [nivel estándar](security-center-pricing.md) de Security Center. El nivel Estándar se ofrece gratis para los primeros 60 días.
* Si tiene preguntas acerca de cómo usar Security Center, consulte las [P+F de Azure Security Center](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

