---
title: Implementación de su aplicación en Azure App Service mediante FTP/S | Microsoft Docs
description: Aprenda a implementar la aplicación en Azure App Service mediante FTP o FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implementación de la aplicación en Azure App Service mediante FTP/S

En este artículo se muestra cómo usar FTP o FTPS para implementar la aplicación web, el back-end de la aplicación móvil o la aplicación de API en [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

El punto de conexión FTP/S de la aplicación ya está activo. No se necesita ninguna configuración para habilitar la implementación de FTP/S.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Paso1: Configurar credenciales de implementación

Para acceder al servidor FTP de la aplicación, necesita en primer lugar credenciales de implementación. 

Para establecer o restablecer las credenciales de implementación, consulte [Credenciales de implementación de Azure App Service](app-service-deployment-credentials.md). Este tutorial muestra el uso de credenciales de nivel de usuario.

## <a name="step-2-get-ftp-connection-information"></a>Paso 2: Obtener la información de conexión para FTP

1. En [Azure Portal](https://portal.azure.com), abra la [página de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de la aplicación.
2. Seleccione **Información general** en el menú de navegación izquierdo y compruebe los valores de **FTP/usuario de implementación**, **Nombre del host FTP** y **Nombre del host FTPS**. 

    ![Información de conexión FTP](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Para proporcionar el contexto adecuado para el servidor FTP, el valor **FTP/usuario de implementación**, tal como aparece en Azure Portal, incluye el nombre de la aplicación.
    > Puede encontrar la misma información si selecciona **Propiedades** en el menú de navegación izquierdo. 
    >
    > Además, nunca se muestra la contraseña de la implementación. Si olvida la contraseña de la implementación, vuelva al [Paso 1](#step1) y restablézcala.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Paso 3: Implementar archivos en Azure

1. Desde el cliente de FTP (por ejemplo, [Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client)), use la información de conexión recopilada para conectarse a la aplicación.
3. Copie los archivos y la estructura de directorio correspondiente al directorio [**/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)en Azure (o el directorio **/site/wwwroot/App_Data/Jobs/** para WebJobs).
4. Vaya a la dirección URL de la aplicación para comprobar que la aplicación se está ejecutando correctamente. 

> [!NOTE] 
> A diferencia de [las implementaciones basadas en Git](app-service-deploy-local-git.md), la implementación de FTP no es compatible con las automatizaciones de implementación siguientes: 
>
> - restauraciones de dependencias (por ejemplo, automatizaciones de NuGet, NPM, PIP y Composer)
> - compilación de archivos binarios de .NET
> - generación del archivo web.config (aquí se muestra un [ejemplo de Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Genere estos archivos necesarios de forma manual en la máquina local e impleméntelos luego junto con la aplicación.
>
>

## <a name="enforce-ftps"></a>Aplicación de FTPS

Para mejorar la seguridad, permita solo FTP a través de SSL. También puede deshabilitar FTP y FTPS si no utiliza la implementación de FTP.

En la página de recursos de la aplicación de [Azure Portal](https://portal.azure.com), seleccione **Configuración de la aplicación** en el panel de navegación izquierdo.

Para deshabilitar FTP sin cifrar, seleccione **Solo FTPS**. Para deshabilitar completamente el FTP y FTPS, seleccione **Deshabilitar**. Cuando termine, haga clic en **Guardar**.

![Deshabilitación de FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>Solución de problemas de implementación de FTP

- [¿Cómo se solucionan los problemas de implementación de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [No puedo usar FTP y publicar mi código. ¿Cómo se resuelve este problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [¿Cómo me conecto a FTP en Azure App Service con el modo pasivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>¿Cómo se solucionan los problemas de implementación de FTP?

El primer paso para solucionar los problemas de implementación de FTP es aislar los de implementación de los de la aplicación en tiempo de ejecución.

Un problema de implementación suele terminar en la ausencia de archivos o en la implementación de archivos incorrectos en la aplicación. Esto se puede solucionar al investigar la implementación de FTP o seleccionar una ruta de implementación alternativa (como el control de código fuente).

Los problemas de aplicación en tiempo de ejecución suelen provocar la implementación del conjunto de archivos correctos en la aplicación, pero un comportamiento incorrecto de esta. Esto se puede solucionar si nos centramos en el comportamiento del código en tiempo de ejecución e investigamos las rutas de acceso con error concretas.

Para determinar un problema de implementación o de tiempo de ejecución, consulte [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues) (Problemas de implementación frente a los de tiempo de ejecución).

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>No puedo usar FTP y publicar mi código. ¿Cómo se resuelve este problema?
Compruebe que ha escrito las [credenciales](#step-1--set-deployment-credentials) y el nombre de host correctos. Compruebe también que los siguientes puertos FTP de la máquina no estén bloqueados por firewall:

- Puerto de conexión de control FTP: 21
- Puerto de conexión de datos FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>¿Cómo me conecto a FTP en Azure App Service con el modo pasivo?
Azure App Service permite la conexión activa y pasiva. Se recomienda el modo pasivo, ya que las máquinas de implementación suelen estar protegidas por firewall (del sistema operativo o como parte de una red particular o profesional). Consulte un [ejemplo en la documentación de WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Pasos siguientes

Para ver escenarios de implementación más avanzados, pruebe [Implementación en Azure con Git](app-service-deploy-local-git.md). La implementación basada en Git en Azure permite el control de versiones, la restauración de paquetes, MSBuild y mucho más.

## <a name="more-resources"></a>Más recursos

* [Credenciales de implementación de Azure App Service](app-service-deploy-ftp.md)
