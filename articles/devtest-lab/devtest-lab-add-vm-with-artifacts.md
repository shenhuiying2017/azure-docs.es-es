<properties
	pageTitle="Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas | Microsoft Azure"
	description="Aprenda cómo agregar una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas

> [AZURE.NOTE] Haga clic en el vínculo siguiente para ver el vídeo que acompaña a este artículo: [Cómo crear VM con artefactos en un laboratorio de desarrollo y pruebas](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## Información general

Puede crear una máquina virtual en un laboratorio de desarrollo y pruebas a partir de una imagen base que sea una [imagen personalizada](./devtest-lab-create-template.md) o bien una imagen de Marketplace.

Los *artefactos* del Laboratorio de desarrollo y pruebas le permiten especificar las *acciones* que se realizan cuando se crea la máquina virtual.

Las acciones del artefacto pueden realizar procedimientos como la ejecución de scripts de Windows Powershell y comandos de Bash, así como la instalación de software.

Los *parámetros* del artefacto le permiten personalizar el artefacto para su escenario en particular.

En este artículo se muestra cómo crear una máquina virtual en el laboratorio con artefactos.

## Incorporación de una máquina virtual con artefactos

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio en el que desea crear la nueva máquina virtual.

1. En la hoja del laboratorio, pulse **+ Máquina virtual de laboratorio** tal como se muestra en la ilustración siguiente. ![Hoja principal del Laboratorio de desarrollo y pruebas](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. En la hoja **Máquina virtual de laboratorio**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de máquina virtual de laboratorio**.

1. Pulse **Base / Configurar los valores obligatorios** y seleccione una imagen base para la máquina virtual.

    ![Configuración de la máquina virtual de laboratorio](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Después de seleccionar una imagen base y pulsar **Aceptar**, se ampliará la hoja **Máquina virtual de laboratorio** para incluir elementos de interfaz de usuario para especificar información de cuenta de usuario, incluido **Nombre de usuario**, **Tipo de autenticación** (si el tipo de sistema operativo de la base seleccionada es Linux) y **Contraseña** (suponiendo que el tipo de autenticación es *Contraseña*).

    ![Hoja de Máquina virtual de laboratorio ampliada](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.

1. Si el tipo de sistema operativo de la base seleccionada es Linux, especifique el tipo de autenticación *Contraseña* o *Clave pública SSH*.

1. Según el tipo de autenticación especificado, escriba una contraseña o una clave pública SSH.

1. Pulse **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.

1. Pulse **Red virtual** y seleccione la red virtual deseada.

1. Pulse **Subred** y seleccione la subred.

1. Si se establece la directiva de laboratorio para permitir las direcciones IP públicas para la subred seleccionada, especifique si desea que la dirección IP sea pública seleccionando **Sí** o **No**. De lo contrario, se deshabilita esta opción y se selecciona como **No**.

1. Pulse **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base. **Nota:** Si no está familiarizado con los Laboratorios de desarrollo y pruebas o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.

1. Pulse **Crear** para agregar la máquina virtual especificada al laboratorio.

1. La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **En creación** y luego como **En ejecución**, una vez que se haya iniciado la máquina virtual.

1. Vaya a la sección [Pasos siguientes](#next-steps).

## Incorporación de un artefacto existente a una máquina virtual

Al crear una máquina virtual, puede agregar artefactos existentes. Cada laboratorio incluye artefactos procedentes del repositorio de artefactos del Laboratorio de desarrollo y pruebas público, así como los artefactos que ha creado y agregado a su propio repositorio de artefactos. Para descubrir cómo crear artefactos, vea el artículo [Aprenda a crear sus propios artefactos para usarlos con laboratorios de desarrollo y pruebas](devtest-lab-artifact-author.md).

1. En la hoja **Máquina virtual de laboratorio**, pulse **Artefactos**. 

1. En la hoja **Agregar artefactos**, pulse el artefacto deseado.

![Hoja de Agregar artefactos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. Escriba los valores de parámetro necesarios y cualquier otro parámetro opcional que necesite.  

1. Pulse **Agregar** para agregar el artefacto y vuelva a la hoja **Agregar artefactos**.

1. Siga agregando los artefactos que necesite para la máquina virtual.

1. Cuando haya agregado los artefactos, puede [cambiar el orden en que se ejecutan](#change-the-order-in-which-artifacts-are-run). También puede volver atrás para [ver o modificar un artefacto](#view-or-modify-an-artifact).

## Cambio del orden en que se ejecutan los artefactos

De forma predeterminada, las acciones de los artefactos se ejecutan en el orden en que se agregan a la máquina virtual. Los siguientes pasos muestran cómo cambiar el orden en que se ejecutan los artefactos.

1. En la parte superior de la hoja **Agregar artefactos**, pulse el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Arrastre y coloque los artefactos de la lista de modo que reflejen el orden deseado. **Nota:** Si tiene problemas para arrastrar el artefacto, asegúrese de que lo arrastra desde el lado izquierdo del artefacto.

1. Pulse **Aceptar** cuando haya terminado.

## Visualización o modificación de un artefacto

Los siguientes pasos muestran cómo ver o modificar los parámetros de un artefacto:

1. En la parte superior de la hoja **Agregar artefactos**, pulse el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. En la hoja **Artefactos seleccionados**, pulse el artefacto que desea ver o modificar.

1. En la hoja **Agregar artefacto**, realice los cambios necesarios y pulse **Aceptar** para cerrar la hoja **Agregar artefacto**.

1. Pulse **Aceptar** para cerrar la hoja **Artefactos seleccionados**.

## Pasos siguientes

- Una vez creada la máquina virtual, puede conectarse a la máquina virtual pulsando **Conectar** en la hoja de la máquina virtual.
- Para descubrir cómo crear artefactos, vea el artículo [Aprenda a crear sus propios artefactos para usarlos con laboratorios de desarrollo y pruebas](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0316_2016-->