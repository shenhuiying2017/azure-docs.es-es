---
title: "Publicación de una aplicación web en una máquina virtual de Azure desde Visual Studio| Microsoft Docs"
description: "Publique una aplicación web ASP.NET en una máquina virtual de Azure desde Visual Studio."
services: virtual-machines-windows
documentationcenter: 
author:
- kraigb
- justcla
manager: ghogen
editor: 
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2017
ms.author:
- kraigb
- justcla
ms.openlocfilehash: 74ac1f27620e4871c3c6972baa949c64d850fe4c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publicación de una aplicación web ASP.NET en una máquina virtual de Azure desde Visual Studio

En este documento se describe cómo publicar una aplicación web ASP.NET en una máquina virtual de Azure mediante la característica de publicación **Microsoft Azure Virtual Machines**en Visual Studio 2017.  

## <a name="prerequisites"></a>Requisitos previos
A fin de utilizar Visual Studio para publicar un proyecto de ASP.NET en una máquina virtual de Azure, la máquina virtual debe estar correctamente configurada.

- La máquina debe configurarse para ejecutar una aplicación web ASP.NET y tener WebDeploy instalado.

- La máquina virtual debe tener un nombre DNS configurado. Para obtener más información, consulte [Creación de un nombre de dominio completo en Azure Portal para una máquina virtual Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publicación de la aplicación web ASP.NET en la máquina virtual de Azure con Visual Studio
En la siguiente sección se describe cómo publicar una aplicación web ASP.NET existente en una máquina virtual de Azure.

1. Abra la solución de la aplicación web en Visual Studio 2017.
2. Haga clic con el botón derecho en el Explorador de soluciones y elija **Publicar...**
3. Con la flecha situada a la derecha de la página, desplácese por las opciones de publicación hasta que encuentre **Microsoft Azure Virtual Machines**.  

   ![Página de publicación: flecha derecha]

4. Seleccione el icono **Microsoft Azure Virtual Machines** y luego **Publicar**.

   ![Página de publicación: icono de Microsoft Azure Virtual Machine]

5. Elija la cuenta adecuada (con la suscripción de Azure conectada a la máquina virtual).  
   - Si ha iniciado sesión en Visual Studio, la lista de cuentas se completa con todas las cuentas autenticadas.  
   - Si no ha iniciado sesión, o si la cuenta que necesita no aparece, elija "Agregar una cuenta..." y siga las indicaciones para iniciar sesión.  
   ![Selector de cuentas de Azure]  

6. Seleccione la máquina virtual adecuada en la lista de máquinas virtuales existentes.

   > [!Note]
   > Esta lista puede tardar algún tiempo en completarse.

   ![Selector de máquinas virtuales de Azure]

7. Haga clic en Aceptar para empezar a publicar.

8. Cuando se le pidan las credenciales, proporcione el nombre de usuario y la contraseña de una cuenta de usuario en la máquina virtual de destino que esté configurada con derechos de publicación (normalmente el nombre de usuario de administrador y la contraseña que se usó al crear la máquina virtual).  

   ![Inicio de sesión de WebDeploy]

9. Acepte el certificado de seguridad.

   ![Error de certificado]

10. Vea la ventana de salida para comprobar el progreso de la operación de publicación.

    ![Ventana de salida]

11. Si la publicación se completa correctamente, se iniciará un explorador para abrir la dirección URL del sitio recién publicado.

**Correcto**

Ya ha publicado correctamente la aplicación web en una máquina virtual de Azure.

## <a name="publish-page-options"></a>Opciones de la página de publicación

Después de completar al Asistente para publicación, se abrirá la página de publicación en el documento con el nuevo perfil de publicación seleccionado.

### <a name="re-publish"></a>Republicación

Para publicar actualizaciones en la aplicación web, seleccione el botón **Publicar** en la página de publicación.  
- Si se le pide, escriba su nombre de usuario y su contraseña.  
- La publicación se inicia de inmediato.

![Página de publicación: botón Publicar]

### <a name="modify-publish-profile-settings"></a>Modificación de la configuración del perfil de publicación

Para ver y modificar la configuración del perfil de publicación, seleccione **Configuración...**  

![Página de publicación: botón Configuración]

La configuración debería ser similar a la que se muestra a continuación:  

![Configuración de publicación: página de conexión]

#### <a name="save-user-name-and-password"></a>Guardado de nombre de usuario y contraseña
- Para no tener que proporcionar la información de autenticación cada vez que publique, puede rellenar los campos **Nombre de usuario** y **Contraseña** y activar la casilla **Guardar contraseña**.
- Use el botón **Validar conexión** para confirmar que ha escrito la información correcta.

#### <a name="deploy-to-clean-web-server"></a>Implementación en servidor web limpio

- Si desea asegurarse de que el servidor web tenga una copia limpia de la aplicación web después de cada carga (y que no se quede ningún otro archivo pendiente de una implementación anterior), puede activar la casilla **Quitar archivos adicionales en el destino	** en la pestaña **Configuración**.

- Advertencia: La publicación con este parámetro elimina todos los archivos existentes en el servidor web (directorio wwwroot). Asegúrese de que conoce el estado de la máquina antes de publicar con esta opción habilitada. 

![Configuración de publicación: página de configuración]

## <a name="next-steps"></a>Pasos siguientes

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Configuración de la integración o la entrega continua para la implementación automatizada en Azure VM

Para configurar una canalización de entrega continua con Visual Studio Team Services, consulte [Deploy to a Windows Virtual Machine](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups) (Implementación en una máquina virtual Windows).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Página de publicación: flecha derecha]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Página de publicación: icono de Microsoft Azure Virtual Machine]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selector de cuentas de Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selector de máquinas virtuales de Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Inicio de sesión de WebDeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Error de certificado]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Ventana de salida]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Página de publicación: botón Publicar]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Página de publicación: botón Configuración]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Configuración de publicación: página de conexión]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Configuración de publicación: página de configuración]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
