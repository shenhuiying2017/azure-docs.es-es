---
title: "Instalación del Sistema en ejecución de Azure Functions | Microsoft Docs"
description: "Instalación de la versión preliminar 2 del Sistema en tiempo de ejecución de Azure Functions"
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
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: f8ce27bf28f73818932f2ac9056d4fdd573679e8
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Instalación de la versión preliminar 2 del Sistema en tiempo de ejecución de Azure Functions

Si desea instalar la versión preliminar 2 del Sistema en tiempo de ejecución de Azure Functions, debe seguir estos pasos:

1. Asegúrese de que la máquina cumpla los requisitos mínimos.
1. Descargue el [instalador de la versión preliminar del Sistema en ejecución de Azure Functions](https://aka.ms/azafrv2).
1. Desinstale la versión preliminar 1 del Sistema en tiempo de ejecución de Azure Functions.
1. Instale la versión preliminar 2 del Sistema en tiempo de ejecución de Azure Functions.
1. Complete la configuración de la versión preliminar 2 del Sistema en tiempo de ejecución de Azure Functions.
1. Creación de la primera función en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions

## <a name="prerequisites"></a>Requisitos previos

Antes de instalar la versión preliminar del Sistema en tiempo de ejecución de Azure Functions, debe tener los siguientes recursos disponibles:

1. Un máquina donde se ejecute Microsoft Windows Server 2016 o Microsoft Windows 10 Creators Update (Professional o Enterprise Edition).
1. Una instancia de SQL Server que se ejecute dentro de la red.  La edición mínima requerida es SQL Server Express.

## <a name="uninstall-previous-version"></a>Desinstalación de la versión anterior

Si anteriormente ha instalado la versión preliminar del Sistema en tiempo de ejecución de Azure Functions, debe desinstalar antes de instalar la última versión.  Desinstale la versión preliminar del Sistema en tiempo de ejecución de Azure Functions al quitar el programa en la sección Agregar o quitar programas de Windows.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalación de la versión preliminar del Sistema en ejecución de Azure Functions

El instalador de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions le guía por la instalación de los roles de administración y de trabajo de la versión preliminar de dicho sistema.  Es posible instalar el rol de administración y el de trabajo en la misma máquina.  Sin embargo, a medida que agregue más aplicaciones de función, debe implementar más roles de trabajo en máquinas adicionales para poder escalar sus funciones a varios trabajos.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalación del rol de administración y el de trabajo en la misma máquina

1. Ejecute el instalador de la versión preliminar del Sistema en ejecución de Azure Functions.

    ![Instalador de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][1]

1. Haga clic en **Siguiente**.
1. Una vez que haya leído los términos del **CLUF**, **active la casilla** para aceptar los términos y haga clic en **Siguiente** para avanzar.
1. Seleccione los roles que desea instalar en esta máquina, **Functions Management Role** (Rol de administración de Functions) o **Functions Worker Role** (Rol de trabajo de Functions) y haga clic en **Next** (Siguiente).

    ![Instalador de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions: selección de roles][3]

    > [!NOTE]
    > Puede instalar **Functions Worker Role** (Rol de trabajo de Functions) en muchas otras máquinas. Para ello, siga estas instrucciones y seleccione únicamente **Functions Worker Role** (Rol de trabajo de Functions) en el instalador.

1. Haga clic en **Next** (Siguiente) tener que **Azure Functions Runtime Setup Wizard** (Asistente para la instalación del Sistema en tiempo de ejecución de Azure Functions) comience el proceso de instalación en la máquina.
1. Una vez completado el Asistente para la instalación, se iniciará la herramienta de configuración del **Sistema en tiempo de ejecución de Azure Functions**.

    ![Instalador de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions completado][6]

    > [!NOTE]
    > Si está instalando en **Windows 10** y la característica **Contenedor** no se ha habilitado previamente, el **programa de instalación del Sistema en tiempo de ejecución de Azure Functions** le pedirá que reinicie la máquina para completar la instalación.

## <a name="configure-the-azure-functions-runtime"></a>Configuración del Sistema en ejecución de Azure Functions

Para completar la instalación del Sistema en tiempo de ejecución de Azure Functions, debe completar la configuración.

1. La herramienta de configuración del **Sistema en tiempo de ejecución de Azure Functions** muestra qué roles están instalados en su máquina.

    ![Herramienta de configuración de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][7]

1. Haga clic en la pestaña **Base de datos**, especifique los detalles de conexión de la instancia de SQL Server, además de una [clave maestra de base de datos](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine), y haga clic en **Aplicar**.  Se requiere conectividad a una instancia de SQL Server para que el Sistema en tiempo de ejecución de Azure Functions cree una base de datos que admita el Sistema en ejecución.

    ![Configuración de la base de datos en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][8]

1. Haga clic en la pestaña **Credenciales**.  En esta pantalla debe crear dos credenciales que usará con un recurso compartido de archivos para hospedar todas las aplicaciones de función.  Especifique la información correspondiente en **User name** (Nombre de usuario) y **Password** (Contraseña) para el **propietario del recurso compartido de archivos** y para el **usuario del recurso compartido de archivos**, y haga clic en **Apply** (Aplicar).

    ![Credenciales en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][9]

1. Haga clic en la pestaña **Recurso compartido de archivos**.  En esta pantalla debe especificar los detalles de la ubicación del recurso compartido de archivos.  Se puede crear automáticamente o puede usar un recurso compartido de archivos existente; haga clic en **Aplicar**.  Si selecciona una nueva ubicación de recurso compartido de archivos, debe especificar un directorio que usará el Sistema en ejecución de Azure Functions.

    ![Recurso compartido de archivos en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][10]

1. Haga clic en la pestaña **IIS**.  En esta pestaña se muestran los detalles de los sitios web en IIS que la herramienta de configuración del Sistema en tiempo de ejecución de Azure Functions creará.  Puede especificar un nombre DNS personalizado en esta pantalla para el portal de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions.  Haga clic en **Apply** (Aplicar) para completarlo.

    ![IIS en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][11]

1. Haga clic en la pestaña **Servicios**.  En esta pestaña se muestra el estado de los servicios en la herramienta de configuración del Sistema en tiempo de ejecución de Azure Functions.  Si el **servicio de activación de host de Azure Functions** no se está ejecutando después de la configuración inicial, haga clic en **Iniciar servicio**.

    ![Finalización de la configuración de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][12]

1. Vaya al **portal del Sistema en tiempo de ejecución de Azure Functions** como `https://<machinename>.<domain>/`.

    ![Portal de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Creación de la primera función en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions

Para crear la primera función en la versión preliminar del Sistema en tiempo de ejecución de Azure Functions, siga estos pasos:

1. Por último, vaya al **portal del Sistema en tiempo de ejecución de Azure Functions** como https://<machinename>.<domain> Por ejemplo, https://miEquipo.miDominio.com
1. Se le pedirá que **inicie sesión**. Si se implementó en un dominio, use el nombre de usuario y la contraseña de la cuenta de dominio, en caso contrario, utilice el nombre de usuario y la contraseña de la cuenta local y para iniciar sesión en el portal.

![Inicio de sesión en el Portal de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][14]

1. Para crear aplicaciones de función, debe crear una suscripción.  En la esquina superior izquierda del portal, haga clic en la opción **+** junto a las suscripciones

![Suscripciones del Portal de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][15]

1. Elija **DefaultPlan**, escriba un nombre para la suscripción y haga clic en **Crear**.

![Nombre y plan de suscripción del Portal de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][16]

1. Todas las aplicaciones de función se muestran en el panel izquierdo del portal.  Para crear una aplicación de función, seleccione el encabezado **Instancias de Function App** y haga clic en la opción **+**.

1. Escriba un nombre para la aplicación de función, seleccione la suscripción correcta, elija qué versión del Sistema en tiempo de ejecución de Azure Functions desea programar y haga clic en **Crear**

![Nueva aplicación de función del Portal de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][17]

1. La nueva aplicación de función aparece en el panel izquierdo del portal.  Seleccione las funciones y, luego, haga clic en **Nueva función** en la parte superior del panel central en el portal.

![Plantillas de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][18]

1. Seleccione la función de desencadenador de temporizador. En el control flotante derecho, asigne el nombre de la función y cambie la programación a `*/5 * * * * *` (esta expresión CRON hace que la función de temporizador se ejecute cada cinco segundos). Haga clic en **Crear**.

![Configuración de la función de temporizador de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][19]

1. Ahora se ha creado la función.  Puede ver el registro de ejecución de la aplicación de función expandiendo el panel de **registro** de la parte inferior del portal.

![Ejecución de la función de la versión preliminar del Sistema en tiempo de ejecución de Azure Functions][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
