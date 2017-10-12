---
title: "Instalación del Sistema en ejecución de Azure Functions | Microsoft Docs"
description: "Cómo instalar el Sistema en ejecución de Azure Functions"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Instalación de la versión preliminar del Sistema en ejecución de Azure Functions

Si desea instalar la versión preliminar del Sistema en ejecución de Azure Functions, debe seguir estos pasos:

1. Asegúrese de que la máquina cumpla los requisitos mínimos.
1. Descargue el [instalador de la versión preliminar del Sistema en ejecución de Azure Functions](https://aka.ms/azafr). 
1. Instale la versión preliminar del Sistema en ejecución de Azure Functions.
1. Complete la configuración de la versión preliminar del Sistema en ejecución de Azure Functions.

## <a name="prerequisites"></a>Requisitos previos

Antes de instalar la versión preliminar del Sistema en ejecución de Azure Functions, debe tener lo siguiente:

1. Un máquina donde se ejecute Microsoft Windows Server 2016 o Microsoft Windows 10 Creators Update (Professional o Enterprise Edition).
1. Una instancia de SQL Server que se ejecute dentro de la red.  El requisito mínimo de edición es SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalación de la versión preliminar del Sistema en ejecución de Azure Functions

El instalador de la versión preliminar del Sistema en ejecución de Azure Functions le guía por la instalación de los roles de administración y de trabajo de la versión preliminar del Sistema en ejecución de Azure Functions.  Es posible instalar el rol de administración y el de trabajo en la misma máquina.  Sin embargo, a medida que agregue más funciones, debe implementar más roles de trabajo más en máquinas adicionales para poder escalar sus funciones a varios trabajos.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalación del rol de administración y el de trabajo en la misma máquina

1. Ejecute el instalador de la versión preliminar del Sistema en ejecución de Azure Functions.

    ![Instalador de la versión preliminar del Sistema en ejecución de Azure Functions][1]

1. **Haga clic en Siguiente** para pasar la primera fase del instalador.
1. Una vez que haya leído los términos del **CLUF**, **active la casilla** para aceptar los términos y **haga clic en Siguiente** para avanzar.
1. Ahora seleccione los roles que desea instalar en esta máquina, **Functions Management Role** (Rol de administración de Functions) o **Functions Worker Role** (Rol de trabajo de Functions), y **haga clic en Siguiente**.

    ![Instalador de la versión preliminar del Sistema en ejecución de Azure Functions: selección de roles][3]

    > [!NOTE]
    > Puede instalar **Functions Worker Role** (Rol de trabajo de Functions) en muchas otras máquinas; para hacerlo, siga estas instrucciones y seleccione solo **Functions Worker Role** en el instalador.

1. **Haga clic en Siguiente** para que el **instalador del Sistema en ejecución de Azure Functions** lleve a cabo la instalación en su máquina.
1. Una vez completado el instalador, se iniciará la **herramienta de configuración del Sistema en ejecución de Azure Functions**.

    ![Instalador de la versión preliminar del Sistema en ejecución de Azure Functions completado][5]

    > [!NOTE]
    > Si está instalando en **Windows 10** y la característica **Contenedor** no se ha habilitado previamente, el instalador del **Sistema en ejecución de Azure Functions** le pide que reinicie la máquina para completar la instalación.

## <a name="configure-the-azure-functions-runtime"></a>Configuración del Sistema en ejecución de Azure Functions

Para completar la instalación del Sistema en ejecución de Azure Functions, debe completar la configuración.

1. El **herramienta de configuración del Sistema en ejecución de Azure Functions** muestra qué roles están instalados en su máquina.

    ![Herramienta de configuración de la versión preliminar del Sistema en ejecución de Azure Functions][6]

1. Haga clic en la pestaña **Base de datos**, especifique los **detalles de conexión para la instancia de SQL Server** y **haga clic en Aplicar**.  Esto es necesario para que el Sistema en ejecución de Azure Functions cree una base de datos que admita el sistema en tiempo de ejecución.
    
    ![Configuración de la base de datos en la versión preliminar del Sistema en ejecución de Azure Functions][7]

1. Haga clic en la pestaña **Credenciales**.  En esta pantalla debe crear dos credenciales que usará con un recurso compartido de archivos para hospedar todas las funciones de Azure.  **Especifique el nombre de usuario y la contraseña** para el **propietario del recurso compartido de archivos** y para el **usuario del recurso compartido de archivos**, y haga clic en **Aplicar**.

    ![Credenciales en la versión preliminar del Sistema en ejecución de Azure Functions][8]

1. Haga clic en la pestaña **Recurso compartido de archivos**.  En esta pantalla debe especificar los detalles de la **ubicación del recurso compartido de archivos**.  Se puede crear automáticamente o puede usar un recurso compartido de archivos existente; haga clic en **Aplicar**.  Si selecciona una nueva ubicación de recurso compartido de archivos, debe especificar un directorio que usará el Sistema en ejecución de Azure Functions.
    
    ![Recurso compartido de archivos en la versión preliminar del Sistema en ejecución de Azure Functions][9]

1. Haga clic en la pestaña **IIS**.  En esta pestaña se muestran los detalles de los sitios web en IIS que la instalación del Sistema en ejecución de Azure Functions creará.  **Haga clic en Aplicar** para completarlo.

    ![IIS en la versión preliminar del Sistema en ejecución de Azure Functions][10]

1. Haga clic en la pestaña **Servicios**.  En esta pestaña se muestra el estado de los servicios en la instalación del Sistema en ejecución de Azure Functions.  Si después de la configuración inicial, el **servicio de activación de host de Azure Functions** no se está ejecutando, haga clic en **Iniciar servicio**.

    ![Configuración de la versión preliminar del Sistema en ejecución de Azure Functions completada][11]

1. Por último, vaya al **portal del Sistema en ejecución de Azure Functions** como `https://<machinename>/`.

    ![Portal del Sistema en ejecución de Azure Functions (versión preliminar)][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png