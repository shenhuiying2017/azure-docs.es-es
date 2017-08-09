---
title: "Herramientas de Azure Data Lake: Ejecución y depuración locales de U-SQL con Visual Studio Code | Microsoft Docs"
description: "Obtenga información sobre cómo usar Herramientas de Azure Data Lake para Visual Studio Code para la ejecución y depuración locales."
Keywords: "VScode, Herramientas de Azure Data Lake, ejecución local, depuración local, archivo de almacenamiento de versión preliminar, carga en la ruta de acceso de almacenamiento"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Ejecución y depuración locales de U-SQL con Visual Studio Code

## <a name="prerequisites"></a>Requisitos previos
- Herramientas de Azure Data Lake para Visual Studio Code. Para obtener instrucciones, vea [Uso de Herramientas de Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# para Visual Studio Code (si desea realizar la depuración local de U-SQL).

   ![VscodeCsharp de instalación de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > Las características de ejecución y depuración locales de U-SQL solo admiten actualmente usuarios de Windows. 


## <a name="set-up-u-sql-local-run-environment"></a>Configuración del entorno de ejecución local de U-SQL

1. Abra la paleta de comandos pulsando **CTRL+MAYÚS+P**, y especifique **ADL: Download LocalRun Dependency** (ADL: descarga de dependencia LocalRun) para descargar los paquetes.  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Busque los paquetes de dependencia de la ruta de acceso mostrada en el panel de salida siguiente y, a continuación, instale BuildTools y Win10SDK 10240. Por ejemplo:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- Instalación de BuildTools: siga las instrucciones del asistente para realizar la instalación.   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Instalación de Win10SDK 10240: siga las instrucciones de instalación para realizar la instalación.  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. Variable de entorno de configuración; establezca la variable de entorno **SCOPE_CPP_SDK** en:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
Asegúrese de reiniciar el SO para que la configuración de la variable de entorno surta efecto.  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>Inicio de un servicio de ejecución local y envío de un trabajo de U-SQL para la cuenta local 
Para el nuevo usuario, se le solicitarán los paquetes de **ADL: Download Localrun Dependency** (ADL: descarga de dependencia Localrun) si no se ha hecho aún.
1. Pulse **CTRL+MAYÚS+P** para abrir la paleta de comandos y especifique **ADL: Start Local Run Service** (ADL: inicio de servicio de ejecución local).
2. Acepte el CLUF para hacer uso por primera vez. 

   ![Aceptación del CLUF ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Aparecerá la consola cmd. Para los usuarios nuevos, tiene que especificar 3 e insertar una ruta de acceso a la carpeta local para la entrada y salida de datos. Para otras opciones, puede usar simplemente el valor predeterminado. 

   ![Cmd de ejecución local de Data Lake Tools para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Pulse CTRL+MAYÚS+P para abrir la paleta de comandos y especifique **ADL: Submit Job** (ADL: envío de trabajo) y seleccione **Local** para enviar el trabajo a la cuenta local.

   ![Selección local de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Después del envío del trabajo, puede ver los detalles del envío haciendo clic en jobUrl en la ventana de salida. También puede ver el estado del envío del trabajo desde la consola CMD y especificar 7 en la consola CMD si desea conocer más detalles del trabajo.

   ![Salida d ejecución local de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Herramientas de Data Lake para el estado de cmd de ejecución local de Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>Inicio de la depuración local para el trabajo de U-SQL  
Para el nuevo usuario, se le solicitarán los paquetes de **ADL: Download Localrun Dependency** (ADL: descarga de dependencia Localrun) si no se ha hecho aún.
  
1. Pulse **CTRL+MAYÚS+P** para abrir la paleta de comandos y especifique **ADL: Start Local Run Service** (ADL: inicio de servicio de ejecución local). Aparecerá la ventana cmd. Asegúrese de que se establece **DataRoot**.
3. Establezca un punto de interrupción en el código de C# subyacente.
4. Vuelva al editor de script, pulse **CTRL+MAYÚS+P** para abrir la paleta de comandos y especifique **Depuración local** para iniciar el servicio de depuración local.

![Resultado de la depuración local de Herramientas de Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Pasos siguientes
- Para usar Herramientas de Azure Data Lake para Visual Studio, consulte [Use the Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md) (Uso de Herramientas de Azure Data Lake para Visual Studio Code).
- Para obtener información detallada de Data Lake Analytics, consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-get-started-portal.md).
- Para información sobre el uso de Data Lake Tools para Visual Studio, consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener información sobre el desarrollo de ensamblados, consulte [Desarrollo de ensamblados U-SQL para trabajos de Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).
