<properties
	pageTitle="Definición de directivas de laboratorio | Microsoft Azure"
	description="Obtenga información acerca de cómo definir directivas de laboratorio como tamaños de máquina virtual, máximo de máquinas virtuales por usuario y automatización de apagado."
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

# Definición de directivas de laboratorio

## Información general

DevTest Labs permite especificar directivas de clave que rigen cómo se usan el laboratorio y sus máquinas virtuales. Por ejemplo, puede definir las reglas para los tamaños de VM que se permiten para crear VM, el umbral para la cantidad de VM que se pueden crear y programar trabajos para iniciar y detener automáticamente las VM de laboratorio.

## Acceso a las directivas de un laboratorio

Para ver (y cambiar) las directivas para un laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Pulse **Configuración**.

	![Settings](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. En la hoja **Configuración**, hay un grupo de configuraciones llamado **Directivas de VM**.

	![Settings](./media/devtest-lab-set-lab-policy/policies.png)

	Pulse la directiva que quiera de la lista siguiente para obtener más información acerca de su configuración:

	- [Tamaños de máquina virtual permitidos](#set-allowed-vm-sizes): seleccione la lista de tamaños de máquinas virtuales que se permiten en el laboratorio. Un usuario puede crear máquinas virtuales solo desde esta lista.

	- [Máximo de máquinas virtuales por usuario](#set-maximum-vms-per-user): especifique el número máximo de máquinas virtuales que se pueden crear para un laboratorio, así como el número máximo de máquinas virtuales que puede crear un usuario.

	- [Total de máquinas virtuales permitidas](#set-total-vms-allowed): especifique el número máximo de máquinas virtuales que se pueden crear para un laboratorio, así como el número máximo de máquinas virtuales que puede crear un usuario.

	- [Apagado automático](#set-auto-shutdown): especifique la hora a la que se deben apagar las máquinas virtuales del laboratorio actual.

	- [Inicio automático](#set-auto-start): especifique la hora a la que se deben apagar las máquinas virtuales del laboratorio actual.

## Establecimiento de los tamaños de máquina virtual permitidos

La directiva para establecer los tamaños permitidos de la máquina virtual ayuda a minimizar la pérdida del laboratorio al permitirle especificar los tamaños de máquina virtual que se permiten en este. Si se activa esta directiva, los tamaños de máquina virtual de esta lista son los únicos que pueden utilizarse en la creación de tales máquinas.

1. En la hoja **Configuración** del laboratorio, en **Directivas de máquina virtual**, pulse **Tamaños de máquina virtual permitidos**.

	![Settings](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, pulse uno o varios tamaños de máquina virtual que se pueden crear en el laboratorio.

1. Pulse **Guardar**.

## Definir el máximo de máquinas virtuales por usuario

La directiva sobre el **máximo de máquinas virtuales por usuario** le permite especificar la cantidad máxima de máquinas virtuales que un usuario individual puede crear. Si un usuario intenta crear una nueva máquina virtual cuando se ha alcanzado el límite del usuario, un mensaje de error indicará que no se puede crear la máquina virtual.

1. En la hoja **Configuración** del laboratorio, en **Directivas de máquina virtual**, pulse **Máximo de máquinas virtuales por usuario**.

	![Settings](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, en el cuadro de texto **Máximo de máquinas virtuales permitidas por usuario**, escriba un valor numérico que indique el número máximo de máquinas virtuales que puede crear un usuario. Si escribe un número que no sea válido, la interfaz de usuario mostrará el número máximo permitido para este campo.

1. Pulse **Guardar**.

## Definir el total de máquinas virtuales permitidas

La directiva sobre el **total de máquinas virtuales permitidas** le permite especificar la cantidad máxima de máquinas virtuales que se pueden crear para el laboratorio actual. Si un usuario intenta crear una nueva máquina virtual cuando se ha alcanzado el límite del laboratorio, un mensaje de error indicará que no se puede crear la máquina virtual.

1. En la hoja **Configuración** del laboratorio, en **Directivas de máquina virtual**, pulse **Total de máquinas virtuales permitidas**.

	![Settings](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, en el cuadro de texto **Total de máquinas virtuales permitidas en este laboratorio**, escriba un valor numérico que indique el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual. Si escribe un número que no sea válido, la interfaz de usuario mostrará el número máximo permitido para este campo.

1. Pulse **Guardar**.

## Establecimiento del apagado automático

La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de la máquina virtual de este laboratorio.

1. En la hoja **Configuración** del laboratorio, en **Directivas de máquina virtual**, pulse **Apagado automático**.

	![Settings](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, especifique la hora local para apagar todas las máquinas virtuales en el laboratorio actual.

1. Pulse **Guardar**.

1. De manera predeterminada, una vez que se habilite, esta directiva se aplica a todas las máquinas virtuales del laboratorio actual. Para quitar esta configuración de una máquina virtual específica, abra la hoja de la máquina virtual y cambie la configuración de **Apagado automático**.

## Definir inicio automático

La directiva de inicio automático le permite especificar cuándo se deben iniciar las máquinas virtuales del laboratorio actual.

1. En la hoja **Configuración** del laboratorio, en **Directivas de máquina virtual**, pulse **Inicio automático**.

	![Settings](./media/devtest-lab-set-lab-policy/auto-start.png)

1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, especifique la hora de inicio local programada y los días de la semana a los que se aplica la hora.

1. Pulse **Guardar**.

1. Una vez que se habilite, esta directiva no se aplica automáticamente a ninguna máquina virtual del laboratorio actual. Para aplicar esta configuración a una máquina virtual específica, abra la hoja de la máquina virtual y cambie la configuración de **Inicio automático**.

## Pasos siguientes

Una vez que defina y aplique las distintas configuraciones de las directivas de máquina virtual correspondientes al laboratorio, puede intentar algunos de los siguientes pasos:

- [Configurar administración de costos](./devtest-lab-configure-cost-management.md): se muestra el uso del gráfico **Monthly Estimated Cost Trend** (Tendencia de costos estimados mensuales) para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo previsto para fin de mes.
- [Crear una imagen personalizada](./devtest-lab-create-template.md): cuando cree una máquina virtual, deberá especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
- [Configurar imágenes de Marketplace](./devtest-lab-configure-marketplace-images.md): Laboratorios de desarrollo y pruebas admite la creación de máquinas virtuales nuevas basadas en imágenes de Azure Marketplace. Este artículo muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales nuevas en un laboratorio.
- [Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas](./devtest-lab-add-vm-with-artifacts.md): este artículo muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

<!---HONumber=AcomDC_0518_2016-->