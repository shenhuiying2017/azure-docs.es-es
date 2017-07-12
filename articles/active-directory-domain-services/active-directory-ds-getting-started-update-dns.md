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
ms.date: 06/27/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 8bee2a25f196d645b27f30f21305b1550e44e07a
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
<a id="update-dns-settings-for-the-azure-virtual-network" class="xliff"></a>

# Actualización de la configuración DNS para Azure Virtual Network
<a id="task-4-update-dns-settings-for-the-azure-virtual-network" class="xliff"></a>

## Tarea 4: Actualización de la configuración DNS para la red virtual de Azure
En las tareas de configuración anteriores, ha habilitado correctamente Azure Active Directory Domain Services para el directorio. La tarea siguiente consiste en asegurarse de que los equipos dentro de la red virtual pueden conectarse y consumir estos servicios. En este artículo, se actualiza la configuración del servidor DNS de la red virtual para que apunte a las direcciones IP en las que Azure Active Directory Domain Services está disponible en la red virtual.

> [!NOTE]
> Después de habilitar Azure Active Directory Domain Services para el directorio, anote las direcciones IP de Azure Active Directory Domain Services mostrados en la pestaña **Configurar** del directorio.
>
>

Para actualizar la configuración del servidor DNS en la red virtual en la que ha habilitado Azure Active Directory Domain Services, realice los siguientes pasos:

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
>  Las máquinas virtuales de la red solo obtendrán la nueva configuración DNS después de reiniciarse. Si necesita tener la configuración de DNS actualizada inmediatamente, desencadene un reinicio mediante el portal, PowerShell o la CLI.
>
>

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
Tarea 5: [Habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)

