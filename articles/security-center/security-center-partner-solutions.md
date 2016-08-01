<properties
   pageTitle="Administración de soluciones de asociados en Azure Security Center | Microsoft Azure"
   description="En este documento se explica cómo Azure Security Center permite supervisar de un vistazo el estado de mantenimiento de las soluciones de asociados integradas con su suscripción de Azure."
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
   ms.date="07/19/2016"
   ms.author="terrylan"/>

# Supervisión de las soluciones de asociados con Azure Security Center

En este documento se explica cómo supervisar el estado de mantenimiento de las soluciones de asociados en Azure Security Center.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Supervisión de soluciones de asociados

El icono de **Partner solutions** (Soluciones de asociados) de la hoja **Security Center** permite supervisar de un vistazo el estado de mantenimiento de las soluciones de asociados que se integran con su suscripción de Azure. ![Icono Partner solutions (Soluciones de asociados)][1]

El icono de **Partner solutions** (Soluciones de asociados) muestra el número de soluciones de asociados y un resumen de estado de esas soluciones.

El **ESTADO** de una solución de asociado puede ser:

- Protegido (verde): no hay ningún problema de estado.
- Incorrecto (rojo): hay un problema de estado que requiere atención inmediata.
- Información detenida (naranja): la solución ha dejado de informar sobre su estado.
- Estado de protección desconocido (naranja): el estado de la solución se desconoce en este momento debido a un error en el proceso de agregación de un nuevo recurso a la solución existente.
- Sin información (gris): la solución no ha notificado nada todavía; el estado de una solución puede ser no notificado si acaba de conectarse y todavía se está implementando.

Si no hay ninguna solución integrada con su suscripción, el icono indicará este hecho. La selección del icono **Partner solutions** (Soluciones de asociados) le permite abrir la hoja **Recomendaciones** para implementar soluciones de seguridad de asociados. ![Sin soluciones de asociados][2]

Para ver el estado de sus soluciones de asociados:

1. Seleccione el icono **Soluciones de asociados**. Se abre una hoja con una lista de todas las soluciones de asociados conectadas a Azure Security Center. ![Soluciones de socios][3]

2. Seleccione una solución de asociado. En este ejemplo, seleccionaremos la solución **F5-WAF2**. Se abre una hoja que muestra que el estado de la solución de asociado y de sus recursos asociados. Seleccione **Consola de soluciones** para abrir la experiencia de administración de asociados de esta solución. ![Detalle de solución de asociado][4]

3. Vuelva a la hoja **F5-WAF2** y seleccione **Link app** (Vincular aplicación). Se abre la hoja **Link Applications** (Vincular aplicaciones). Aquí puede conectar recursos a la solución de asociados. ![Vinculación de recursos a soluciones de asociados][5]

## Consulte también
En este documento, se ha presentado el icono **Partner Solutions** (Soluciones de asociados) de Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

<!---HONumber=AcomDC_0720_2016-->