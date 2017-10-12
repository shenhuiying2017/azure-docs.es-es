---
title: "Habilitación del FTP en App Service en Azure Stack | Microsoft Docs"
description: Pasos necesarios para habilitar el FTP en App Service en Azure Stack
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Habilitación del FTP en App Service en Azure Stack

Una vez que ha implementado correctamente App Service en Azure Stack, si desea volver a habilitar la publicación de FTP, para que los inquilinos pueden cargar sus archivos de la aplicación y el contenido, hay algunos pasos más que se deben realizar.  Estos pasos se automatizarán en futuras versiones.

> [!NOTE]
> Además, están dirigidos a los administradores de empresas o servicios que configuran una instancia de App Service en el proveedor de recursos de Azure Stack.

## <a name="enable-ftp"></a>Habilitación del FTP

1.  Inicie sesión en el Portal de Azure Stack como administrador de servicios.
2.  Vaya a **Interfaces de red** y seleccione la **FTP NIC** en **Grupo de recursos** - **AppService-LOCAL**. ![Interfaces de red de Azure Stack][1]
3.  Anote la **dirección IP pública** de **FTP NIC**. 
![Detalles de la interfaz de red de Azure Stack][2]
4.  A continuación, vaya a **Máquinas virtuales** y seleccione **FTP0-VM**. ![Máquinas virtuales de Azure Stack][3]
5.  Abra una sesión de escritorio remoto en la máquina virtual mediante el botón **Conectar** e inicie sesión en la sesión con las credenciales de administrador establecidas durante la implementación de App Service.  
![Detalles de la máquina virtual de Azure Stack][4]
6.  Abra el **Administrador de Internet Information Services (IIS)** en la máquina virtual de FTP (FTP0-VM).
7.  En **Sitios**, seleccione **Hospedaje de sitio FTP**.
8.  Abra **Compatibilidad con el FTP Firewall**. ![Administrador de IIS en la máquina virtual FTP0-VM de App Service][5]
9.  Escriba la dirección IP pública de la NIC de FTP y haga clic en **Aplicar**![IIS Manager FTP Firewall Support][6] (Soporte de firewall de FTP del Administrador de IIS).

## <a name="validate-the-enabling-of-ftp"></a>Validación de la habilitación de FTP

1.  Inicie sesión en el Portal de Azure Stack como el Administrador de servicios o como un inquilino.
2.  Vaya a **App Services** y seleccione una aplicación web, para móviles o de API que haya creado. ![App Services][7]
3.  En los detalles de la aplicación, anote el **nombre de host de FTP** y el **nombre de usuario de FTP o la implementación**. ![Detalles de la aplicación de App Service][8]
> [!NOTE]
> Si no ve ninguna entrada en **FTP/Nombre de usuario de implementación**, debe establecer primero las credenciales de implementación credenciales utilizando la hoja **Credenciales de implementación**.

4.  Abra el Explorador de Windows, escriba el nombre de host de FTP en la barra de direcciones del archivo; por ejemplo, ftp://ftp.appservice.azurestack.local.
5.  Cuando se le solicite escribir las **credenciales de implementación** que anotó en el paso 3, si se ha habilitado la característica, verá una lista de directorios de contenido de la aplicación de App Service. ![Lista de archivos de FTP][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
