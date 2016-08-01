<properties
   pageTitle="Enrutamiento del tráfico a través del firewall de próxima generación solo en Azure Security Center | Microsoft Azure"
   description="En este documento se muestra cómo implementar la recomendación de Azure Security Center de enrutar el tráfico solo a través del NGFW."
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
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Enrutamiento del tráfico a través del firewall de próxima generación solo en Azure Security Center

Azure Security Center puede detectar cuándo se ha implementado un firewall de próxima generación (NGFW). Si tiene puntos de conexión accesibles a través de Internet, Azure Security Center recomendará configurar reglas de grupos de seguridad de red que fuercen a que el tráfico entrante pase a la máquina virtual a través del NGFW.

> [AZURE.NOTE] En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Enrutar el tráfico solo a través de NGFW**. ![Enrutar el tráfico solo a través de NGFW][1]

2. Se abrirá la hoja **Enrutar el tráfico solo a través de NGFW**, que enumera las máquinas virtuales que puede enrutar el tráfico. Seleccione una máquina virtual de la lista. ![Seleccionar una máquina virtual][2]

3. Se abre una hoja de la máquina virtual seleccionada, que muestra las reglas de entrada relacionadas. Una descripción proporciona más información sobre los posibles pasos posteriores. ![Configuración de reglas para limitar el acceso][3]

## Consulte también

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->