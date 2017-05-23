---
title: Compatibilidad con SSH para Web App on Linux de Azure App Service | Microsoft Docs
description: Aprenda a usar SSH con Web App on Linux de Azure.
keywords: "azure app service, aplicación web, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c1fdd9835992559c985426855a45c09849d54af2
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="ssh-support-for-azure-web-app-on-linux"></a>Compatibilidad con SSH para Web App on Linux de Azure

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Información general

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) es un protocolo de red criptográfico que permite usar servicios de red de forma segura. Normalmente, se usa para iniciar sesión en un sistema de forma remota y segura desde una línea de comandos, además de para ejecutar comandos administrativos de forma remota.

Web App on Linux proporciona compatibilidad con SSH para todas las imágenes de Docker integradas que se usan para la pila en tiempo de ejecución de nuevas aplicaciones web. 

![Pilas en tiempo de ejecución](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

También puede usar SSH con sus imágenes de Docker personalizadas. Para ello, incluya el servidor SSH como parte de la imagen y configúrelo como se indica en este tema.



## <a name="making-a-client-connection"></a>Establecimiento de una conexión de cliente

Para establecer una conexión de cliente SSH, se debe iniciar el sitio principal. 

Pegue el punto de conexión de Administración del control de código fuente (SCM) de la aplicación web en el explorador con el siguiente formulario:

        https://<your sitename>.scm.azurewebsites.net/webssh/host

Si no lo está ya, será necesario que se autentique con su suscripción a Azure para poder conectarse.

![Conexión SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)


## <a name="ssh-support-with-custom-docker-images"></a>Compatibilidad con SSH para imágenes personalizadas de Docker

Para que una imagen personalizada de Docker admita la comunicación mediante SSH entre el contenedor y el cliente de Azure Portal, realice los siguientes pasos para su imagen de Docker. 

Estos pasos se indican en el repositorio de Azure App Service, como en [este](https://github.com/Azure-App-Service/node/tree/master/4.4.7-1) ejemplo.

1. Incluya la instalación `openssh-server` en la instrucción [`RUN`](https://docs.docker.com/engine/reference/builder/#run) del Dockerfile de la imagen y establezca la contraseña de la cuenta raíz a `"Docker!"`. 

    > [!NOTE] 
    > Esta configuración no permite realizar conexiones externas al contenedor. Solo es posible acceder a SSH a través del sitio de Kudu o SCM, que se autentica con las credenciales de publicación.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \ 
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "root:Docker!" | chpasswd
    ``` 

2. Agregue una instrucción [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) al Dockerfile para copiar un archivo [sshd_config](http://man.openbsd.org/sshd_config) en el directorio */etc/ssh/*. Su archivo de configuración debería basarse en nuestro archivo sshd_config del repositorio de GitHub de Azure App Service, localizable [aquí](https://github.com/Azure-App-Service/node/blob/master/6.9.3-1/sshd_config).

    > [!NOTE] 
    > El archivo *sshd_config* debe incluir los siguientes elementos o se producirá un error de conexión: 
    > * `Ciphers` debe incluir al menos uno de los siguientes elementos: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` debe incluir al menos uno de los siguientes elementos: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```


3. Incluya el puerto 2222 en la instrucción [`EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) del Dockerfile. Aunque se conozca la contraseña raíz, no es posible acceder al puerto 2222 desde Internet. Se trata de un puerto interno exclusivo al que solo pueden acceder los contenedores que se encuentren en el puente de una red privada virtual.

    ```docker
    EXPOSE 2222 80
    ```

4. Asegúrese de iniciar el servicio SSH. En [este](https://github.com/Azure-App-Service/node/blob/master/6.9.3-1/init_container.sh) ejemplo, se usa un script de shell en el directorio */bin*.

    ```bash
    #!/bin/bash
    service ssh start
    ```

    El Dockerfile usa la instrucción [`CMD`](https://docs.docker.com/engine/reference/builder/#cmd) para ejecutar el script.

    ```docker
    COPY init_container.sh /bin/
      ...
    RUN chmod 755 /bin/init_container.sh 
      ...        
    CMD ["/bin/init_container.sh"]
    ```



## <a name="next-steps"></a>Pasos siguientes
Visite los siguientes vínculos para obtener más información acerca de Web App on Linux. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creación de aplicaciones en Web App on Linux de Azure](app-service-linux-how-to-create-web-app.md)
* [Uso de una imagen personalizada de Docker para Web App on Linux de Azure](app-service-linux-using-custom-docker-image.md)
* [Uso de la configuración de PM2 para Node.js en Web App on Linux de Azure](app-service-linux-using-nodejs-pm2.md)
* [Uso de .NET Core en Web App on Linux de Azure](app-service-linux-using-dotnetcore.md)
* [Uso de Ruby en Web App on Linux de Azure](app-service-linux-ruby-get-started.md)
* [Preguntas más frecuentes sobre Web App on Linux de Azure App Service](app-service-linux-faq.md)


