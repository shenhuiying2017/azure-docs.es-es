---
title: "Habilitación de la conexión a Escritorio remoto para un rol en Azure Cloud Services | Microsoft Docs"
description: "Configuración de la aplicación de servicios en la nube de Azure para permitir conexiones a Escritorio remoto"
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: 0ff7fde5f3753aa6a24fb0af54d68d0dc0bd96a4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Habilitación de la conexión a Escritorio remoto para un rol de Azure Cloud Services
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Portal de Azure clásico](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)
>
>

Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar la conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta.

Puede habilitar una conexión a Escritorio remoto en el rol durante el desarrollo mediante la inclusión de los módulos de Escritorio remoto en la definición de servicio, o puede habilitar Escritorio remoto a través de la extensión de Escritorio remoto. El método preferido es usar la extensión de Escritorio remoto dado que puede habilitar Escritorio remoto incluso después de que se implementa la aplicación sin tener que volver a implementarla.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configuración de Escritorio remoto desde Azure Portal
Azure Portal usa el enfoque de extensión de Escritorio remoto, por lo que puede habilitar Escritorio remoto incluso después de que se implementa la aplicación. En la hoja **Escritorio remoto** de su servicio en la nube, puede habilitar Escritorio remoto, cambiar la cuenta de Administrador local usada para conectarse a las máquinas virtuales o el certificado que se usa en la autenticación y definir la fecha de caducidad.

1. Haga clic en **Cloud Services**, en el nombre del servicio en la nube y luego en **Escritorio remoto**.

    ![Escritorio remoto de Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Elija si desea habilitar Escritorio remoto para un rol individual o para todos los roles y luego cambie el valor del modificador para **Habilitado**.

3. Rellene los campos obligatorios correspondientes al nombre de usuario, la contraseña, la expiración y el certificado.

    ![Escritorio remoto de Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > se reiniciarán todas las instancias de rol la primera vez que habilite el Escritorio remoto y haga clic en Aceptar (marca de verificación). Para evitar un reinicio, el certificado que se usó para cifrar la contraseña debe instalarse en el rol. Para evitar un reinicio, [cargue un certificado para el servicio en la nube](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) y, luego, vuelva a este cuadro de diálogo.
   >
   >
3. En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **Todos** para todos los roles.

4. Después de terminar las actualizaciones de la configuración, haga clic en **Guardar**. Las instancias de rol tardarán unos minutos en estar listas para recibir conexiones.

## <a name="remote-into-role-instances"></a>Acceso remoto en instancias de rol
Una vez que Escritorio remoto está habilitado en los roles, puede iniciar una conexión directamente desde Azure Portal:

1. Haga clic en **Instancias** para abrir la hoja **Instancias**.
2. Seleccione una instancia de rol que tenga el Escritorio remoto configurado.
3. Haga clic en **Conectar** para descargar un archivo RDP para la instancia de rol.

    ![Escritorio remoto de Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Haga clic en **Abrir** y, a continuación, en **Conectar** para iniciar la conexión del Escritorio remoto.

>[!NOTE]
> Si su servicio en la nube se encuentra detrás de un NSG, quizás deba crear reglas que permitan el tráfico en los puertos **3389** y **20000**.  Escritorio remoto usa el puerto **3389**.  Las instancias del servicio en la nube tienen la carga equilibrada, por lo que no se puede controlar directamente a qué instancia conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* administran el tráfico RDP y permiten al cliente enviar una cookie de RDP y especificar una instancia concreta a la que conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* requieren que ese puerto **20000*** esté abierto, que podría estar bloqueado si tiene un grupo de seguridad de red.

## <a name="additional-resources"></a>Recursos adicionales

[How to Configure Cloud Services](cloud-services-how-to-configure.md) (Configuración de Cloud Services)
[Cloud services FAQ - Remote Desktop](cloud-services-faq.md) (Preguntas frecuentes sobre Cloud Services > Escritorio remoto)
