<properties
	pageTitle="Límites y cuotas del servicio Lote | Microsoft Azure"
	description="Obtenga información sobre las cuotas, los límites y las restricciones en el uso del servicio Lote de Azure"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="marsma"/>

# Cuotas y límites del servicio de Lote de Azure

Al igual que en otros servicios de Azure, existen límites en determinados recursos asociados con el servicio Lote. Muchos de ellos son cuotas predeterminadas que Azure aplica en el nivel de cuenta o suscripción. En este artículo se describen esos valores predeterminados y cómo solicitar un aumento de la cuota.

Si planea ejecutar cargas de trabajo de producción en Lote, es posible que tenga que aumentar el valor predeterminado de una o varias de las cuotas. Si desea aumentar una cuota, puede abrir una [solicitud de soporte técnico al cliente](#increase-a-quota) en línea sin ningún costo.

>[AZURE.NOTE] Una cuota es un límite de crédito, no una garantía de capacidad. Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure.

## Cuotas de suscripción
**Recurso**|**Límite predeterminado**|**Límite máximo**
---|---|---
Cuentas de Lote por región y suscripción | 1 | 50

## Cuotas de la cuenta de Lote
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Otros límites
**Recurso**|**Límite máximo**
---|---
[Tareas simultáneas](batch-parallel-node-tasks.md) por nodo de proceso | 4 × número de núcleos de nodo
[Aplicaciones](batch-application-packages.md) por cuenta de Lote | 20 |
Paquetes de aplicación por aplicación | 40
Tamaño del paquete de aplicación (cada uno) | Aprox. 195 GB<sup>1</sup>

<sup>1</sup> Límite de Almacenamiento de Azure para el tamaño máximo de blob en bloques

## Visualización de las cuotas de Lote

Vea las cuotas de la cuenta de Lote en el [Portal de Azure][portal].

1. En el Portal, haga clic en **Cuentas de Lote** y luego en el nombre de la cuenta de Lote.

2. En la hoja de la cuenta, haga clic en **Toda la configuración** > **Propiedades**.

	![Cuotas de la cuenta de Lote][account_quotas]

3. La hoja **Propiedades** muestra las cuotas que hay aplicadas actualmente en la cuenta de Lote.

## Aumento de la cuota

Siga los pasos a continuación para solicitar un aumento de la cuota mediante el [Portal de Azure][portal].

1. Seleccione el icono **Ayuda y soporte técnico** en el panel del portal o el signo de interrogación (**?**) en la esquina superior derecha del portal.

2. Seleccione **Nueva solicitud de soporte técnico** > **Básico**.

3. En la hoja **Básico**:

	a. **Tipo de problema** > **Cuota**

	b. Seleccione su suscripción.

	c. **Tipo de cuota** > **Lote**

	d. **Plan de soporte técnico** > **Quota support - Included** (Soporte de cuota incluido)

	Haga clic en **Siguiente**.

4. En la hoja **Problema**:

	a. Seleccione una de las opciones en **Gravedad** según su [impacto en el negocio][support_sev].

	b. En **Detalles**, especifique cada cuota que desee cambiar, el nombre de cuenta de Lote y el nuevo límite.

	Haga clic en **Siguiente**.

5. En la hoja **Información de contacto**:

	a. Seleccione un valor en **Método de contacto preferido**.

	b. Compruebe y especifique los detalles de contacto necesarios.

	Haga clic en **Crear** para enviar la solicitud de soporte técnico.

Una vez que haya enviado la solicitud de soporte técnico, el servicio de soporte técnico de Azure se comunicará con usted. Tenga en cuenta que se puede tardar hasta 2 días laborables en completar la solicitud.

## Temas relacionados

* [Creación de una cuenta de Lote de Azure con el Portal de Azure](batch-account-create-portal.md)

* [Información general de las características de Lote de Azure](batch-api-basics.md)

* [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0831_2016-->