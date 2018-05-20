---
title: Administración de laboratorios de clase en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo crear y configurar un laboratorio de clase, ver todos los laboratorios de clase, compartir el vínculo de registro con un usuario del laboratorio o eliminar un laboratorio.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Administración de laboratorios de clase en Azure Lab Services (anteriormente, Azure DevTest Labs)
En este artículo se describe cómo crear y configurar un laboratorio de clase, ver todos los laboratorios de clase o eliminar un laboratorio de clase.

## <a name="create-a-classroom-lab"></a>Creación de un laboratorio de clase

1. Vaya a [sitio web de Azure Lab Services](https://labs.azure.com).
2. En la ventana **Nuevo laboratorio**, lleve a cabo las siguientes acciones: 
    1. Escriba un **nombre** para el laboratorio de clase. 
    2. Seleccione el **tamaño** de la máquina virtual que se va a usar en la clase.
    3. Seleccione la **imagen** utilizada para crear la máquina virtual.
    4. Especifique las **credenciales predeterminadas** para el registro en máquinas virtuales en el laboratorio.
    7. Seleccione **Guardar**.

        ![Creación de un laboratorio de clase](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Verá la **página principal** del laboratorio. 
    
    ![Página principal del laboratorio de clase](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configuración de la política de uso

1. Seleccione **Política de uso**. 
2. En **Política de uso**, Configuración, escriba el **número de usuarios** con permiso para usar el laboratorio.
3. Seleccione **Guardar**. 

    ![Política de uso](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configuración de la plantilla
Una plantilla del laboratorio es desde la cual se crean todas las máquinas virtuales de los usuarios. Configure la máquina virtual de la plantilla de modo que esté configurada exactamente con lo que desea proporcionar a los usuarios del laboratorio. Puede proporcionar un nombre y una descripción de la plantilla que verán los usuarios del laboratorio, y establecer la visibilidad en "Público" para que las instancias de la máquina virtual de plantilla estén disponibles para los usuarios del laboratorio.  

## <a name="set-template-title-and-description"></a>Establecimiento del título y descripción de la plantilla
1. En la sección **Plantilla**, seleccione **Editar** (icono de lápiz) para la plantilla. 
2. En la ventana **Vista de usuario**, escriba un **título** para la plantilla.
3. Escriba la **descripción** para la plantilla.
4. Seleccione **Guardar**.

    ![Descripción del laboratorio de clase](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Conversión en públicas las instancias de la plantilla 
Una vez establecida la visibilidad de una plantilla en **Público**, Azure Lab Services crea las máquinas virtuales en el laboratorio mediante la plantilla. El número de máquinas virtuales creadas en este proceso es el mismo que el número máximo de usuarios permitidos en el laboratorio, que puede establecer en la política de uso del laboratorio. Todas las máquinas virtuales tienen la misma configuración que la plantilla.  

1. Seleccione **Visibilidad** en la sección **Plantilla**. 
2. En la página **Disponibilidad**, seleccione **Público**.
    
    > [!IMPORTANT]
    > Cuando la plantilla está disponible públicamente, no se puede cambiar el acceso a privado. 
3. Seleccione **Guardar**.

    ![Disponibilidad](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Envío del vínculo de registro a los estudiantes

1. Seleccione el icono **Registro de usuario**.
2. En el cuadro de diálogo **Registro de usuario**, seleccione el botón **Copiar**. El vínculo se copia en el Portapapeles. Péguelo en un editor de correo electrónico y envíe un correo electrónico al estudiante. 

    ![Vínculo de registro del estudiante](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Visualización de todos los laboratorios de clase
1. Vaya al [portal de Azure Lab Services](https://labs.azure.com).
2. Confirme que ve todos los laboratorios en la cuenta de laboratorio seleccionada. 

    ![Todos los laboratorios](./media/how-to-manage-classroom-labs/all-labs.png)
3. Use la lista desplegable de la parte superior para seleccionar una cuenta de laboratorio distinta. Verá los laboratorios en la cuenta de laboratorio seleccionada. 

## <a name="delete-a-classroom-lab"></a>Eliminación de un laboratorio de clase
1. En el icono del laboratorio, seleccione los tres puntos (...) en la esquina. 

    ![Selección del laboratorio](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Seleccione **Eliminar**. 

    ![Botón Eliminar](./media/how-to-manage-classroom-labs/delete-button.png)
3. En el cuadro de diálogo **Delete lab** (Eliminar laboratorio), seleccione **Delete** (Eliminar). 

    ![Cuadro de diálogo Eliminar](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Pasos siguientes
Introducción a la configuración de un laboratorio con Azure Lab Services:

- [Configuración de un laboratorio educativo](how-to-manage-classroom-labs.md)
- [Configuración de un laboratorio personalizado](tutorial-create-custom-lab.md)
