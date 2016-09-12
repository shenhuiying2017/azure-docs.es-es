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
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# Definición de directivas de laboratorio

> [AZURE.VIDEO how-to-set-vm-policies-in-a-devtest-lab]

## Información general

DevTest Labs permite especificar directivas de clave que rigen cómo se usan el laboratorio y sus máquinas virtuales. Por ejemplo, puede definir las reglas para los tamaños de VM que se permiten para crear VM, el umbral para la cantidad de VM que se pueden crear y programar trabajos para iniciar y detener automáticamente las VM de laboratorio.

## Acceso a las directivas de un laboratorio

Para ver (y cambiar) las directivas para un laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**, y, a continuación, seleccione **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. Seleccione **Configuración**.

	![Settings](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. En la hoja **Configuración**, hay un grupo de configuraciones llamado **VM Policies** (Directivas de máquina virtual).

	![Settings](./media/devtest-lab-set-lab-policy/policies.png)

	Seleccione la directiva que quiera de la lista siguiente para más información acerca de su configuración:

	- [Tamaños de máquina virtual permitidos](#set-allowed-vm-sizes): seleccione la lista de tamaños de máquinas virtuales que se permiten en el laboratorio. Un usuario puede crear máquinas virtuales solo desde esta lista.

	- [Maximum VMs per user](#set-maximum-vms-per-user) (Máximo de máquinas virtuales por usuario): especifique el número máximo de máquinas virtuales que un usuario puede crear.

	- [Total VMs allowed](#set-total-vms-allowed) (Total de máquinas virtuales permitidas): especifique el número máximo de máquinas virtuales que se pueden crear para un laboratorio.

	- [Auto shutdown](#set-auto-shutdown) (Apagado automático): especifique la hora a la que las máquinas virtuales del laboratorio actual deben apagarse automáticamente.

	- [Auto start](#set-auto-start) (Inicio automático): especifique la hora a la que las máquinas virtuales del laboratorio actual deben iniciarse automáticamente.

## Establecimiento de los tamaños de máquina virtual permitidos

La directiva para establecer los tamaños permitidos de la máquina virtual ayuda a minimizar la pérdida del laboratorio al permitirle especificar los tamaños de máquina virtual que se permiten en este. Si se activa esta directiva, los tamaños de máquina virtual de esta lista son los únicos que pueden utilizarse en la creación de tales máquinas.

1. En la hoja **Configuración** del laboratorio, en **VM Policies** (Directivas de máquina virtual), seleccione **Allowed VM Sizes** (Tamaños de máquina virtual permitidos).

	![Settings](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, seleccione uno o varios tamaños de máquina virtual que se pueden crear en el laboratorio.

1. Seleccione **Guardar**.

## Definir el máximo de máquinas virtuales por usuario

La directiva sobre el **máximo de máquinas virtuales por usuario** le permite especificar la cantidad máxima de máquinas virtuales que un usuario individual puede crear. Si un usuario intenta crear una nueva máquina virtual cuando se ha alcanzado el límite del usuario, un mensaje de error indicará que no se puede crear la máquina virtual.

1. En la hoja **Configuración** del laboratorio, en **VM Policies** (Directivas de máquina virtual), seleccione **Maximum VMs per user** (Máximo de máquinas virtuales por usuario).

	![Settings](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, en el cuadro de texto **Maximum VMs allowed per User** (Máximo de máquinas virtuales permitidas por usuario), escriba un valor numérico que indique el número máximo de máquinas virtuales que puede crear un usuario. Si escribe un número que no sea válido, la interfaz de usuario mostrará el número máximo permitido para este campo.

1. Seleccione **Guardar**.

## Definir el total de máquinas virtuales permitidas

La directiva sobre el **total de máquinas virtuales permitidas** le permite especificar la cantidad máxima de máquinas virtuales que se pueden crear para el laboratorio actual. Si un usuario intenta crear una nueva máquina virtual cuando se ha alcanzado el límite del laboratorio, un mensaje de error indicará que no se puede crear la máquina virtual.

1. En la hoja **Configuración** del laboratorio, en **VM Policies** (Directivas de máquina virtual), seleccione **Total VMs allowed** (Total de máquinas virtuales permitidas).

	![Settings](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, en el cuadro de texto **Total VMs allowed in this lab** (Total de máquinas virtuales permitidas en este laboratorio), escriba un valor numérico que indique el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual. Si escribe un número que no sea válido, la interfaz de usuario mostrará el número máximo permitido para este campo.

1. Seleccione **Guardar**.

## Establecimiento del apagado automático

La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de la máquina virtual de este laboratorio.

1. En la hoja **Configuración** del laboratorio, en **VM Policies** (Directivas de máquina virtual), seleccione **Auto shutdown** (Apagado automático).

	![Settings](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, especifique la hora local para apagar todas las máquinas virtuales en el laboratorio actual.

1. Seleccione **Guardar**.

1. De manera predeterminada, una vez que se habilite, esta directiva se aplica a todas las máquinas virtuales del laboratorio actual. Para quitar esta configuración de una máquina virtual específica, abra la hoja de la máquina virtual y cambie el valor de **Auto Shutdown** (Apagado automático)

## Definir inicio automático

La directiva de inicio automático le permite especificar cuándo se deben iniciar las máquinas virtuales del laboratorio actual.

1. En la hoja **Configuración** del laboratorio, en **VM Policies** (Directivas de máquina virtual), seleccione **Auto start** (Inicio automático).

	![Settings](./media/devtest-lab-set-lab-policy/auto-start.png)

1. Seleccione **Activado** para habilitar esta directiva, y **Desactivado** para deshabilitarla.

1. Si habilita esta directiva, especifique la hora de inicio local programada y los días de la semana a los que se aplica la hora.

1. Seleccione **Guardar**.

1. Una vez que se habilite, esta directiva no se aplica automáticamente a ninguna máquina virtual del laboratorio actual. Para aplicar este valor a una máquina virtual específica, abra la hoja de la máquina virtual y cambie el valor de **Auto Start** (Inicio automático)

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Pasos siguientes

Una vez que defina y aplique las distintas configuraciones de las directivas de máquina virtual correspondientes al laboratorio, puede intentar algunos de los siguientes pasos:

- [Configurar administración de costos](./devtest-lab-configure-cost-management.md): se muestra el uso del gráfico de **tendencias de costo estimado mensual** para ver el costo estimado hasta la fecha del mes de calendario actual, así como el costo previsto para fin de mes.
- [Creación de una imagen personalizada](./devtest-lab-create-template.md): cuando cree una máquina virtual, deberá especificar una base, que puede ser una imagen personalizada o una imagen de Marketplace. Este artículo muestra cómo crear una imagen personalizada desde un archivo VHD.
- [Configuración de imágenes de Marketplace](./devtest-lab-configure-marketplace-images.md): DevTest Labs admite la creación de máquinas virtuales nuevas basadas en imágenes de Azure Marketplace. Este artículo muestra cómo especificar las imágenes de Azure Marketplace, si corresponde, que se pueden usar en el momento de crear máquinas virtuales nuevas en un laboratorio.
- [Creación de una máquina virtual en un laboratorio](./devtest-lab-add-vm-with-artifacts.md): se muestra cómo crear una máquina virtual desde una imagen base (ya sea personalizada o de Marketplace) y cómo trabajar con artefactos en la máquina virtual.

<!---HONumber=AcomDC_0831_2016-->