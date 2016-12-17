---
title: "Definición de la configuración de la aplicación de Azure Functions | Microsoft Docs"
description: "Obtenga información sobre cómo definir la configuración de Azure Function App."
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 836c5b48c6b9a635d4255e3954b7b9c0a2bae482


---
# <a name="how-to-configure-azure-function-app-settings"></a>Configuración de Azure Function App
## <a name="settings-overview"></a>Introducción a la configuración
Puede administrar la configuración de Azure Function App haciendo clic en el vínculo **Configuración de Function App** situado en la esquina inferior izquierda del portal. La configuración de Azure Function App se aplica a todas las funciones de la aplicación.

1. Vaya a [Azure Portal](http://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. Haga clic en **Configuración de Function App** en la esquina inferior izquierda del portal. Esta acción muestra varias opciones de configuración que puede elegir. 

![Configuración de Azure Function App](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="memory-size"></a>Tamaño de memoria
Puede configurar la cantidad de memoria para asignar para las funciones en la aplicación de la función actual. 

Para configurar la memoria, mueva el control deslizante hasta la cantidad de memoria que desee. La cantidad máxima es de 128 MB.

![Configuración del tamaño de memoria de aplicación de función](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-memory-size.png)

## <a name="continuous-integration"></a>Integración continua
Integre Function App con GitHub, Visual Studio Team Services, etc.

1. Haga clic en el vínculo **Configurar integración continua**. Se abrirá un panel **Implementaciones** con opciones.
2. Haga clic en **Configuración** en el panel **Implementaciones** para mostrar un panel **Origen de implementación** con una opción: haga clic en **Elegir origen** para mostrar los orígenes disponibles. 
3. Elija cualquiera de los orígenes de implementación disponibles haciendo clic en él: Visual Studio Team Services, OneDrive, Repositorio de Git local, GitHub, Bitbucket, DropBox o Repositorio externo. 
   
    ![Configuración de CI de App Function](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. Escriba sus credenciales y la información tal como se lo soliciten los diversos orígenes de implementación. Las credenciales y la información solicitadas pueden ser ligeramente diferentes dependiendo de qué origen ha elegido. 

Una vez que configurada CI, el código conectado que inserta en el origen configurado se implementa automáticamente en esta aplicación de función.

## <a name="authenticationauthorization"></a>Autenticación y autorización
Puede requerir que se autentiquen las llamadas para las funciones que usan un desencadenador HTTP.

1. Para configurar la autenticación, haga clic en el vínculo **Configurar autenticación**.
2. Establezca el botón **Autenticación de App Service** en **Desactivada**.

![Configuración de CI de App Function](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

La mayoría de los proveedores de autenticación piden a un identificador de cliente o clave de API y un secreto; sin embargo, las opciones de la cuenta de Microsoft y Facebook también permiten definir ámbitos (credenciales de autorización específica). Active Directory tiene varias opciones de configuración rápida o avanzada que puede establecer.

Para más información sobre cómo configurar los proveedores de autenticación específicos, consulte [Autenticación y autorización en Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="cors"></a>CORS
Normalmente, por motivos de seguridad, no se permiten llamadas a los hosts (dominios) desde orígenes externos, como llamadas de Ajax en un explorador. En caso contrario, se podría enviar código malintencionado al back-end y ejecutarse en este. Después, la ruta más segura incluye en la lista negra todos los orígenes de código, salvo algunos de los suyos de confianza. Puede configurar qué orígenes aceptan llamadas desde Azure Functions mediante la configuración de uso compartido de recursos entre orígenes (CORS). CORS permite enumerar los dominios que son el origen de JavaScript que pueden llamar a funciones en Azure Function App. 

1. Para configurar CORS, haga clic en el vínculo **Configurar CORS**. 
2. Especifique los dominios que desea incluir en la lista blanca.

![Configuración de CORS de App Function](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="api-definition"></a>Definición de API
Permita que los clientes consuman las funciones desencadenadas por HTTP de forma más fácil.

1. Para configurar una API, haga clic en **Configurar metadatos de API**. 
2. Escriba la dirección URL que apunta a un archivo JSON de Swagger.

![Configuración de API de App Function](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

Para más información sobre cómo crear definiciones de API con Swagger, visite la [introducción a API Apps, ASP.NET y Swagger en Azure](../app-service-api/app-service-api-dotnet-get-started.md).

## <a name="application-settings"></a>Configuración de la aplicación
Administre variables de entorno, versiones de Framework, depuración remota, configuración de aplicaciones, cadenas de conexión, documentos predeterminados, etc. Estos valores son específicos a Function App. 

Para definir la configuración de la aplicación, haga clic en el vínculo **Configurar las opciones de la aplicación**. 

![Configurar las opciones de la aplicación](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

## <a name="in-portal-console"></a>Consola en el portal
Puede ejecutar comandos de estilo de DOS con la consola en el portal de Azure Functions. Los comandos comunes incluyen creación de archivos y directorios y navegación por los mismos, así como la ejecución de archivos y scripts por lotes. 

> [!NOTE]
> Puede cargar scripts, pero primero debe configurar un cliente FTP en la **Configuración avanzada** de Azure Functions.
> 
> 

Para abrir la consola en portal, haga clic en **Abrir consola de desarrollo**.

![Configuración del tamaño de memoria de aplicación de función](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> Trabajar en una consola con arte ASCII como esta le da un toque interesante.
> 
> 

## <a name="kudu"></a>Kudu
Kudu permite acceder a características administrativas avanzadas de una Function App.

Para abrir Kudu, haga clic en **Vaya a Kudu**. Esta acción abre una ventana del explorador completamente nueva con el administrador web de Kudu.

> [!NOTE]
> También puede iniciar **Kudu** insertando "scm" en la dirección URL de la función, como se muestra aquí: ```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

Desde la página web de Kudu, puede ver y administrar la información del sistema, la configuración de la aplicación, las variables de entorno, los encabezados HTTP, las variables de servidor, etc.

![Configurar Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="advanced-settings"></a>Configuración avanzada
Administre la instancia de Function App como cualquier otra instancia del Servicio de aplicaciones. Esta opción le da acceso a todas las configuraciones comentadas anteriormente, además de muchas otras.  

Para abrir la configuración avanzada, haga clic en el vínculo **Configuración avanzada**. 

![Configuración de App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

Para más información sobre cómo definir cada configuración de App Service, vea [Configuración de aplicaciones web en Azure App Service](../app-service-web/web-sites-configure.md).

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO3-->


