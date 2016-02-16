<properties
   pageTitle="Introducción al Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Introducción al Centro de seguridad de Azure

Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## ¿Qué es el Centro de seguridad de Azure?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## Requisitos previos

Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. El Centro de seguridad se habilita con su suscripción. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) para más información.


## Acceso al Centro de seguridad

En el portal, siga estos pasos para acceder al Centro de seguridad:

1. Seleccione **Examinar** y después desplácese hasta la opción **Centro de seguridad**. ![Acceso al Centro de seguridad de Azure en el portal][1]

2. Seleccione **Centro de seguridad**. De este modo se abrirá la hoja **Centro de seguridad**.
3. Para facilitar el acceso a la hoja **Centro de seguridad** en el futuro, seleccione la opción **Fijar hoja al panel** (parte superior derecha). ![Opción Anclar hoja al panel][2]

## Uso del Centro de seguridad

Configure una **directiva** de seguridad para sus suscripciones:

4. Haga clic en el icono **Directiva de seguridad** en la hoja **Centro de seguridad**.
5. En la hoja **Directiva de seguridad-Definir directiva por suscripción**, seleccione una suscripción.
6. En la hoja **Directiva de seguridad**, active **Recopilación de datos** para recopilar registros de forma automática. La activación de la **Recopilación de datos** también proporcionará la extensión de supervisión en todas las máquinas virtuales actuales y nuevas en la suscripción.
7. Haga clic en **Elegir cuentas de almacenamiento**. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se creará automáticamente. Los datos recopilados se aíslan lógicamente de los datos de otros clientes por seguridad.
8. Active las **Recomendaciones** que le gustaría ver como parte de la directiva de seguridad. Ejemplos:

  - Si se activa **Actualizaciones del sistema**, se examinarán todas las máquinas virtuales compatibles para comprobar si faltan actualizaciones del sistema operativo.
  - Si se activa **Reglas de línea de base**, se examinarán las máquinas virtuales compatibles para identificar las configuraciones del sistema operativo que podrían hacer que la máquina virtual resultara más vulnerable a ataques.

![El icono Directiva de seguridad en el Centro de seguridad de Azure][3]

Atender las **recomendaciones**:

9. Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Recomendaciones**. El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Una vez identificadas las posibles vulnerabilidades de seguridad, muestra una recomendación.
11.	Haga clic en cada recomendación para ver más información y/o actuar para resolver el problema.

![Recomendaciones en el Centro de seguridad de Azure][4]

Consulte el estado de y la seguridad de los recursos a través de **Estado de los recursos**:

12.	Vuelva a la hoja **Centro de seguridad**.
13.	El icono **Estado de los recursos** contiene indicadores del estado de seguridad de las **Máquinas virtuales**, **Redes**, **SQL** y **Aplicaciones**.
14.	Seleccione **Máquinas virtuales** para consultar más información.
15.	La hoja **Máquinas virtuales** muestra un resumen del estado de los programas antimalware, las actualizaciones del sistema, los reinicios y las reglas de línea de base de sus máquinas virtuales.
16.	Seleccione un elemento en **PASOS DE PREVENCIÓN** para ver más información y/o tomar medidas para configurar los controles necesarios.
17.	Explore en profundidad para ver información adicional para máquinas virtuales específicas.

![El icono Estado de los recursos en el Centro de seguridad de Azure][5]

Atender las **Alertas de seguridad**:

19.	Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Alertas de seguridad**. En la hoja **Alertas de seguridad**, se muestra una lista de alertas. Las alertas se generan mediante el análisis que el Centro de seguridad hace de los registros de seguridad y la actividad de la red. También se incluyen las alertas de soluciones de socios integradas. ![Alertas de seguridad en el Centro de seguridad de Azure][6]

21.	Haga clic en una alerta para ver información adicional.

  ![Detalles de alertas de seguridad en el Centro de seguridad de Azure][7]

## Pasos siguientes
En este documento, se han presentado los componentes de supervisión de seguridad y de administración de directivas en el Centro de seguridad. Para obtener más información, consulte:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): aprenda a configurar directivas de seguridad.
- [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información sobre cómo las recomendaciones lo ayudan a proteger sus recursos de Azure.
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): aprenda a supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): aprenda a administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información de seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_0211_2016-->