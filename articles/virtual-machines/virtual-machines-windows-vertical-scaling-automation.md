<properties
	pageTitle="Escalado vertical de máquinas virtuales de Azure con Automatización de Azure | Microsoft Azure"
	description="Escalado verticalmente una máquina virtual de Windows en respuesta a las alertas de supervisión con Automatización de Azure"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="singhkay"/>

# Escalado vertical de máquinas virtuales de Azure con Automatización de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica

El escalado vertical es el proceso de aumentar o disminuir los recursos de una máquina como respuesta a la carga de trabajo. Para lograrlo en Azure, cambie el tamaño de la máquina virtual. Esto puede ser útil en los siguientes escenarios:

- Si la máquina virtual no se utiliza con frecuencia, puede disminuir su tamaño para reducir los costos mensuales.
- Si la máquina virtual experimenta una carga máxima, es posible ajustarla en un mayor tamaño para aumentar su capacidad.

Los pasos para lograr esto se describen a continuación:

1. Configurar la Automatización de Azure para tener acceso a las máquinas virtuales.
2. Importar los runbooks de escalado vertical de Automatización de Azure a la suscripción.
3. Agregar un webhook al runbook.
4. Agregar una alerta a la máquina virtual.

> [AZURE.NOTE] Debido al tamaño de la primera máquina virtual, los tamaños a los que se puede escalar pueden estar limitados en virtud de la disponibilidad de los demás tamaños en el clúster donde actualmente está implementada la máquina virtual. En los runbooks de automatización publicados que se usan en este artículo nos hacemos cargo de esta situación y solo escalamos dentro de los siguientes pares de tamaños de máquina virtual. Esto significa que una máquina virtual Standard\_D1v2 no se aumentará de manera repentina a Standard\_G5 ni se reducirá a Basic\_A0.

>| Pares de escalado de tamaños de VM | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Configurar la Automatización de Azure para tener acceso a las máquinas virtuales.

Lo primero que debe hacer es crear una cuenta de Automatización de Azure que hospedará los runbooks que se usan para escalar una máquina virtual. Recientemente, el servicio de automatización presentó la característica "Cuenta de ejecución", que facilita la configuración de la entidad de servicio para ejecutar los Runbooks automáticamente en nombre de un usuario. Encontrará más información al respecto en el siguiente artículo:

* [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md)

## Importar los runbooks de escalado vertical de Automatización de Azure a la suscripción

Los runbooks necesarios para el escalado vertical de la máquina virtual están publicados actualmente en la galería de runbooks de Automatización de Azure. Deberá importarlos a la suscripción. En el siguiente artículo, puede obtener información sobre cómo importar runbooks:

* [Galerías de runbooks y módulos para la automatización de Azure](../automation/automation-runbook-gallery.md)

En la imagen que aparece a continuación, se muestran los runbooks que es necesario importar:

![Importar runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## Agregar un webhook al runbook

Una vez que importe los runbooks, deberá agregar un webhook al runbook para que, de este modo, una alerta proveniente de una máquina virtual pueda desencadenarlo. A continuación, puede leer los detalles sobre cómo crear un webhook para el runbook:

* [Webhooks de Automatización de Azure ](../automation/automation-webhooks.md)

Asegúrese de copiar el webhook antes de cerrar el cuadro de diálogo de webhook, porque lo necesitará en la siguiente sección.

## Agregar una alerta a la máquina virtual

1. Seleccione la configuración de la máquina virtual.
2. Seleccione "Reglas de alerta".
3. Seleccione "Agregar alerta".
4. Seleccione una métrica según la cual activar la alerta.
5. Seleccione una condición que, cuando se cumpla, hará que se active la alerta.
6. Seleccione un umbral para cumplir la condición establecida en el paso 5.
7. Seleccione un período durante el cual el servicio de supervisión comprobará la condición y el umbral que se establecieron en los pasos 5 y 6.
8. Péguelo en el webhook que copió desde la sección anterior.

![Agregar alerta a máquina virtual 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Agregar alerta a máquina virtual 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)

<!---HONumber=AcomDC_0518_2016-->