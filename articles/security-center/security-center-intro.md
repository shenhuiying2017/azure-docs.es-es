<properties
   pageTitle="Introducción al Centro de seguridad de Azure | Microsoft Azure"
   description="Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Introducción al Centro de seguridad de Azure

Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona.

> [AZURE.NOTE]La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. Se trata de una introducción al servicio mediante una implementación de ejemplo.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

##	Principales capacidades
Centro de seguridad de Azure ofrece las capacidades de prevención, detección y respuesta ante amenazas integradas en Azure. Principales capacidades:

| | |
|----- |-----|
| Prevención | Supervisa el estado de seguridad de los recursos de Azure |
| | Define las directivas de sus suscripciones de Azure en función de los requisitos de seguridad de su compañía y del tipo de aplicaciones o la confidencialidad de los datos |
| | Usa recomendaciones de seguridad controladas por directivas para guiar a los propietarios de los servicios a través del proceso de implementación de los controles necesarios |
| | Implementa rápidamente servicios y dispositivos de seguridad de Microsoft y asociados |
| Detección | Recopila y analiza los datos de seguridad de los recursos de Azure automáticamente, la red y soluciones de asociados como firewalls y antimalware |
| | Aprovecha información global sobre amenazas de productos y servicios de Microsoft, Centros de respuesta ante incidentes y delitos digitales y fuentes externas |
| | Aplica análisis avanzados, incluido el aprendizaje automático y el análisis de comportamientos |
| Respuesta | Proporciona seguridad prioritaria ante incidentes y alertas |
| | Proporciona información sobre el origen del ataque y los recursos afectados |
| | Sugiere formas de detener el ataque actual y de ayudar a impedir ataques futuros |

## Tutorial de introducción
Al Centro de seguridad de Azure se accede desde el [Portal de Azure](http://azure.microsoft.com/features/azure-portal/). Para acceder, [inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar** y desplácese hasta la opción **Centro de seguridad** o seleccione el icono **Centro de seguridad** que anteriormente ancló al panel del portal.

![][1]

Desde el Centro de seguridad de Azure, puede establecer directivas de seguridad, supervisar configuraciones de seguridad y ver alertas de seguridad.

### Directivas de seguridad

Puede definir directivas para sus suscripciones de Azure de acuerdo con las necesidades de seguridad de su compañía y adaptadas al tipo de aplicaciones o a la confidencialidad de los datos de cada suscripción. Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan distintos requisitos de seguridad que los empleados para aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como PII requieran un mayor nivel de seguridad.

> [AZURE.NOTE]Para editar una directiva de seguridad, debe ser propietario o colaborador de la suscripción.

Haga clic en el icono **Directiva de seguridad** para obtener una lista de las suscripciones y elija una suscripción para ver los detalles de la directiva.

![][2]

**Recopilación de datos** (consultar más arriba) habilita la recopilación de datos para una directiva de seguridad. La habilitación proporciona: - Un análisis diario de todas las máquinas virtuales compatibles para la supervisión de la seguridad y obtener recomendaciones - Colección de eventos de seguridad para el análisis y la detección de amenazas

**Mostrar recomendaciones para:** (consultar más arriba) le permite elegir los controles de seguridad que desea supervisar y recomendar según las necesidades de seguridad de los recursos de la suscripción.

### Recomendaciones de seguridad

El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure para identificar posibles vulnerabilidades de seguridad. Una lista de recomendaciones le guiará por el proceso de configuración de los controles necesarios. Algunos ejemplos son:

- Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado
- Configuración de reglas y Grupos de seguridad de red para controlar el tráfico a las máquinas virtuales
- Aprovisionamiento de un firewall de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web
- Implementación de actualizaciones del sistema que faltan.
- Resolución de las configuraciones de sistema operativo que no coinciden con las líneas de base recomendadas

Haga clic en el icono **Recomendaciones** para obtener una lista de recomendaciones. Haga clic en cada recomendación para ver información adicional o para llevar a cabo acciones para resolverla.

![][3]

### Estado de los recursos

El icono **Estado de los recursos** muestra el estado general de la seguridad del entorno por tipo de recurso (máquinas virtuales, aplicaciones web y otros recursos).

Seleccione un tipo de recurso en el icono **Estado de recursos** para ver más información (máquinas virtuales en el ejemplo siguiente), incluida una lista de las posibles vulnerabilidades de seguridad que se han identificado.

![][4]

### Alertas de seguridad

El Centro de seguridad de Azure recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y soluciones de asociados como firewalls y antimalware. Cuando se detecten amenazas, se creará una alerta de seguridad. Como ejemplos se incluye la detección de:

- Máquinas virtuales en peligro que se comunican con direcciones IP malintencionadas conocidas.
- Malware avanzado detectado mediante la generación de informes de errores de Windows
- Ataques por fuerza bruta contra máquinas virtuales
- Alertas de seguridad de antimalware y firewalls integrados

Al hacer clic en el icono **Alertas de seguridad** se muestra una lista de alertas por prioridad.

![][5]

Al seleccionar una alerta se muestra más información sobre el ataque y sugerencias sobre cómo resolverlo.

![][6]

## Primeros pasos
Para empezar a trabajar con el Centro de seguridad de Azure, debe disponer de una suscripción a Microsoft Azure. Centro de seguridad de Azure se habilita con su suscripción de Azure. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).

Al Centro de seguridad de Azure se accede desde el [Portal de Azure](http://azure.microsoft.com/features/azure-portal/). Consulte [Documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) para más información.

[Introducción al Centro de seguridad de Azure](security-center-get-started.md) rápidamente le guía a través de los componentes de administración de directivas y supervisión de seguridad del Centro de seguridad de Azure.

## Pasos siguientes
En este documento se presentó el Centro de seguridad de Azure, sus capacidades clave y cómo empezar a usarlo. Para obtener más información, consulte:

- [Configuración de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información acerca de cómo configurar las directivas de seguridad
- [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información sobre cómo las recomendaciones le ayudan a proteger sus recursos de Azure.
- [Supervisión del estado de seguridad en Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración de alertas de seguridad y respuesta a estas en Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información de seguridad de Azure

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_1217_2015-->