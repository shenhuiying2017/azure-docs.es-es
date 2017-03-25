---
title: "Creación de fórmulas en Azure DevTest Labs | Microsoft Docs"
description: "Aprenda a crear fórmulas de Azure DevTest Labs y a usarlas para crear máquinas virtuales."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c18b0eefb2d8bb43416e15c5b91cf10827f90089
ms.lasthandoff: 03/15/2017


---
# <a name="create-azure-devtest-labs-formulas"></a>Creación de fórmulas de Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

En este artículo se explica cómo crear una fórmula a partir de una base (una imagen personalizada, una imagen de Marketplace u otra fórmula) o una máquina virtual existente. 

## <a name="create-a-formula"></a>Creación de una fórmula
Cualquier usuario con permisos de *usuarios* de DevTest Labs puede crear máquinas virtuales tomando una fórmula como base. Hay dos maneras de crear fórmulas: 

* Desde una base: use esta forma cuando desee definir todas las características de la fórmula.
* Desde una máquina virtual de laboratorio existente: use esta forma cuando desee crear una fórmula basada en la configuración de una máquina virtual existente.

Para obtener más información sobre cómo agregar usuarios y permisos, consulte [Adición de propietarios y usuarios en Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Creación de una fórmula desde una base
Los siguientes pasos le guiarán por el proceso de creación de una fórmula a partir de una imagen personalizada, una imagen de Marketplace u otra fórmula.

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.

3. En la lista de laboratorios, seleccione el laboratorio que desee.  

4. En la hoja del laboratorio, seleccione **Fórmulas (bases reutilizables)**.
   
    ![Menú Fórmula](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. En la hoja **Fórmulas**, seleccione **+ Agregar**.
   
    ![Agregar una fórmula](./media/devtest-lab-create-formulas/add-formula.png)

6. En la hoja **Elegir una base** , seleccione la base (imagen personalizada, imagen de Marketplace o fórmula) a partir de la cual quiere crear la fórmula.
   
    ![Lista base](./media/devtest-lab-create-formulas/base-list.png)

7. En la hoja **Crear fórmula** , especifique los valores siguientes:
   
    * **Nombre de fórmula** : escriba un nombre para la fórmula. Este valor se mostrará en la lista de imágenes base al crear la máquina virtual. El nombre se valida a medida que lo escribe y, si no es válido, un mensaje le indicará los requisitos para un nombre válido.
    * **Descripción** : escriba una descripción significativa para la fórmula. Este valor está disponible desde el menú contextual de la fórmula al crear una máquina virtual.
    * **Nombre de usuario**: escriba un nombre de usuario al que se concederán privilegios de administrador.
    * **Contraseña** : escriba, o seleccione en la lista desplegable, un valor que esté asociado con el secreto (contraseña) que desee usar para el usuario especificado. Para obtener más información sobre los secretos, consulte [Azure DevTest Labs: Personal secret store](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/) (Azure DevTest Labs: almacén personal de secretos).
    * **Virtual machine disk type** (Tipo de disco de máquina virtual): especifique cualquier HDD (unidad de disco duro) o SSD (unidad de estado sólido) para indicar qué tipo de disco de almacenamiento se permite para las máquinas virtuales que se aprovisionan con esta imagen base.
    * **Tamaño de la máquina virtual**: seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear. 
    * **Artefactos**: seleccione esta opción para abrir la hoja **Agregar artefactos**, donde podrá seleccionar y configurar los artefactos que quiere agregar a la imagen base. Para obtener más información sobre los artefactos, vea [Administración de artefactos de máquina virtual en Azure DevTest Labs](./devtest-lab-add-vm-with-artifacts.md).
    * **Configuración avanzada**: seleccione esta opción para abrir la hoja **Avanzado**, donde podrá configurar las siguientes opciones:
        * **Red virtual** : especifique la red virtual que desee.
        * **Subred** : especifique la subred deseada.    
        * **Configuración de dirección IP**: especifique si desea direcciones IP públicas, privadas o compartidas. Para obtener información sobre las direcciones IP compartidas, consulte Understand shared IP addresses in Azure DevTest Labs (Las direcciones IP compartidas en Azure DevTest Labs).
        * **Make this machine claimable** (Poder reclamar esta máquina): poder reclamar una máquina significa que no se podrá asignar la propiedad durante su creación. En su lugar, los usuarios del laboratorio podrán asumir su propiedad ("reclamar") de la máquina en la hoja del laboratorio.     
    * **Imagen** : este campo muestra el nombre de la imagen base que seleccionó en la hoja anterior. 
     
       ![Crear fórmula](./media/devtest-lab-create-formulas/create-formula.png)

8. Seleccione **Crear** para crear la fórmula.

9. Cuando se ha creado la fórmula, se muestra en la lista de la hoja **Fórmulas**.

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
   
    ![Máquinas virtuales de laboratorios](./media/devtest-lab-create-formulas/my-vms.png)
5. En la hoja de la máquina virtual, seleccione **Crear fórmula (base reutilizable)**.
   
    ![Crear fórmula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. En la hoja **Crear fórmula**, escriba un **Nombre** y una **Descripción** para la nueva fórmula.
   
    ![Hoja Crear fórmula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Seleccione **Aceptar** para crear la fórmula.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Custom images or formulas? (¿Imágenes personalizadas o fórmulas?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Pasos siguientes
- [Incorporación de una máquina virtual a un laboratorio de Azure DevTest Labs](devtest-lab-add-vm.md)
- [Administración de fórmulas de Azure DevTest Labs](devtest-lab-manage-formulas.md)
