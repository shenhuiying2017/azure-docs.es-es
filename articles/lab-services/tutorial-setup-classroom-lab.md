---
title: Configuración de un laboratorio con Azure Lab Services | Microsoft Docs
description: En este tutorial, va a configurar un laboratorio para usarlo en una clase.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configuración de un laboratorio de clase 
En este tutorial, se va a configurar un laboratorio con un conjunto de máquinas virtuales que van a utilizar los estudiantes en la clase.  

En este tutorial realizará lo siguiente:

> [!div class="checklist"]
> * Creación de un laboratorio educativo
> * Configuración del laboratorio de clase
> * Envío del vínculo de registro a los estudiantes

## <a name="create-a-classroom-lab"></a>Creación de un laboratorio educativo

1. Vaya al [sitio web de Azure Lab Services](https://labs.azure.com).
2. En la ventana **Nuevo laboratorio**, lleve a cabo las siguientes acciones: 
    1. Escriba un **nombre** para el laboratorio de clase. 
    2. Seleccione el **tamaño** de la máquina virtual que se va a usar en la clase.
    3. Seleccione la **imagen** utilizada para crear la máquina virtual.
    4. Especifique las **credenciales predeterminadas** para el registro en máquinas virtuales en el laboratorio. 
    7. Seleccione **Guardar**.

        ![Creación de un laboratorio educativo](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Verá la **página principal** del laboratorio. 
    
    ![Página principal del laboratorio de clase](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configuración de la política de uso

1. Seleccione **Política de uso**. 
2. En **Política de uso**, Configuración, escriba el **número de usuarios** con permiso para usar el laboratorio.
3. Seleccione **Guardar**. 

    ![Política de uso](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configuración de la plantilla 
Una plantilla del laboratorio es desde la cual se crean todas las máquinas virtuales de los usuarios. Configure la máquina virtual de la plantilla de modo que esté configurada exactamente con lo que desea proporcionar a los usuarios del laboratorio. Puede proporcionar un nombre y una descripción de la plantilla que verán los usuarios del laboratorio, y establecer la visibilidad en "Público" para que las instancias de la máquina virtual de plantilla estén disponibles para los usuarios del laboratorio. 

### <a name="set-title-and-description"></a>Establecimiento del título y descripción
1. En la sección **Plantilla**, seleccione **Editar** (icono de lápiz) para la plantilla. 
2. En la ventana **Vista de usuario**, escriba un **título** para la plantilla.
3. Escriba la **descripción** para la plantilla.
4. Seleccione **Guardar**.

    ![Descripción del laboratorio de clase](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Conversión en públicas las instancias de la plantilla
Una vez establecida la visibilidad de una plantilla en **Público**, Azure Lab Services crea las máquinas virtuales en el laboratorio mediante la plantilla. El número de máquinas virtuales creadas en este proceso es el mismo que el número máximo de usuarios permitidos en el laboratorio, que puede establecer en la política de uso del laboratorio. Todas las máquinas virtuales tienen la misma configuración que la plantilla. 

1. Seleccione **Visibilidad** en la sección **Plantilla**. 
2. En la página **Disponibilidad**, seleccione **Público**.
    
    > [!IMPORTANT]
    > Cuando la plantilla está disponible públicamente, no se puede cambiar el acceso a privado. 
3. Seleccione **Guardar**.

    ![Disponibilidad](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Envío del vínculo de registro a los estudiantes

1. Seleccione el icono **Registro de usuario**.
2. En el cuadro de diálogo **Registro de usuario**, seleccione el botón **Copiar**. El vínculo se copia en el Portapapeles. Péguelo en un editor de correo electrónico y envíe un correo electrónico al estudiante. 

    ![Vínculo de registro del estudiante](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha creado un laboratorio de clase y ha configurado el laboratorio. Para saber cómo el estudiante puede acceder a una máquina virtual en el laboratorio con el enlace de registro, avance al siguiente tutorial:

> [!div class="nextstepaction"]
> [Conexión a una máquina virtual en el laboratorio de clase](tutorial-connect-virtual-machine-classroom-lab.md)

