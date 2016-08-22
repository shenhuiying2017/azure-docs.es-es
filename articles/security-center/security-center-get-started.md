<properties
   pageTitle="Guía de inicio rápido de Azure Security Center | Microsoft Azure"
   description="Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# Guía de inicio rápido de Azure Security Center

Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes.

> [AZURE.NOTE] En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Colección de datos

Security Center recopila datos de las máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede al Centro de seguridad la recopilación de datos se habilita en todas las máquinas virtuales de la suscripción. Se recomienda utilizar la recopilación de datos, pero se puede cancelar desactivando la recopilación de datos en la directiva del Centro de seguridad. Los pasos siguientes muestran cómo desactivar la recopilación de datos.

## Requisitos previos

Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. El Centro de seguridad se habilita con su suscripción. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) para más información.


## Acceso al Centro de seguridad

En el portal, siga estos pasos para acceder al Centro de seguridad:

1. Seleccione **Examinar** y después desplácese hasta la opción **Centro de seguridad**. ![Acceso al Centro de seguridad de Azure en el portal][1]

2. Seleccione **Centro de seguridad**. De este modo se abrirá la hoja **Centro de seguridad**.
3. Para facilitar el acceso a la hoja **Centro de seguridad** en el futuro, seleccione la opción **Fijar hoja al panel** (parte superior derecha). ![Opción Anclar hoja al panel][2]

## Uso del Centro de seguridad

Puede configurar directivas de seguridad para los grupos de recursos y las suscripciones de Azure. Configuremos una **directiva** de seguridad para su suscripción:

1. Haga clic en el icono **Directiva** en la hoja **Centro de seguridad**. ![Centro de seguridad][3]

2. En la hoja **Directiva de seguridad: definir directiva por suscripción o grupo de recursos**, seleccione una suscripción. ![La hoja Directiva de seguridad en el Centro de seguridad de Azure][4]

3. En la hoja **Directiva de seguridad**, **Recopilación de datos** está habilitado para que recopile registros de forma automática. La extensión de supervisión se aprovisiona en todas las máquinas virtuales actuales y nuevas de la suscripción. (para desactivar la recopilación de datos, en **Recopilación de datos** seleccione **Off**, pero si lo hace, Azure Security Center dejará de proporcionar alertas y recomendaciones de seguridad).
4. Seleccione **Elija una cuenta de almacenamiento por región**. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se creará automáticamente. Los datos recopilados se aíslan lógicamente de los datos de otros clientes por seguridad.

     > [AZURE.NOTE] Se recomienda que en primer lugar habilite la recopilación de datos y elija una cuenta de almacenamiento en el nivel de suscripción. Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la configuración de la recopilación de datos y la cuenta de almacenamiento tiene lugar solo en el nivel de suscripción.

5. Active las **Recomendaciones** que le gustaría ver como parte de la directiva de seguridad. Ejemplos:

 - Si se activa **Actualizaciones del sistema**, se examinarán todas las máquinas virtuales compatibles para comprobar si faltan actualizaciones del sistema operativo.
 - Si se activa **OS vulnerabilities** (Vulnerabilidades del SO), se examinarán todas las máquinas virtuales compatibles para identificar todas las configuraciones del sistema operativo que podrían hacer que la máquina virtual resultara más vulnerable a ataques.

Atender las **recomendaciones**:

1. Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Recomendaciones**. El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Una vez identificadas las posibles vulnerabilidades de seguridad, muestra una recomendación.
2.	Haga clic en cada recomendación para ver más información o para realizar alguna acción encaminada a resolver el problema. ![Recomendaciones en el Centro de seguridad de Azure][5]

Consulte el estado de mantenimiento y seguridad de los recursos a través de **Estado de seguridad de los recursos**:

1.	Vuelva a la hoja **Centro de seguridad**.
2.	El icono **Estado de seguridad de los recursos** contiene indicadores del estado de seguridad de las **Máquinas virtuales**, **Redes**, **SQL** y **Aplicaciones**.
3.	Seleccione **Máquinas virtuales** para consultar más información.
4.	La hoja **Máquinas virtuales** muestra un resumen del estado de los programas antimalware, las actualizaciones del sistema, los reinicios y las reglas de línea de base de sus máquinas virtuales.
5.	Seleccione un elemento en **RECOMENDACIONES SOBRE MÁQUINAS VIRTUALES** para ver más información o tomar medidas para configurar los controles necesarios.
6.	Explore en profundidad para ver información adicional de máquinas virtuales concretas. ![El icono Estado de los recursos en el Centro de seguridad de Azure][6]

Atender las **Alertas de seguridad**:

1.	Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Alertas de seguridad**. En la hoja **Alertas de seguridad**, se muestra una lista de alertas. Las alertas se generan mediante el análisis que el Centro de seguridad hace de los registros de seguridad y la actividad de la red. También se incluyen las alertas de soluciones de socios integradas. ![Alertas de seguridad en el Centro de seguridad de Azure][7]

2.	Seleccione una alerta para ver información adicional. ![Detalles de alertas de seguridad en el Centro de seguridad de Azure][8]

Ver el estado de su **Soluciones de asociados**:

1. Vuelva a la hoja **Centro de seguridad**. El icono de **Soluciones de asociados** permite supervisar de un solo vistazo el estado de mantenimiento de las soluciones de asociados integradas en su suscripción de Azure.
2. Seleccione el icono de **Soluciones de asociados**. Se abre una hoja con una lista de todas las soluciones de asociados conectadas a Azure Security Center. ![Soluciones de socios][9]

3. Seleccione una solución de asociado. En este ejemplo, seleccionaremos la solución **F5-WAF2**. Se abre una hoja que muestra que el estado de la solución de asociado y de sus recursos asociados. Seleccione **Consola de soluciones** para abrir la experiencia de administración de asociados de esta solución. ![Detalle de solución de asociado][10]

## Otras referencias
En este documento, se han presentado los componentes de supervisión de seguridad y de administración de directivas en el Centro de seguridad. Para obtener más información, consulte:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png

<!---HONumber=AcomDC_0810_2016-->