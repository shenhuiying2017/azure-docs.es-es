---
title: Antes de implementar App Service en Azure Stack | Microsoft Docs
description: Pasos necesarios para implementar App Service en Azure Stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3cba11acc6279f24d0a47af8978610180724c0a2
ms.contentlocale: es-es
ms.lasthandoff: 09/15/2017

---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de empezar a trabajar con App Service en Azure Stack

Se necesitan algunos elementos para instalar Azure App Service en Azure Stack:

- Una implementación completa de [Azure Stack Development Kit](azure-stack-run-powershell-script.md).
- Suficiente espacio en el sistema de Azure Stack para una implementación pequeña de App Service en Azure Stack.  El espacio necesario es de aproximadamente 20 GB de espacio en disco.
- Una imagen de máquina virtual de Windows Server para usarla al crear máquinas virtuales para App Service en Azure Stack.
- [Un servidor que ejecute SQL Server](#SQL-Server).

>[!NOTE] 
> *Todos* los pasos siguientes se realizan en el equipo host de Azure Stack.

Para implementar un proveedor de recursos, debe ejecutar el Entorno de scripting integrado (ISE) de PowerShell como administrador. Por este motivo, se deben permitir las cookies y JavaScript en el perfil de Internet Explorer que se usa para iniciar sesión en Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Desactivación de la seguridad mejorada de Internet Explorer

1.  Inicie sesión en la máquina de Azure Stack Development Kit como **AzureStack/administrator** y, a continuación, abra el **Administrador del servidor**.

2.  Desactive la **Configuración de seguridad mejorada de Internet Explorer** para los administradores y usuarios.

3.  Inicie sesión en la máquina de Azure Stack Development Kit como administrador y, a continuación, abra el **Administrador del servidor**.

4.  Desactive la **Configuración de seguridad mejorada de Internet Explorer** para los administradores y usuarios.

## <a name="enable-cookies"></a>Habilitación de las cookies

1.  Seleccione **Inicio** > **Todas las aplicaciones** > **Accesorios de Windows**. Haga clic con el botón derecho en **Internet Explorer** > **Más** > **Ejecutar como administrador**.

2.  Si se le pide, seleccione **Use recommended security** (Usar seguridad recomendada) y, a continuación, seleccione **Aceptar**.

3.  En Internet Explorer, seleccione **Herramientas** (icono de engranaje) > **Opciones de Internet** > **Privacidad** > **Avanzada**.

4.  Seleccione **Advanced** (Avanzadas). Asegúrese de que las dos casillas **Aceptar** estén activadas. Seleccione **Aceptar** dos veces.

5.  Cierre Internet Explorer y reinicie el ISE de PowerShell como administrador.

## <a name="install-powershell-for-azure-stack"></a>Instalación de PowerShell para Azure Stack

Para instalar PowerShell para Azure Stack, siga los pasos de [instalación de PowerShell](azure-stack-powershell-install.md).

## <a name="use-visual-studio-with-azure-stack"></a>Uso de Visual Studio con Azure Stack

Para usar Visual Studio con Azure Stack, siga los pasos de [instalación de Visual Studio](azure-stack-install-visual-studio.md).

## <a name="add-a-windows-server-2016-vm-image-to-azure-stack"></a>Adición de una imagen de máquina virtual de Windows Server 2016 a Azure Stack

Puesto que App Service implementa varias máquinas virtuales, requiere una imagen de máquina virtual de Windows Server 2016 en Azure Stack. Para instalar una imagen de máquina virtual, siga los pasos para [agregar una imagen de máquina virtual predeterminada](azure-stack-add-default-image.md).

## <a name="SQL-Server"></a>SQL Server

App Service en Azure Stack requiere acceso a una instancia de SQL Server para crear y hospedar dos bases de datos a fin de ejecutar el proveedor de recursos de App Service.  Si decide implementar una máquina virtual de SQL Server en Azure Stack, debe tener el nivel de conectividad SQL establecido en **Público**.  Puede elegir la instancia de SQL Server que se utilizará al completar las opciones en App Service en el instalador de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

- [Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md).

<!--Image references-->
[1]: ./media/azure-stack-app-service-before-you-get-started/PSGallery.png
[2]: ./media/azure-stack-app-service-before-you-get-started/WebPI_InstalledProducts.png

