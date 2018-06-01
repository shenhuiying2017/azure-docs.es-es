---
title: Compatibilidad con SSH para Azure App Service en Linux | Microsoft Docs
description: Aprenda a usar SSH con Azure App Service en Linux.
keywords: azure app service, aplicación web, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301082"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Compatibilidad con SSH para Azure App Service en Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) se suele utilizar para ejecutar comandos administrativos de forma remota desde un terminal de línea de comandos. App Service en Linux proporciona compatibilidad con SSH en el contenedor de la aplicación para todas las imágenes de Docker integradas que se usan para la pila en tiempo de ejecución de nuevas aplicaciones web. 

![Pilas en tiempo de ejecución](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Para imágenes personalizadas de Docker, mediante la configuración del servidor SSH de la imagen personalizada.

También puede conectarse al contenedor directamente desde la máquina de desarrollo local mediante SSH y SFTP.

## <a name="open-ssh-session-in-browser"></a>Abrir sesión SSH en el explorador

Para realizar una conexión de cliente SSH con el contenedor, la aplicación debe estar en ejecución.

Pegue la siguiente dirección URL en el explorador y reemplace \<app_name> por el nombre de la aplicación:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Si no lo está ya, será necesario que se autentique con su suscripción a Azure para poder conectarse. Una vez autenticado, verá un shell del explorador en el que puede ejecutar comandos dentro del contenedor.

![Conexión SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Uso de la compatibilidad con SSH para imágenes personalizadas de Docker

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

1. No olvide iniciar el servicio SSH mediante un script de shell (vea el ejemplo en [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

El Dockerfile usa la instrucción [`ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint) para ejecutar el script.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Abrir sesión SSH desde un shell remoto

> [!NOTE]
> Esta característica está actualmente en versión preliminar.
>

Mediante la tunelización TCP puede crear una conexión de red entre la máquina de desarrollo y Web App for Containers a través de una conexión de WebSocket autenticada. Permite abrir una sesión SSH con el contenedor que se ejecuta en App Service desde el cliente de su elección.

Para empezar, es preciso instalar la [CLI de Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Para ver cómo funciona sin instalar la CLI de Azure, abra [Azure Cloud Shell](../../cloud-shell/overview.md). 

Agregue la extensión de App Service más reciente ejecutando [az extension add](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add -–name webapp
```

Si ya ha ejecutado `az extension add` antes, ejecute [az extension update](/cli/azure/extension?view=azure-cli-latest#az-extension-update) en su lugar:

```azurecli-interactive
az extension update -–name webapp
```

Abra una conexión remota a la aplicación mediante el comando [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Especifique _\<group\_name>_ y \_<app\_name>_ para la aplicación y sustituya \<port> por un número de puerto local.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> El `&` al final del comando es solo para su comodidad si usa Cloud Shell. El proceso se ejecuta en segundo plano, por lo que puede ejecutar el siguiente comando en el mismo shell.

La salida del comando le ofrece la información necesaria para abrir una sesión SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra una sesión SSH con el contenedor con el cliente de su elección mediante el puerto local. En el ejemplo siguiente se utiliza el comando [ssh](https://ss64.com/bash/ssh.html) predeterminado:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Cuando se solicite, escriba `yes` para continuar con la conexión. Se le pedirá la contraseña. Use `Docker!`, que ya apareció anteriormente.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Una vez autenticado, debería ver la pantalla de inicio de sesión.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Ahora está conectado a su conector. 

Intente ejecutar el comando[top](https://ss64.com/bash/top.html). Debe poder ver el proceso de la aplicación en la lista de procesos. En la salida del ejemplo siguiente, es el marcado con `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Pasos siguientes

Puede publicar preguntas y problemas en el [foro de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Para obtener más información sobre Web App for Containers, vea:

* [Introducing remote debugging of Node.js apps on Azure App Service from VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) (Introducción a la depuración remota de aplicaciones de Node.js en Azure App Service desde VS Code)
* [Uso de una imagen personalizada de Docker para Web App for Containers](quickstart-docker-go.md)
* [Uso de .NET Core en Azure App Service en Linux](quickstart-dotnetcore.md)
* [Uso de Ruby en Azure App Service en Linux](quickstart-ruby.md)
* [Preguntas más frecuentes sobre Web App for Containers de Azure App Service ](app-service-linux-faq.md)