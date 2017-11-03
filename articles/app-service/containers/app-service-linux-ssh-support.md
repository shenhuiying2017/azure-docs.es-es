---
title: Compatibilidad con SSH para Azure App Service en Linux | Microsoft Docs
description: Aprenda a usar SSH con Azure App Service en Linux.
keywords: "azure app service, aplicación web, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 7e6bb974565810ebb8d8e21d1c274d42d6d39e55
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Compatibilidad con SSH para Azure App Service en Linux

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) es un protocolo de red criptográfico que permite usar servicios de red de forma segura. Normalmente, se usa para iniciar sesión en un sistema de forma remota y segura desde una línea de comandos, además de para ejecutar comandos administrativos de forma remota.

App Service en Linux proporciona compatibilidad con SSH en el contenedor de la aplicación para todas las imágenes de Docker integradas que se usan para la pila en tiempo de ejecución de nuevas aplicaciones web.

![Pilas en tiempo de ejecución](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

También puede usar SSH con sus imágenes de Docker personalizadas. Para ello, incluya el servidor SSH como parte de la imagen y configúrelo como se indica en este tema.

## <a name="making-a-client-connection"></a>Establecimiento de una conexión de cliente

Para establecer una conexión de cliente SSH, se debe iniciar el sitio principal.

Pegue el punto de conexión de Administración del control de código fuente (SCM) de la aplicación web en el explorador con el siguiente formulario:

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Si no lo está ya, será necesario que se autentique con su suscripción a Azure para poder conectarse.

![Conexión SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>Compatibilidad con SSH para imágenes personalizadas de Docker

Para que una imagen personalizada de Docker admita la comunicación mediante SSH entre el contenedor y el cliente de Azure Portal, realice los siguientes pasos para su imagen de Docker.

Estos pasos se indican en el repositorio de Azure App Service, como [un ejemplo](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

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

1. Agregue una instrucción [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) al Dockerfile para copiar un archivo [sshd_config](http://man.openbsd.org/sshd_config) en el directorio */etc/ssh/*. Su archivo de configuración debería basarse en nuestro archivo sshd_config del repositorio de GitHub de Azure App Service, localizable [aquí](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > El archivo *sshd_config* debe incluir los siguientes elementos o se producirá un error de conexión: 
    > * `Ciphers` debe incluir al menos uno de los siguientes elementos: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` debe incluir al menos uno de los siguientes elementos: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Incluya el puerto 2222 en la instrucción [`EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) del Dockerfile. Aunque se conozca la contraseña raíz, no es posible acceder al puerto 2222 desde Internet. Se trata de un puerto interno exclusivo al que solo pueden acceder los contenedores que se encuentren en el puente de una red privada virtual.

    ```docker
    EXPOSE 2222 80
    ```

1. No olvide [iniciar el servicio ssh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) mediante un script de shell en el directorio */bin*.

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

Visite los siguientes vínculos para más información acerca de Web Apps for Containers. Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Uso de una imagen personalizada de Docker para Web App for Containers](quickstart-custom-docker-image.md)
* [Uso de .NET Core en Azure App Service en Linux](quickstart-dotnetcore.md)
* [Uso de Ruby en Azure App Service en Linux](quickstart-ruby.md)
* [Preguntas más frecuentes sobre Web App for Containers de Azure App Service ](app-service-linux-faq.md)