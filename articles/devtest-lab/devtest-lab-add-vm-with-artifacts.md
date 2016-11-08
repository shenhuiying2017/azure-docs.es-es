---
title: Incorporación de una máquina virtual con artefactos a un laboratorio en Azure DevTest Labs | Microsoft Docs
description: Aprenda a agregar una máquina virtual con artefactos a Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: tarcher

---
# Incorporación de una máquina virtual con artefactos a un laboratorio en Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-create-VMs-with-Artifacts-in-a-DevTest-Lab/player]
> 
> 

Puede crear una máquina virtual en un laboratorio a partir de una *base* que puede ser una [imagen personalizada](devtest-lab-create-template.md), una [fórmula](devtest-lab-manage-formulas.md), o bien una [imagen de Marketplace](devtest-lab-configure-marketplace-images.md).

Los *artefactos* de DevTest Labs le permiten especificar las *acciones* que se realizan cuando se crea la máquina virtual.

Las acciones del artefacto pueden realizar procedimientos como la ejecución de scripts de Windows Powershell y comandos de Bash, así como la instalación de software.

Los *parámetros* del artefacto le permiten personalizar el artefacto para su escenario en particular.

En este artículo se muestra cómo crear una máquina virtual en el laboratorio con artefactos.

## Incorporación de una máquina virtual con artefactos
1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y luego seleccione **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione aquel en el que desea crear la máquina virtual.
4. En la hoja **Información general** del laboratorio, seleccione **+ Virtual Machine** (+ Máquina virtual). ![Botón Agregar VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)
5. En la hoja **Elegir una base**, seleccione una base para la máquina virtual.
6. En la hoja **Máquina virtual**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de la máquina virtual**.
   
    ![Hoja de la máquina virtual de laboratorio](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)
7. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.
8. Si quiere usar una contraseña almacenada en su *almacén secreto*, seleccione **Use secrets from my secret store** (Usar secretos de mi almacén secreto), y especifique un valor de clave que corresponda a su secreto (contraseña). De lo contrario, simplemente escriba una contraseña en el campo de texto **Escriba un valor**.
9. Seleccione **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
10. Seleccione **Red virtual** y seleccione la que desee.
11. Seleccione **Subred** y seleccione la subred.
12. Si se establece la directiva de laboratorio para permitir direcciones IP públicas para la subred seleccionada, especifique si desea que la dirección IP sea pública seleccionando **Sí** o **No**. De lo contrario, se deshabilita esta opción y se selecciona como **No**.
13. Seleccione **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base. **Nota:** Si no está familiarizado con DevTest Labs o la configuración de artefactos, vaya a la sección [Incorporación de un artefacto existente a una máquina virtual](#add-an-existing-artifact-to-a-vm) y vuelva aquí cuando haya finalizado.
14. Si desea ver o copiar la plantilla de Azure Resource Manager, vaya a la sección [Almacenamiento de una plantilla de Azure Resource Manager](#save-arm-template) y vuelva aquí cuando haya finalizado.
15. Seleccione **Crear** para agregar la máquina virtual especificada al laboratorio.
16. La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **En creación** y luego como **En ejecución**, una vez que se haya iniciado la máquina virtual.
17. Vaya a la sección [Pasos siguientes](#next-steps).

## Incorporación de un artefacto existente a una máquina virtual
Al crear una máquina virtual, puede agregar artefactos existentes. Cada laboratorio incluye artefactos procedentes del repositorio de artefactos de DevTest Labs público, así como los artefactos que ha creado y agregado a su propio repositorio de artefactos. Para descubrir cómo crear artefactos, consulte el artículo [Creación de artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).

1. En la hoja **Máquina virtual**, seleccione **Artefactos**.
2. En la hoja **Agregar artefactos**, seleccione el artefacto deseado.
   
    ![Hoja de Agregar artefactos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)
3. Escriba los valores de parámetro necesarios y cualquier otro parámetro opcional que necesite.
4. Seleccione **Agregar** para agregar el artefacto y vuelva a la hoja **Agregar artefactos**.
5. Siga agregando los artefactos que necesite para la máquina virtual.
6. Cuando haya agregado los artefactos, puede [cambiar el orden en que se ejecutan](#change-the-order-in-which-artifacts-are-run). También puede volver atrás para [ver o modificar un artefacto](#view-or-modify-an-artifact).

## Cambio del orden en que se ejecutan los artefactos
De forma predeterminada, las acciones de los artefactos se ejecutan en el orden en que se agregan a la máquina virtual. Los siguientes pasos muestran cómo cambiar el orden en que se ejecutan los artefactos.

1. En la parte superior de la hoja **Agregar artefactos**, seleccione el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.
   
    ![Número de artefactos que se agregan a la máquina virtual](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. Para especificar el orden en que quiere que se ejecuten los artefactos, arrástrelos y colóquelos en el orden deseado. **Nota:** Si tiene problemas para arrastrar el artefacto, asegúrese de que lo arrastra desde el lado izquierdo del artefacto.
3. Seleccione **Aceptar** cuando haya terminado.

## Visualización o modificación de un artefacto
Los siguientes pasos muestran cómo ver o modificar los parámetros de un artefacto:

1. En la parte superior de la hoja **Agregar artefactos**, seleccione el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.
   
    ![Número de artefactos que se agregan a la máquina virtual](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. En la hoja **Artefactos seleccionados**, seleccione el artefacto que desea ver o modificar.
3. En la hoja **Agregar artefacto**, realice los cambios necesarios y seleccione **Aceptar** para cerrar la hoja **Agregar artefacto**.
4. Seleccione **Aceptar** para cerrar la hoja **Selected Artifacts** (Artefactos seleccionados).

## Guardar plantillas de Azure Resource Manager
Una plantilla de Azure Resource Manager proporciona una manera declarativa de definir una implementación repetible. Los siguientes pasos explican cómo guardar la plantilla de Azure Resource Manager para la máquina virtual que se va a crear. Una vez guardada, puede utilizar la plantilla de Azure Resource Manager para [implementar nuevas máquinas virtuales con Azure PowerShell](../resource-group-overview.md#template-deployment).

1. En la hoja **Máquina virtual**, seleccione **View ARM Template** (Ver plantilla de ARM).
2. En la hoja **View Azure Resource Manager Template** (Ver plantilla de Azure Resource Manager), seleccione todo el texto de la plantilla.
3. Copie el texto seleccionado en el Portapapeles.
4. Seleccione **Aceptar** para cerrar la hoja **View Azure Resource Manager Template** (Ver plantilla de Azure Resource Manager).
5. Abra un editor de texto.
6. Pegue el texto de la plantilla desde el Portapapeles.
7. Guarde el archivo para su uso posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Pasos siguientes
* Una vez creada la máquina virtual, puede conectarse a la misma seleccionando **Conectar** en la hoja de la máquina virtual.
* Aprenda a [crear artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).
* Explore la [galería de plantillas de inicio rápido de ARM de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

<!---HONumber=AcomDC_0907_2016-->