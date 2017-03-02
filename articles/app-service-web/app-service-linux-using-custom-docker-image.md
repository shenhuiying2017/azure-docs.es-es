---
title: Uso de una imagen de Docker personalizada para Azure App Service en Linux | Microsoft Docs
description: Uso de una imagen de Docker personalizada para App Service en Linux.
keywords: "azure app service, aplicación web, linux, docker, contenedor"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 7e4aab65feac187b48ccca65b35bb94185323506
ms.lasthandoff: 02/17/2017


---

# <a name="using-a-custom-docker-image-for-app-service-on-linux"></a>Uso de una imagen de Docker personalizada para App Service en Linux #

App Service proporciona pilas de aplicaciones predefinidas en Linux con compatibilidad para versiones específicas, como PHP 7.0 y Node.js 4.5. App Service en Linux utiliza contenedores de Docker para hospedar estas pilas de aplicaciones incorporadas. También puede usar una imagen personalizada de Docker para implementar la aplicación web a una pila de aplicaciones aún sin definir en Azure. Las imágenes de Docker personalizadas se pueden hospedar en un repositorio de Docker público o privado.


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Establecimiento de una imagen de Docker personalizada para una aplicación web
Puede establecer la imagen de Docker personalizada para aplicaciones web nuevas y existentes. Cuando cree una nueva aplicación web en Linux en [Azure Portal](https://portal.azure.com), haga clic en **Configurar contenedor** para establecer una imagen de Docker personalizada:

![Imagen de Docker personalizada para una nueva aplicación web en Linux][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Uso de una imagen personalizada de Docker de Docker Hub ##
Para usar una imagen personalizada de Docker de Docker Hub:

1. En [Azure Portal](https://portal.azure.com), busque la aplicación web en Linux y, en **Configuración**, haga clic en **Contenedor de Docker**.

2.  Seleccione **Docker Hub** como **Origen de imagen**, haga clic en **Pública** o **Privada** y escriba nombre en **Imagen y etiqueta opcional (por ejemplo, 'image:tag')**, como `node:4.5`. El **Comando de inicio** se establece automáticamente basándose en lo que se definió en el archivo de imagen de Docker, pero puede establecer sus propios comandos.  

    ![Configuración de la imagen del repositorio público de Docker Hub][2]

    Cuando la imagen pertenece a un repositorio privado, también tendrá que especificar las credenciales de Docker Hub (**nombre de usuario de inicio de sesión** y **contraseña**) para el repositorio de Docker Hub privado.

    ![Configuración de la imagen del repositorio privado de Docker Hub][3]

3. Después de haber configurado el contenedor, haga clic en **Guardar**.

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>Uso de una imagen de Docker de un registro de imágenes privado ##
Para usar una imagen de Docker de un registro de imágenes privado:

1. En [Azure Portal](https://portal.azure.com), busque la aplicación web en Linux y, en **Configuración**, haga clic en **Contenedor de Docker**.

2.  Haga clic en **Registro privado** como **Origen de imagen**. Especifique valores para el **nombre de la imagen y la etiqueta opcional** y la **dirección URL del servidor** para el registro privado, junto con las credenciales (**nombre de usuario de inicio de sesión** y **contraseña**). Haga clic en **Guardar**.

    ![Configuración de la imagen de Docker del registro privado][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Establecimiento del puerto que usa la imagen de Docker ##

Cuando use una imagen personalizada de Docker para la aplicación web, puede usar la variable de entorno `PORT` en el Dockerfile, que se agrega al contenedor generado. Tenga en cuenta el siguiente ejemplo de archivo de Docker para una aplicación Ruby:

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p $PORT -e production

En la última línea del comando, puede ver que la variable de entorno de PORT se pasa en tiempo de ejecución. Recuerde que en los comandos se distingue entre mayúsculas y minúsculas.

Al usar una imagen de Docker existente creada por otra persona, para la aplicación debe especificar un puerto distinto al 80. Para configurar el puerto, agregue una configuración de la aplicación llamada `PORT` con el valor tal y como se muestra a continuación:

![Configuración de aplicación PORT para una imagen personalizada de Docker][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>Cambio para usar una imagen incorporada ##

Para cambiar de una imagen personalizada a una incorporada:

1. En [Azure Portal](https://portal.azure.com), busque la aplicación web en Linux y, en **Configuración**, haga clic en **App Service**.

2. Seleccione la **Pila en tiempo de ejecución** para utilizar para la imagen incorporada y haga clic en **Guardar**. 

![Configuración de una imagen de Docker incorporada][5]


## <a name="troubleshooting"></a>Solución de problemas ##

Cuando la aplicación no se inicia con la imagen de Docker personalizada, compruebe que el Docker registra en el directorio LogFiles/docker. A este directorio se accede a través del sitio SCM o a través de FTP. 

![Uso de Kudu para ver registros de Docker][7]

Puede acceder al sitio SCM desde **Advanced Tools** (Herramientas avanzadas) en el menú **Herramientas de desarrollo**.

## <a name="next-steps"></a>Pasos siguientes ##

Siga los vínculos a continuación para empezar a trabajar con App Service en Linux.   

* [Introducción a App Service en Linux](./app-service-linux-intro.md)
* [Creación de aplicaciones web en App Service en Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Using PM2 Configuration for Node.js in Web Apps on Linux](./app-service-linux-using-nodejs-pm2.md) (Uso de la configuración de PM2 para Node.js en Web Apps en Linux)
* [Preguntas más frecuentes sobre Azure App Service Web Apps en Linux](app-service-linux-faq.md)

Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png

