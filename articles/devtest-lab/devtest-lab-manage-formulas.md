<properties
	pageTitle="Manage DevTest Labs Formulas to create VMs (Administración de fórmulas de Laboratorios de desarrollo y pruebas para crear máquinas virtuales) | Microsoft Azure"
	description="Aprenda a crear, actualizar y quitar fórmulas de Laboratorios de desarrollo y pruebas y usarlas para crear máquinas virtuales."
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
	ms.date="04/22/2016"
	ms.author="tarcher"/>

# Manage DevTest Labs Formulas to create VMs (Administración de fórmulas de Laboratorios de desarrollo y pruebas para crear máquinas virtuales)

## Información general

Fórmulas: como las [imágenes personalizadas](./devtest-lab-create-template.md) y las [imágenes de Marketplace](./devtest-lab-configure-marketplace-images.md), ofrecen un mecanismo para el aprovisionamiento rápido de máquinas virtuales. Una fórmula de Laboratorios de desarrollo y pruebas es una lista de valores de propiedad predeterminados que se usan para crear una máquina virtual de laboratorio. Al crear una máquina virtual a partir de una fórmula, los valores predeterminados pueden usarse tal como están o modificarlos.

En este artículo aprenderá a realizar las siguientes tareas:

- [Creación de una fórmula](#create-a-new-formula)
- [Uso de una fórmula para crear una máquina virtual](#use-a-formula-to-create-a-new-vm)
- [Modificación de una fórmula](#modify-a-formula)
- [Eliminación de una fórmula](#delete-a-formula)

> [AZURE.NOTE] Las fórmulas son similares a las [imágenes personalizadas](./devtest-lab-create-template.md) en que cada una de ellas le permite crear una imagen base a partir de un disco duro virtual que se usa para aprovisionar una máquina virtual. Para que le sea más fácil decidir cuál es la adecuada para su entorno concreto, consulte el artículo [Comparación de imágenes personalizadas y fórmulas en Laboratorios de desarrollo y pruebas](./devtest-lab-comparing-vm-base-image-types.md).

## Creación de una fórmula
Cualquier usuario con permisos de *usuarios* de Laboratorios de desarrollo y pruebas puede crear máquinas virtuales en un laboratorio tomando una fórmula como base. Hay dos maneras de crear fórmulas:

- Desde cero: use esta forma cuando desee definir todas las características de la fórmula desde el principio.
- Desde una máquina virtual de laboratorio existente: use esta forma cuando desee crear una fórmula basada en la configuración de una máquina virtual existente.

### Creación de una fórmula desde cero
Los siguientes pasos le guiarán a través del proceso de creación de una nueva fórmula desde cero.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Se mostrará la hoja **Configuración** del laboratorio seleccionado.

1. En la hoja **Configuración** del laboratorio, pulse **Fórmulas**.

    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. En la hoja **Lab formulas** (Fórmulas de laboratorio), pulse **+ Agregar**.

    ![Incorporación de una nueva fórmula](./media/devtest-lab-manage-formulas/add-formula.png)

1. En la hoja **Elegir una base**, pulse la imagen personalizada o la imagen de Marketplace a partir de la que quiere crear la fórmula.

    ![Lista base](./media/devtest-lab-manage-formulas/base-list.png)

1. En la hoja **Crear fórmula**, especifique los valores siguientes:

	- **Nombre de fórmula**: escriba un nombre para la fórmula. Este valor se mostrará en la lista de imágenes base al crear la máquina virtual. El nombre se valida a medida que lo escribe y, si no es válido, un mensaje le indicará los requisitos para un nombre válido.
	- **Descripción**: escriba una descripción significativa para la fórmula. Este valor está disponible desde el menú contextual de la fórmula al crear una máquina virtual.
	- **Imagen**: este campo muestra el nombre de la imagen base que seleccionó en la hoja anterior. 
	- **Tamaño de máquina virtual**: pulse uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
	- **Red virtual**: pulse esta opción y seleccione la red virtual que desee.
	- **Subred**: pulse esta opción y seleccione la subred que desea.
	- **Dirección IP pública**: si la directiva de laboratorio se establece para permitir direcciones IP públicas para la subred seleccionada, especifique si desea que la dirección IP sea pública seleccionando **Sí** o **No**. De lo contrario, se deshabilita esta opción y se selecciona como **No**.
	- **Artefactos**: pulse esta opción y, en la lista de artefactos, seleccione y configure los artefactos que quiere agregar a la imagen base. Tenga en cuenta que los parámetros de artefacto que son cadenas seguras no se muestran, puesto que la fórmula no guarda los valores de cadena segura. 

    	![Crear fórmula](./media/devtest-lab-manage-formulas/create-formula.png)

1. Pulse **Crear** para crear la fórmula. Cuando se haya creado la fórmula en el portal, se mostrará en la hoja **Lab formulas** (Fórmulas de laboratorio).

	![Fórmula recién creada](./media/devtest-lab-manage-formulas/newly-created-formula.png)

### Creación de una fórmula desde una máquina virtual de laboratorio
Los siguientes pasos le guiarán a través del proceso de creación de una nueva fórmula basada en una máquina virtual existente.

> [AZURE.NOTE] Solo las máquinas virtuales creadas después del 30 de marzo de 2016 admiten la creación de una fórmula a partir de una máquina virtual de laboratorio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. En la hoja del laboratorio, busque la sección denominada **Mis máquinas virtuales** y haga clic en la máquina virtual a partir de la que quiere crear la fórmula.

	![Máquinas virtuales de laboratorios](./media/devtest-lab-manage-formulas/my-vms.png)

1. En la hoja **Configuración** de la máquina virtual, pulse **Crear fórmula**.

	![Crear fórmula](./media/devtest-lab-manage-formulas/create-formula-menu.png)

1. En la hoja **Crear fórmula**, escriba un **Nombre** y una **Descripción** para la nueva fórmula y pulse **Aceptar**. Cuando se haya creado la fórmula en el portal, se mostrará en la hoja **Lab formulas** (Fórmulas de laboratorio).

	![Hoja Crear fórmula](./media/devtest-lab-manage-formulas/create-formula-blade.png)

## Modificación de una fórmula
Para modificar una fórmula, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. En la hoja **Configuración** del laboratorio, pulse **Fórmulas**.

    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. En la hoja **Lab formulas** (Fórmulas de laboratorio), pulse la fórmula que quiere modificar.

1. En la hoja **Actualizar fórmula**, realice las modificaciones deseadas y pulse **Actualizar**.

## Eliminación de una fórmula 
Para eliminar una fórmula, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. En la hoja **Configuración** del laboratorio, pulse **Fórmulas**.

    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)

1. En La hoja **Lab formulas** (Fórmulas de laboratorio), haga clic en los puntos suspensivos a la derecha de la fórmula que desea eliminar.

    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)

1. En el menú contextual de la fórmula, seleccione **Eliminar**.

    ![Menú contextual de fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)

1. Pulse **Sí** en el cuadro de diálogo de confirmación de eliminación.

    ![Menú contextual de fórmula](./media/devtest-lab-manage-formulas/formula-delete-confirmation.png)

## Pasos siguientes
Cuando haya creado una fórmula para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual a su laboratorio de DevTest](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0427_2016-->