<properties
	pageTitle="Tendencia de costos estimados mensuales | Microsoft Azure"
	description="Obtenga información sobre el gráfico de tendencias de costos estimados mensuales de DevTest Labs."
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
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# Tendencia de costo estimado mensual

## Información general

La característica de administración de costos de Laboratorios de desarrollo y pruebas le ayuda a controlar el costo del laboratorio. En este artículo se muestra el uso del gráfico **Monthly Estimated Cost Trend** (Tendencia de costos estimados mensuales) para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo a fin de mes previsto para el mes de calendario actual.

## Visualización del gráfico de tendencias de costo estimado mensual

Para ver el gráfico de tendencias de costo estimado mensual, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**, y, a continuación, seleccione **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. Seleccione **Configuración**.

	![Settings](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. En la hoja **Configuración** del laboratorio, en **Cost Management** (Administración de costos), seleccione **Umbrales**.

	![Menú](./media/devtest-lab-configure-cost-management/menu.png)
 
1. La siguiente captura de pantalla muestra un ejemplo de gráfico de costos.

    ![Gráfico de costos](./media/devtest-lab-configure-cost-management/graph.png)

El valor **Estimated cost** (Costo estimado) es el costo estimado hasta la fecha del mes natural en curso, mientras que **Projected cost** (Costo previsto) muestra el costo estimado de todo el mes natural en curso, y se calcula mediante los costos del laboratorio de los cinco días anteriores.
 
Tenga en cuenta que los importes de los costos se redondean al siguiente número entero. Por ejemplo:

- 5,01 se redondea a 6.
- 5,50 se redondea a 6.
- 5,99 se redondea a 6.

Como indica anteriormente el gráfico, los costos que se ven en el gráfico son costos *estimados* con tarifas de oferta de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Además, en el cálculo de costos *no* se incluye lo siguiente:

- Las suscripciones de DreamSpark y CSP no se admiten, puesto que DevTest Labs emplea las [API de facturación de Azure](../billing-usage-rate-card-overview.md) para calcular el costo del laboratorio, y las API de facturación de Azure no admiten suscripciones de CSP ni Dreamspark.
- Las tarifas de su oferta. Actualmente, no es posible usar las tarifas de su oferta (que aparecen en su suscripción) negociadas con Microsoft o con asociados de Microsoft. Se usan tarifas de pago por uso.
- Los impuestos
- Los descuentos
- La divisa de facturación. Actualmente, el costo de laboratorio solo se muestra en divisa USD.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Entradas blogs relacionadas

- [Two more things to keep your cost on track in DevTest Labs (Dos cosas para mantener el costo por el buen camino en DevTest Labs)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
- [Why Cost Thresholds? (¿Por qué los umbrales de costo?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## Pasos siguientes

Vea algunas acciones que puede probar a continuación:

- [Definición de directivas de laboratorio](./devtest-lab-set-lab-policy.md): descubra cómo establecer las distintas directivas que se emplean para controlar el uso del laboratorio y sus máquinas virtuales.
- [Creación de una imagen personalizada](./devtest-lab-create-template.md): cuando cree una máquina virtual, deberá especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
- [Configuración de imágenes de Marketplace](./devtest-lab-configure-marketplace-images.md): DevTest Labs admite la creación de máquinas virtuales nuevas basadas en imágenes de Azure Marketplace. Este artículo muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales nuevas en un laboratorio.
- [Creación de una máquina virtual en un laboratorio](./devtest-lab-add-vm-with-artifacts.md): se muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

<!---HONumber=AcomDC_0831_2016-->