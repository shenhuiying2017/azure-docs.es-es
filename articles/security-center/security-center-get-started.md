<properties
   pageTitle="Introducción al Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Introducción al Centro de seguridad de Azure

Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes.

> [AZURE.NOTE]La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. Se trata de una introducción al servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## Requisitos previos

Para empezar a trabajar con el Centro de seguridad de Azure, debe disponer de una suscripción a Microsoft Azure. Centro de seguridad de Azure se habilita con su suscripción. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).

Al Centro de seguridad de Azure se accede desde el [Portal de vista previa de Microsoft Azure](http://azure.microsoft.com/features/azure-portal/). Consulte [Documentación del Portal de vista previa de Azure](https://azure.microsoft.com/documentation/services/azure-portal/) para obtener más información.


## Acceso al Centro de seguridad de Azure

En el Portal de Azure, siga estos pasos para tener acceso al Centro de seguridad de Azure:

1. Seleccione **Examinar** y desplácese hasta la opción **Centro de seguridad**. ![][1]

2. Seleccione **Centro de seguridad**. De este modo se abrirá la hoja **Centro de seguridad**.
3. Para facilitar el acceso a la hoja **Centro de seguridad** en el futuro, seleccione la opción **Fijar hoja al panel** (parte superior derecha). ![][2]

## Uso del Centro de seguridad de Azure

Configure una **directiva** de seguridad para sus suscripciones:

4. Haga clic en el icono **Directiva de seguridad** en la hoja **Centro de seguridad**.
5. En la hoja **Directiva de seguridad: definir directiva por suscripción**, seleccione una suscripción.
6. En la hoja **Directiva de seguridad**, active **Recopilación de datos** para recopilar registros de forma automática. La activación de la **Recopilación de datos** también proporcionará la extensión de supervisión en todas las máquinas virtuales actuales y nuevas en la suscripción.
7. Haga clic en **Elegir cuentas de almacenamiento**. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se creará una automáticamente. Los datos recopilados se aíslan lógicamente de los datos de otros clientes por motivos de seguridad.
8. Active las **Recomendaciones** que le gustaría ver como parte de la directiva de seguridad. Ejemplos:

  - La activación de **Actualizaciones del sistema** examinará todas las máquinas virtuales para comprobar si faltan actualizaciones de sistema operativo.
  - La activación de **Reglas de línea de base** examinará las máquinas virtuales compatibles para identificar las configuraciones de SO que podrían hacer que la máquina virtual resultase más vulnerable a ataques.

![][3]

Atender las **recomendaciones**:

9. Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Recomendaciones**.
10.	El Centro de seguridad de Azure analiza periódicamente el estado de seguridad de los recursos de Azure. Una vez identificadas las posibles vulnerabilidades de seguridad, muestra una recomendación.
11.	Haga clic en cada recomendación para ver más información y/o actuar para resolverla.

![][4]

Consulte el estado de y la seguridad de los recursos a través de **Estado de los recursos**:

12.	Vuelva a la hoja **Centro de seguridad**.
13.	El icono **Estado de los recursos** contiene indicadores del estado de seguridad de las **Máquinas virtuales**, **Redes**, **SQL** y **Aplicaciones**.
14.	Seleccione **Máquinas virtuales** para consultar más información.
15.	En la hoja **Máquinas virtuales** se muestra un resumen de estado que incluye antimalware, actualizaciones del sistema, reinicios y reglas de línea de base de las máquinas virtuales.
16.	Seleccione un elemento en **PASOS DE PREVENCIÓN** para obtener más información y/o tomar medidas para configurar los controles necesarios.
17.	Explore en profundidad para ver información adicional para máquinas virtuales específicas.

![][5]

Atender las **Alertas de seguridad**:

19.	Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Alertas de seguridad**.
20.	En la hoja **Alertas de seguridad**, se muestra una lista de alertas. Las alertas fueron detectadas mediante el análisis del Centro de seguridad de Azure de los registros de seguridad y la actividad de la red. También se incluyen las alertas de soluciones de socios integradas. ![][6]

21.	Haga clic en una alerta para ver información adicional.

  ![][7]

## Pasos siguientes
En este documento se presentaron los componentes de supervisión de seguridad y de administración de directivas en el Centro de seguridad de Azure. Para obtener más información, consulte:

- [Configuración de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información acerca de cómo configurar las directivas de seguridad
- [Implementación de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información acerca de cómo las recomendaciones le ayudan a proteger sus recursos de Azure
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información acerca de cómo supervisar el estado de los recursos de Azure
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información acerca de cómo administrar y responder a alertas de seguridad
- [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque preguntas frecuentes acerca de cómo usar el servicio
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información de seguridad de Azure

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_1203_2015-->