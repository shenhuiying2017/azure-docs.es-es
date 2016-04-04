<properties
	pageTitle="Configuración de una red virtual para un laboratorio de DevTest | Microsoft Azure"
	description="Aprenda a configurar una red virtual existente y la subred y a usarlas en una máquina virtual."
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
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# Configuración de una red virtual para un laboratorio de DevTest

## Información general

Como se explica en el artículo [Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas de Azure](devtest-lab-add-vm-with-artifacts.md), al crear una máquina virtual en un laboratorio, puede especificar una red virtual configurada (y subred) para esa máquina virtual. Una situación en la que se podría aplicar esto es si quiere poder acceder a los recursos de la red corporativa desde las máquinas virtuales mediante la red virtual que se ha configurado con ExpressRoute o VPN de sitio a sitio. En las siguientes secciones se muestra cómo agregar la red virtual existente a la configuración de red virtual del laboratorio de modo que esté disponible para seleccionarse al crear las máquinas virtuales.

## Configuración de una red virtual para un laboratorio de DevTest mediante el Portal de Azure
Los pasos siguientes le guiarán en el proceso de agregar una red virtual existente (y la subred) a un laboratorio de DevTest para que se puede utilizar al crear una máquina virtual en el mismo laboratorio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Se mostrará la hoja **Configuración** del laboratorio seleccionado.

1. Pulse **Redes virtuales**.

	![Las redes virtuales se pueden configurar desde la hoja Configuración del laboratorio.](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. En la hoja **Redes virtuales**, verá una lista de redes virtuales que se han configurado para el laboratorio actual, así como la red virtual predeterminada que se crea para el laboratorio.

1. Pulse **+ Agregar**.

	![Incorporación de una red virtual existente al laboratorio](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. En la hoja **Red virtual**, pulse **[Seleccionar red virtual]**.

	![Selección de una red virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. En la hoja **Elegir red virtual**, seleccione la red virtual deseada. La hoja muestra todas las redes virtuales que están en la misma región de la suscripción que el laboratorio.

1. Después de seleccionar una red virtual y volver a la hoja **Red virtual**, verá que se han habilitado varios campos.

	![Selección de una red virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. Especifique una descripción para la combinación de red virtual y laboratorio.

1. Para permitir que se utilice una subred en la creación de máquinas virtuales de laboratorio, marque la opción **USAR EN LA CREACIÓN DE VM**.

1. Para permitir direcciones IP públicas en una subred, marque la opción **Permitir IP pública**.

1. En el campo **MÁXIMO DE VM POR USUARIO**, especifique el número máximo de máquinas virtuales por usuario para cada subred. Si quiere un número ilimitado de máquinas virtuales, deje este campo en blanco.

1. Pulse **Guardar**.

1. Ahora que está configurada la red virtual, se puede seleccionar al crear una nueva máquina virtual. Esto se explica en el artículo [Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas de Azure](devtest-lab-add-vm-with-artifacts.md).

## Pasos siguientes

Después de agregar las redes virtuales deseadas al laboratorio, el paso siguiente consiste en [agregar una máquina virtual al laboratorio de DevTest](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0323_2016-->