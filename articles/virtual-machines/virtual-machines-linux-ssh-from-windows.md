---
title: "Uso de claves SSH con Windows para máquinas virtuales Linux | Microsoft Docs"
description: "Obtenga información acerca de cómo generar y utilizar claves SSH en un equipo de Windows para conectarse a una máquina virtual con Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ba75d58b6e0ce6a75173c6d38ea27e7917a054c8
ms.lasthandoff: 03/11/2017


---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Uso de SSH con Windows en Azure
> [!div class="op_single_selector"]
> * [Windows](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Cuando se conecta a máquinas virtuales (VM) Linux en Azure, debe usar la [criptografía de clave pública](https://wikipedia.org/wiki/Public-key_cryptography) para proporcionar una forma más segura de iniciar sesión en la máquina virtual Linux. Este proceso implica un intercambio de claves públicas y privadas mediante el comando de Secure Shell (SSH) para autenticarse, en lugar de un nombre de usuario y una contraseña. Las contraseñas son vulnerables a ataques por fuerza bruta, sobre todo en máquinas virtuales con acceso a Internet, como los servidores web. En este artículo se proporciona información general sobre las claves SSH y cómo generar las claves apropiadas en un equipo con Windows.

## <a name="overview-of-ssh-and-keys"></a>Información general sobre SSH y sus claves
Con las claves públicas y privadas es posible iniciar sesión de forma segura en una máquina virtual Linux:

* La **clave pública** se coloca en la máquina virtual Linux, o en cualquier otro servicio que se desee usar con una criptografía de clave pública.
* El **clave privada** es lo que se introduce en la máquina virtual Linux al iniciar sesión para verificar la identidad. Esta clave se debe proteger, por lo que no se debe compartir.

Las claves públicas y privadas se pueden usar en varias máquinas virtuales y servicios. No es necesario usar un par de claves para cada máquina virtual o servicio al que se desee acceder. Para más información, consulte el artículo de Wikipedia sobre [criptografía de clave pública](https://wikipedia.org/wiki/Public-key_cryptography).

SSH es un protocolo de conexión cifrada que permite inicios de sesión seguros sobre conexiones no seguras. Es el protocolo de conexión predeterminado de las máquinas virtuales Linux hospedadas en Azure. Aunque el propio SSH proporciona una conexión cifrada, el uso de contraseñas con conexiones SSH deja la máquina virtual vulnerable a ataques por fuerza bruta o que se puedan averiguar las contraseñas. Un método más seguro y preferido de conectarse a una máquina virtual mediante SSH es mediante estas claves públicas y privadas, a las que también se las conoce como claves SSH.

Aunque no desee usar claves de SSH, puede iniciar sesión en las máquinas virtuales Linux con una contraseña. Si la máquina virtual no está expuesta a Internet, el uso de contraseñas puede ser suficiente. Aun así, es preciso que administre las contraseñas de cada máquina virtual Linux y mantenga directivas y procedimientos seguros con respecto a las contraseñas, como la elección contraseñas con una la longitud mínima y la actualización periódica de las mismas. El uso de claves SSH reduce la complejidad de la administración de credenciales individuales en varias máquinas virtuales.

## <a name="windows-packages-and-ssh-clients"></a>Paquetes de Windows y los clientes SSH
Para conectarse a máquinas virtuales Linux de Azure y administraras, se usa un cliente **ssh**. Normalmente, los equipos de Windows no tienen un cliente ssh instalado. Los clientes SSH de Windows comunes que puede instalar se encuentran en los siguientes paquetes:

* [Git para Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

> [!NOTE]
> La última Actualización de aniversario de Windows 10 incluye Bash para Windows. Esta característica permite ejecutar el subsistema de Windows para Linux y acceder a utilidades como un cliente SSH. Bash para Windows está aún en desarrollo y se considera una versión beta. Para más información acerca de Bash para Windows, consulte [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Bash en Ubuntu en Windows).
>
>

## <a name="which-key-files-do-you-need-to-create"></a>¿Qué archivos claves necesita crear?
Azure requiere al menos claves públicas y privadas de 2048 bits con el formato **ssh-rsa**. Si va a administrar recursos de Azure mediante el modelo de implementación clásico, también debe generar un PEM (archivo `.pem`).

Estos son los escenarios de implementación y los tipos de archivos que se usan en cada uno:

1. Las claves **ssh rsa** son necesarias para cualquier implementación que se realice desde [Azure Portal](https://portal.azure.com) y las implementaciones de Resource Manager que realicen con la [CLI de Azure](../xplat-cli-install.md).
   * Normalmente estas claves son lo único que necesitan la mayoría de los usuarios.
2. El archivo `.pem` es necesario para crear máquinas virtuales mediante la implementación clásica. Estas claves se admiten en las implementaciones de clásico al usar [Azure Portal](https://portal.azure.com) o la [CLI de Azure](../xplat-cli-install.md).
   * Solo hace falta crear certificados y claves adicionales si se van a administrar recursos creados con el modelo de implementación clásico.

## <a name="install-git-for-windows"></a>Instalación de Git para Windows
En la sección anterior se enumeraban varios paquetes que incluyen la herramienta `openssl` para Windows. Dicha herramienta es necesaria para crear claves públicas y privadas. En los ejemplos siguientes se muestra cómo instalar y usar **Git para Windows**, aunque se puede elegir el paquete que se prefiera. **GIT para Windows** proporciona acceso a varias herramientas y utilidades de software de código abierto ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) adicionales que pueden resultar útiles si se trabaja con máquinas virtuales Linux.

1. Descargue e instale **Git para Windows** desde la siguiente ubicación: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).
2. En el proceso de instalación, acepte las opciones predeterminadas, salvo que necesite específicamente cambiarlas.
3. Ejecute **Git Bash** desde el **menú Inicio** > **Git** > **Git Bash**. El aspecto de la consola es similar a este:

    ![Shell de Bash de Git para Windows](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Creación de una clave privada
1. En la ventana de **Git Bash** ventana, utilice `openssl.exe` para crear una clave privada. En el ejemplo siguiente se crea una clave llamada `myPrivateKey` y un certificado llamado `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    La salida es similar a la siguiente:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Si Bash informa de un error, pruebe a abrir una nueva ventana **Git Bash** con privilegios elevados. A continuación, vuelva a ejecutar el comando `openssl`.

2. Responda a las preguntas sobre el nombre de país, la ubicación, el nombre de la organización, etc.
3. La clave privada y el certificado nuevos se crean en el directorio de trabajo actual. Como medida de seguridad, debe establecer los permisos de la clave privada de tal forma que nadie más pueda acceder a ella:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. En la [próxima sección](#create-a-private-key-for-putty) se detalla el uso de PuTTYgen para ver y usar la clave pública y se crea una clave privada específica para el uso de PuTTY para SSH en máquinas virtuales Linux. El siguiente comando genera un archivo de clave pública llamado `myPublicKey.key` que puede usar inmediatamente:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Si también tiene que administrar recursos clásicos, convierta `myCert.pem` a `myCert.cer` (certificado X509 con codificación DER). Este paso opcional solo se realiza si hay que administrar específicamente recursos clásicos anteriores.

    Para convertir el certificado, use el siguiente comando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Creación de una clave privada para PuTTY
PuTTY es un cliente SSH común para Windows. Puede utilizar cualquier cliente SSH que desee. Para usar PuTTY, es preciso crear un tipo de clave más: una clave privada PuTTY (PPK). Si no desea usar PuTTY, puede omitir esta sección.

En el ejemplo siguiente se crea una clave privada adicional específica para que la use PuTTY:

1. Use **Git Bash** para convertir su clave privada en una clave privada RSA que PuTTYgen puede entender. En el ejemplo siguiente se crea una clave llamada `myPrivateKey_rsa` a partir de la clave existente llamada `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Como medida de seguridad, debe establecer los permisos de la clave privada de tal forma que nadie más pueda acceder a ella:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Descargue y ejecute PuTTYgen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Haga clic en el menú: **File** > **Load a Private Key** (Archivo > Cargar clave privada).
4. Busque la clave privada (`myPrivateKey_rsa` en el ejemplo anterior). El directorio predeterminado al iniciar **Git Bash** es `C:\Users\%username%`. Deberá cambiar el filtro de archivos para mostrar **Todos los archivos (\*:\*)**:

    ![Cargar la clave privada existente en PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)
5. Haga clic en **Abrir**. Un aviso indica que la clave se ha importado correctamente:

    ![Clave importada correctamente en PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)
6. Haga clic en **Aceptar** para cerrar el aviso.
7. La clave pública se muestra en la parte superior de la ventana de **PuTTYGen**. Cuando cree una máquina virtual Linux, copie y pegue esta clave pública en Azure Portal o en una plantilla de Azure Resource Manager. También puede hacer clic en **Save public key** (Guardar clave pública) para guardar una copia en el equipo:

    ![Guardar archivo de clave pública de PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    En el siguiente ejemplo se muestra cómo se copia y pega esta clave pública en Azure Portal al crear una máquina virtual Linux. La clave pública se suele almacenar en `~/.ssh/authorized_keys` en la máquina virtual nueva.

    ![Usar la clave pública al crear una máquina virtual en Azure Portal](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)
8. En **PuTTYgen**, haga clic en **Save private Key** (Guardar clave privada):

    ![Guarde el archivo de clave privada de PuTTY](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > Se le pregunta si desea continuar sin escribir una frase de contraseña para la clave. Una frase de contraseña es como una contraseña que se adjunta a la clave privada. Aunque alguien obtuviera su clave privada, no podría autenticarse solo con la clave, ya que también se necesita la frase de contraseña. Sin una frase de contraseña, si alguien obtiene su clave privada, podrá iniciar sesión en cualquier máquina virtual o servicio que utilice dicha clave. Por consiguiente, se recomienda crear una frase de contraseña. Sin embargo, si se le olvida la frase de contraseña, no hay forma de recuperarla.
   >
   >

    Si desea especificar una frase de contraseña, haga clic en **No**, escriba una frase de contraseña en la ventana principal de PuTTYgen y vuelva a hacer clic en **Save private key** (Guardar clave privada). Si no desea hacerlo, haga clic en **Yes** (Sí) para continuar sin especificar la frase de contraseña opcional.
9. Escriba el nombre y la ubicación en que desea guardar el archivo PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Uso de Putty para usar un cliente SSH en una máquina virtual Linux
PuTTY es un cliente SSH común para Windows. Puede utilizar cualquier cliente SSH que desee. En los siguientes pasos se explica cómo usar una clave privada para autenticarse en una máquina virtual de Azure mediante SSH. Dichos pasos son similares en otros clientes con claves SSH, en lo que se refiere a la necesidad de cargar una clave privada para autenticar la conexión SSH.

1. Descargue y ejecute putty desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Rellene el nombre de host o la dirección IP de la máquina virtual en Azure Portal:

    ![Abrir conexión nueva de PuTTY](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)
3. Antes de seleccionar **Open** (Abrir), haga clic en la pestaña **Connection** (Conexión) > **SSH** > **Auth** (Autorización). Busque y seleccione la clave privada:

    ![Seleccionar la clave privada PuTTY para la autenticación](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)
4. Haga clic en **Abrir** para conectarse a su máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
Las claves públicas y privadas también se pueden generar [con OS X y Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para más información acerca de Bash para Windows y las ventajas de contar con herramientas OSS disponibles en el equipo de Windows, consulte [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Bash en Ubuntu en Windows).

Si tiene problemas con el uso de SSH para conectarse a máquinas virtuales Linux, consulte [Solución de problemas de conexiones SSH a una máquina virtual Linux de Azure](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

