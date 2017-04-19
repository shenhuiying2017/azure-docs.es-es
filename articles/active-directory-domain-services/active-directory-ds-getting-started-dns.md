---
title: "Azure Active Directory Domain Services: actualización de la configuración DNS para Azure Virtual Network | Microsoft Docs"
description: "Introducción a los Servicios de dominio de Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abb27292d4b5533fe6f3d66d6921fea8c82f18dd
ms.lasthandoff: 04/12/2017


---
# <a name="update-dns-settings-for-the-azure-virtual-network"></a>Actualización de la configuración DNS para Azure Virtual Network
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarea 4: Actualización de la configuración DNS para la red virtual de Azure
En las tareas de configuración anteriores, ha habilitado correctamente Azure Active Directory Domain Services para el directorio. La tarea siguiente consiste en asegurarse de que los equipos dentro de la red virtual pueden conectarse y consumir estos servicios. En este artículo, se actualiza la configuración del servidor DNS de la red virtual para que apunte a las direcciones IP en las que Azure Active Directory Domain Services está disponible en la red virtual.

> [!NOTE]
> Después de habilitar Azure Active Directory Domain Services para el directorio, anote las direcciones IP de Azure Active Directory Domain Services mostrados en la pestaña **Configurar** del directorio.
>
>

Para actualizar la configuración del servidor DNS en la red virtual en la que ha habilitado Azure Active Directory Domain Services, realice lo siguiente:

1. Vaya al [Portal de Azure clásico](https://manage.windowsazure.com).
2. En el panel izquierdo, seleccione **Redes**.  
    Se abre la ventana **Redes**.

    ![Ventana Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. En la pestaña **Redes virtuales** , seleccione la red virtual en la que habilitó Azure Active Directory Domain Services para ver sus propiedades.
4. Haga clic en la pestaña **Configure** .

    ![Ventana Redes virtuales](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. En la sección **Servidores DNS**, escriba las dos direcciones IP que se mostraron en la sección **Servicios de dominio** en la pestaña **Configurar** del directorio.
6. Para guardar la configuración del servidor DNS para esta red virtual, haga clic en **Guardar** en el panel de tareas de la parte inferior de la ventana.

   ![Actualización de la configuración del servidor DNS para la red virtual](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> Después de actualizar la configuración del servidor DNS para la red virtual, las máquinas virtuales de la red pueden tardar un rato en recibir la configuración DNS actualizada. Si una máquina virtual no puede conectarse al dominio, puede vaciar la caché DNS ("ipconfig /flushdns") en la máquina virtual. Este comando fuerza una actualización de la configuración DNS en la máquina virtual.
>
>

## <a name="next-steps"></a>Pasos siguientes
Tarea 5: [Habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

