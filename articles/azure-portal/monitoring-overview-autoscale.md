<properties
	pageTitle="Información general de la funcionalidad de escalado automático en Microsoft Azure Virtual Machines, Cloud Services y Web Apps | Microsoft Azure"
	description="Información general sobre el escalado automático en Microsoft Azure Esta funcionalidad se utiliza en Microsoft Azure Virtual Machines, Cloud Services y Web Apps."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="robb"/>

# Información general de la funcionalidad de escalado automático de Microsoft Azure Virtual Machines, Cloud Services y Web Apps

En este artículo se explican el concepto del escalado automático de Microsoft Azure y las ventajas que aporta; además, se realiza una introducción para que pueda comenzar a utilizar esta funcionalidad.

El escalado automático de Azure Insights solo puede utilizarse en los siguientes recursos:

* [Conjuntos de escalado de máquina virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/)
* [Servicios en la nube](https://azure.microsoft.com/services/cloud-services/)
* [App Service: Web Apps](https://azure.microsoft.com/services/app-service/web/)
  
   
>[AZURE.NOTE] Azure tiene dos métodos de escalado automático. Una versión anterior del escalado automático se utiliza en las máquinas virtuales (conjuntos de disponibilidad). Esta característica tiene una compatibilidad limitada, así que recomendamos realizar una migración a los conjuntos de escala de máquina virtual para que se pueda utilizar el escalado automático de manera más rápida y confiable. En este artículo, se incluye un vínculo sobre cómo utilizar la tecnología antigua.


## Qué es el escalado automático 

Gracias al escalado automático, puede ejecutar la cantidad correcta de recursos para administrar la carga de la aplicación sin desperdiciar dinero debido a los recursos que están inactivos. Con esta funcionalidad también puede agregar o quitar automáticamente recursos de proceso en función de un conjunto de reglas. En la ilustración 1 se muestra este concepto.

![Explicación del concepto de escalado automático](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

**Ilustración 1: Explicación del concepto de escalado automático**

El escalado automático solo escala horizontalmente, que es un aumento o una reducción del número de instancias de máquina virtual. El escalado horizontal resulta más flexible en un entorno en la nube, ya que puede llegar a ejecutar miles de máquinas virtuales para administrar la carga. El otro tipo de escalado es vertical. El escalado vertical mantiene el mismo número de máquinas virtuales, pero hace que sean más o menos potentes. La potencia se mide en memoria, velocidad de CPU, espacio en disco, etc. El escalado vertical tiene más limitaciones, ya que depende de la disponibilidad de hardware de mayor tamaño, que puede variar según la región y que supera el límite rápidamente. El escalado vertical también suele requerir que se detenga e inicie una máquina virtual. Para obtener más información, consulte [Escalado vertical de máquinas virtuales de Azure con Automatización de Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


También hay que crear reglas sobre cuándo se debe escalar o reducir horizontalmente. Estos son algunos de los criterios que puede establecer para controlar las acciones de escalado:

* Número **mínimo** y **máximo** de instancias que se ejecutarán. Un mínimo para asegurarse de que la aplicación se ejecute siempre y un máximo para controlar los costos.
* **Regla o condición** de escalado automático. Puede ser un escalado basado en programaciones o métricas.
* **Tiempo de finalización**, que es la cantidad de tiempo que se esperará después de un evento de escalado automático antes de permitir que se produzca otro. Este periodo sirve para evitar un estado llamado "oscilación", que se produce cuando se agregan y eliminan máquinas virtuales rápidamente (en cuestión de minutos). Iniciar o detener una máquina virtual conlleva asumir un costo. La oscilación no ahorra costos; además, la máquina virtual iniciada o detenida no puede realizar tareas útiles, por lo que es peor que dejarla ejecutándose.

   
La lista completa de valores configurables se encuentra en la [API de REST de escalado automático](https://msdn.microsoft.com/library/dn931928.aspx).


## Diagrama conceptual  
En la ilustración 2 se muestra información general conceptual del escalado automático seguido de una explicación de las partes del diagrama.

![Agregar alerta](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)

**Figura 2: Información general del proceso de escalado automático**

## Métricas de recursos 
Los recursos generan métricas, que procesan posteriormente las reglas. Las métricas proceden de métodos diferentes. Los conjuntos de escala de máquina virtual utilizan datos de telemetría de los agentes de Diagnósticos de Azure, mientras que la telemetría de Web Apps y Cloud Services proviene directamente de la infraestructura de Azure. Algunas de las estadísticas que se utilizan frecuentemente son el uso de CPU, el uso de memoria, el número de subprocesos, la longitud de la cola y el uso del disco. Para ver una lista de los datos de telemetría que se pueden utilizar, consulte [Métricas comunes de escalado automático de Azure Insights](insights-autoscale-common-metrics.md).

 
## Hora
Las reglas basadas en programaciones emplean el huso horario UTC. Debe establecer la zona horaria correctamente al configurar las reglas.

## Reglas de escalado automático
El diagrama solo muestra una única regla de escalado automático, pero puede tener muchas. Puede crear reglas complejas de superposición según su situación. Estos son algunos de los tipos de reglas:
 
 - **Basadas en métricas**: por ejemplo, realizar esta acción cuando el uso de CPU sea superior al 50 %.
 - **Basadas en tiempo** : por ejemplo, desencadenar un webhook todos los sábados a las 8:00 en una zona horaria determinada.

 
## Automatización y acciones de escalado automático

Las reglas pueden desencadenar uno o varios tipos de acciones.

- **Escalar**: escalar o reducir horizontalmente máquinas virtuales
- **Enviar correo electrónico** : enviar correo electrónico a los administradores y coadministradores de una suscripción, así como a otras direcciones de correo electrónicos que especifique
- **Automate via webhooks** (Automatizar mediante webhooks): llamar a webhooks, que pueden desencadenar varias acciones dentro o fuera de Azure. Dentro de Azure, puede iniciar runbooks de Azure Automation, Azure Function o Azure Logic Apps. La URL de terceros de ejemplo fuera de Azure incluye servicios como Slack y Twilio.


## Detalles de las reglas de Azure Resource Manager

Las reglas de escalado automático tienen la siguiente estructura en una plantilla de Azure Resource Manager.

![Estructura de regla de escalado automático de Azure Resource Manager](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure2.png)

Puede mostrar varios perfiles. Cada uno de ellos puede tener varias reglas. Las ubicaciones y métodos de notificación se incluyen tras los perfiles. Algunos ejemplos de notificación son un webhook con el identificador URI o correos electrónicos con las direcciones de correo electrónico.

Para ver ejemplos de código, consulte los siguientes artículos:

* [Configuración avanzada de escalado automático con plantillas de Resource Manager para conjuntos de escala de máquina virtual](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [API de REST de escalado automático](https://msdn.microsoft.com/library/dn931953.aspx)

## Métodos de acceso 
Puede configurar reglas de escalado automático en los siguientes lugares:

- Portal de Azure
- PowerShell
- Interfaz de biblioteca común (CLI)
- API de REST de Insights

## Tutoriales de escalado automático

- [Escalado en Cloud Services](../cloud-services/cloud-services-how-to-scale-portal.md)
- [Escalado en Web Apps](insights-how-to-scale.md)
- [Escalado de conjuntos de disponibilidad clásicos de máquina virtual](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/)
- [Escalado de conjuntos de disponibilidad de máquina virtual en Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)
- [Escalado de conjuntos de disponibilidad de máquina virtual en Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md)
- [Configuración avanzada de escalado automático con plantillas de Resource Manager para conjuntos de escala de máquina virtual](insights-advanced-autoscale-virtual-machine-scale-sets.md)

## Pasos siguientes

Para obtener más información sobre el escalado automático, use los tutoriales de escalado automático mencionados anteriormente o consulte los siguientes recursos:

- [Métricas comunes de escalado automático de Azure Insights](insights-autoscale-common-metrics.md)
- [Procedimientos recomendados de escalado automático en Azure Insights](insights-autoscale-best-practices.md)
- [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Insights](insights-autoscale-to-webhook-email.md)
- [API de REST de escalado automático](https://msdn.microsoft.com/library/dn931953.aspx): consulte la API completa y los significados de cada uno de los campos y valores.
- [Solución de problemas de escalado automático de conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0907_2016-->