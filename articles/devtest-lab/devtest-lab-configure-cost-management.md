<properties
	pageTitle="Configure cost management (Configuración de la administración de costos) | Microsoft Azure"
	description="Aprenda a configurar las características de administración de costos de DevTest Labs."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Configure cost management (Configuración de la administración de costos)

## Información general

La característica de administración de costos de Laboratorios de desarrollo y pruebas le ayuda a controlar el costo del laboratorio. En este artículo se muestra el uso del gráfico **Monthly Estimated Cost Trend** (Tendencia de costos estimados mensuales) para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo a fin de mes previsto para el mes de calendario actual.

## Habilitación del gráfico Tendencia de costos estimados mensuales (Tendencia de costos estimados mensuales)

Para habilitar el gráfico Tendencia de costos estimados mensuales (Tendencia de costos estimados mensuales), siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Pulse **Configuración**.

	![Settings](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. En la hoja **Configuración** del laboratorio, en **Cost Policies** (Directivas de costo), pulse **Cost Thresholds** (Umbrales de costo).

	![Menú](./media/devtest-lab-configure-cost-management/menu.png)
 
1. En la hoja **Umbrales de costo**, pulse **Activar** para habilitar esta característica, y **Desactivar** para deshabilitarla.

1. Pulse **Guardar**.

Tras habilitar esta característica, puede tardar varias horas hasta que el gráfico muestre los costos estimado y previsto. Esto se debe a que un servicio se ejecuta cada hora para recopilar esta información, pero ejecuta los datos actualizados que se recopilan algunas horas después. Por ejemplo, supongamos que inicia una máquina virtual a la 1:00 a.m. El costo asociado a esa máquina virtual probablemente no se incorpore al gráfico de costos durante un par de horas.
 
La siguiente captura de pantalla muestra un ejemplo de gráfico de costos.

![Gráfico de costos](./media/devtest-lab-configure-cost-management/graph.png)

El valor **Costo estimado** es el costo estimado hasta la fecha del mes de calendario actual mientras que el **Costo previsto** es el costo estimado para todo el mes de calendario actual.

Como indica anteriormente el gráfico, los costos que se ven en el gráfico son costos *estimados* con tarifas de oferta de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Además, en el cálculo de costos *no* se incluye lo siguiente:

- Las tarifas de su oferta. Actualmente, no es posible usar las tarifas de su oferta (que aparecen en su suscripción) negociadas con Microsoft o con asociados de Microsoft. Se usan tarifas de pago por uso.
- Los impuestos
- Los descuentos
- La divisa de facturación. Actualmente, el costo de laboratorio solo se muestra en divisa USD.

## Pasos siguientes

Vea algunas acciones que puede probar a continuación:

- [Definición de directivas de laboratorio](./devtest-lab-set-lab-policy.md): aprenda a establecer las distintas directivas que se usan para controlar el uso del laboratorio y sus máquinas virtuales. 
- [Creación de una imagen personalizada del laboratorio de desarrollo y pruebas desde un archivo VHD](./devtest-lab-create-template.md): cuando cree una máquina virtual, deberá especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
- [Configuración de valores de imágenes de Azure Marketplace en un laboratorio de desarrollo y pruebas](./devtest-lab-configure-marketplace-images.md): Laboratorios de desarrollo y pruebas admite la creación de máquinas virtuales a partir de imágenes de Azure Marketplace. Este artículo muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales nuevas en un laboratorio.
- [Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas](./devtest-lab-add-vm-with-artifacts.md): este artículo muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

<!---HONumber=AcomDC_0518_2016-->