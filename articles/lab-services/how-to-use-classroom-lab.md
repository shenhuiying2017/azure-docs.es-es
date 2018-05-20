---
title: Acceso a un laboratorio de clase en Azure Lab Services | Microsoft Docs
description: En este tutorial, va a acceder a máquinas virtuales en un laboratorio educativo configurado por un profesor.
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 3a24c826ffdd3e368b6b16f6e78fbafc50c54b1e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Acceso a un laboratorio de clase en Azure Lab Services
En este artículo se describe cómo acceder a un laboratorio de clase, conectarse a la máquina virtual en el laboratorio y detener la máquina virtual. 

## <a name="view-all-the-classroom-labs"></a>Visualización de todos los laboratorios de clase

1. Vaya a la **dirección URL de registro** que recibió del profesor o educador. 
2. Inicie sesión en el servicio con su cuenta organizativa para completar el registro. 
3. Una vez registrado, confirme que ve las máquinas virtuales de los laboratorios a los que tiene acceso. 

    ![Visualización de todos los laboratorios](./media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Conexión a la máquina virtual en un laboratorio de clase

1. Seleccione **Conectar** en el icono que representa la máquina virtual del laboratorio al que quiere acceder.

    ![Visualización de todos los laboratorios](./media/how-to-use-classroom-lab/connect-button.png)
2. La máquina virtual tarda unos minutos en estar lista.

    ![Preparación de la máquina virtual](./media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Guarde el archivo RDP en el disco duro y ábralo. 
    
    ![Guardado del archivo RDP](./media/how-to-use-classroom-lab/save-rdp-file.png)
4. Utilice el **nombre de usuario** y la **contraseña** que obtiene de su profesor o educador para iniciar sesión en la máquina. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Detención de la máquina virtual en un laboratorio de clase

Seleccione **Detener** en el icono que representa el laboratorio de clase. Cuando se detiene la máquina virtual, el botón **Iniciar** en el icono se habilita. 

## <a name="next-steps"></a>Pasos siguientes
Introducción a la configuración de un laboratorio con Azure Lab Services:

- [Configuración de un laboratorio de clase](how-to-manage-classroom-labs.md)
- [Configuración de un laboratorio personalizado](tutorial-create-custom-lab.md)

 