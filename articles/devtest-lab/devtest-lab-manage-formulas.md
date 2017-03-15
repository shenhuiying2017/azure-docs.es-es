---
title: "Administración de fórmulas de Azure DevTest Labs para crear máquinas virtuales | Microsoft Docs"
description: "Aprenda a crear, actualizar y quitar fórmulas de Azure DevTest Labs y usarlas para crear máquinas virtuales."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 52590f0c47954d115f39cbe988e04a0ed3feb94d
ms.openlocfilehash: cd3352ae30734cce116fda5a838f95e5e0b86a8a
ms.lasthandoff: 02/27/2017


---
# <a name="manage-devtest-labs-formulas-to-create-vms"></a>Administración de fórmulas de DevTest Labs para crear máquinas virtuales
Una fórmula en Azure DevTest Labs es una lista de valores de propiedad predeterminados que se usan para crear una máquina virtual. Al crear una máquina virtual a partir de una fórmula, los valores predeterminados pueden usarse tal como están o modificarlos. Al igual que las [imágenes personalizadas](devtest-lab-create-template.md) y las [imágenes de Marketplace](devtest-lab-configure-marketplace-images.md), las fórmulas ofrecen un mecanismo para el aprovisionamiento rápido de máquinas virtuales.  

En este artículo aprenderá a realizar las siguientes tareas:

* [Creación de una fórmula](#create-a-formula)
* [Uso de una fórmula para aprovisionar una máquina virtual](#use-a-formula-to-provision-a-vm)
* [Modificación de una fórmula](#modify-a-formula)
* [Eliminación de una fórmula](#delete-a-formula)

> [!NOTE]
> Fórmulas: al igual que las [imágenes personalizadas](devtest-lab-create-template.md), permiten crear una imagen base desde un archivo VHD. La imagen base se puede usar luego para aprovisionar una nueva máquina virtual. Para que le sea más fácil decidir cuál es la adecuada para su entorno concreto, consulte el artículo [Comparación de imágenes personalizadas y fórmulas en DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
> 
> 

## <a name="create-a-formula"></a>Creación de una fórmula
Cualquier usuario con permisos de *usuarios* de DevTest Labs puede crear máquinas virtuales tomando una fórmula como base. Hay dos maneras de crear fórmulas: 

* Desde una base: use esta forma cuando desee definir todas las características de la fórmula.
* Desde una máquina virtual de laboratorio existente: use esta forma cuando desee crear una fórmula basada en la configuración de una máquina virtual existente.

### <a name="create-a-formula-from-a-base"></a>Creación de una fórmula desde una base
Los siguientes pasos le guiarán por el proceso de creación de una fórmula a partir de una imagen personalizada, una imagen de Marketplace u otra fórmula.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja del laboratorio, seleccione **Fórmulas (bases reutilizables)**.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. En la hoja **Lab formulas** (Fórmulas de laboratorio), seleccione **+ Agregar**.
   
    ![Agregar una fórmula](./media/devtest-lab-manage-formulas/add-formula.png)
6. En la hoja **Elegir una base** , seleccione la base (imagen personalizada, imagen de Marketplace o fórmula) a partir de la cual quiere crear la fórmula.
   
    ![Lista base](./media/devtest-lab-manage-formulas/base-list.png)
7. En la hoja **Crear fórmula** , especifique los valores siguientes:
   
   * **Nombre de fórmula** : escriba un nombre para la fórmula. Este valor se mostrará en la lista de imágenes base al crear la máquina virtual. El nombre se valida a medida que lo escribe y, si no es válido, un mensaje le indicará los requisitos para un nombre válido.
   * **Descripción** : escriba una descripción significativa para la fórmula. Este valor está disponible desde el menú contextual de la fórmula al crear una máquina virtual.
   * **Nombre de usuario** : escriba un nombre de usuario al que se concederán privilegios de administrador.
   * **Contraseña** : escriba, o seleccione en la lista desplegable, un valor que esté asociado con el secreto (contraseña) que desee usar para el usuario especificado.  
   * **Imagen** : este campo muestra el nombre de la imagen base que seleccionó en la hoja anterior. 
   * **Tamaño de la máquina virtual** : seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.
   * **Red virtual** : especifique la red virtual que desee.
   * **Subred** : especifique la subred deseada.
   * **Dirección IP pública**: si la directiva de laboratorio se establece para permitir direcciones IP públicas para la subred seleccionada, especifique si desea que la dirección IP sea pública seleccionando **Sí** o **No**. De lo contrario, se deshabilita esta opción y se selecciona como **No**.
   * **Artefactos** : seleccione y configure los artefactos que quiere agregar a la imagen base. Los valores de cadenas seguras no se guardan con la fórmula. Por lo tanto, no se muestran los parámetros de artefactos que sean cadenas seguras. 
     
       ![Crear fórmula](./media/devtest-lab-manage-formulas/create-formula.png)
8. Seleccione **Crear** para crear la fórmula.

### <a name="create-a-formula-from-a-vm"></a>Creación de una fórmula desde una máquina virtual
Los siguientes pasos le guiarán a través del proceso de creación de una fórmula basada en una máquina virtual existente. 

> [!NOTE]
> Para crear una fórmula desde una máquina virtual, la máquina virtual debe haberse creado después del 30 de marzo de 2016. 
> 
> 

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja **Información general** del laboratorio, seleccione la máquina virtual desde la que desee crear la fórmula.
   
    ![Máquinas virtuales de laboratorios](./media/devtest-lab-manage-formulas/my-vms.png)
5. En la hoja de la máquina virtual, seleccione **Crear fórmula (base reutilizable)**.
   
    ![Crear fórmula](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. En la hoja **Crear fórmula**, escriba un **Nombre** y una **Descripción** para la nueva fórmula.
   
    ![Hoja Crear fórmula](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. Seleccione **Aceptar** para crear la fórmula.

## <a name="use-a-formula-to-provision-a-vm"></a>Uso de una fórmula para aprovisionar una máquina virtual
Una vez creada una fórmula, puede crear una máquina virtual basada en esa fórmula. En el artículo [Incorporación de una máquina virtual con artefactos](devtest-lab-add-vm-with-artifacts.md) se le guiará a través del proceso.

## <a name="modify-a-formula"></a>Modificación de una fórmula
Para modificar una fórmula, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja del laboratorio, seleccione **Fórmulas (bases reutilizables)**.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. En la hoja **Lab formulas** (Fórmulas de laboratorio), seleccione la fórmula que quiere modificar.
6. En la hoja **Update formula** (Actualizar fórmula), realice las modificaciones deseadas y seleccione **Actualizar**.

## <a name="delete-a-formula"></a>Eliminación de una fórmula
Para eliminar una fórmula, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.  
4. En la hoja **Configuración** del laboratorio, seleccione **Fórmulas**.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. En la hoja **Lab formulas** (Fórmulas de laboratorio), seleccione los puntos suspensivos a la derecha de la fórmula que desea eliminar.
   
    ![Menú Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. En el menú contextual de la fórmula, seleccione **Eliminar**.
   
    ![Menú contextual de fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Seleccione **Sí** en el cuadro de diálogo de confirmación de eliminación.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Pasos siguientes
Cuando haya creado una fórmula para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual al laboratorio](devtest-lab-add-vm-with-artifacts.md).


