<properties linkid="article" urlDisplayName="Use SSH" pageTitle="Use SSH to connect to Linux virtual machines in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Learn how to generate and use SSH keys with a Linux virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="" solutions="" manager="" editor="" />


Utilización de SSH con Linux en Azure
=====================================

En este tema se describen los pasos necesarios para generar claves SSH compatibles con Azure.

La versión actual del Portal de administración de Azure solo acepta claves públicas SSH que estén encapsuladas en un certificado X509. Siga los pasos a continuación para generar y utilizar claves SSH con Azure.

Generación de claves compatibles con Azure en Linux
---------------------------------------------------

1.  Instale la utilidad openssl si procede:

    **CentOS / Oracle Linux**

         `sudo yum install openssl`

    **Ubuntu**

         `sudo apt-get install openssl`

    **SLES y openSUSE**

         `sudo zypper install openssl`

2.  Utilice openssl para generar un certificado X509 con un par de claves RSA de 2048 bits. Responda las preguntas que le solicite openssl (también puede dejarlas en blanco). La plataforma no utiliza el contenido de estos campos:

             openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Cambie los permisos en la clave privada para asegurarla.

            chmod 600 myPrivateKey.key

4.  Cargue myCert.pem mientras se crea la máquina virtual con Linux. El proceso de aprovisionamiento instalará automáticamente la clave pública del certificado en el archivo authorized\_keys para el usuario especificado en la máquina virtual.

5.  Si va a utilizar la API directamente y no utiliza el Portal de administración, convierta myCert.pem en myCert.cer (certificado X509 con codificación DER) mediante el siguiente comando:

            openssl  x509 -outform der -in myCert.pem -out myCert.cer

Generación de una clave a partir de una clave compatible con OpenSSH existente
------------------------------------------------------------------------------

El ejemplo anterior describe la forma de crear una nueva clave para el uso con Azure. En algunos casos, es posible que los usuarios ya tengan un par de claves pública y privada compatibles con OpenSSH y deseen utilizar las mismas claves con Azure.

La utilidad openssl puede leer directamente las claves privadas OpenSSH. El siguiente comando empleará una clave privada SSH existente (id\_rsa en el siguiente ejemplo) para crear la clave pública .pem que es necesaria para Azure:

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

El archivo **myCert.pem** es la clave pública que, a partir de entonces, se puede utilizar para aprovisionar Azure con una máquina virtual con Linux. Durante el aprovisionamiento, el archivo .pem se convertirá en una clave pública compatible con openssh y se ubicará en \~/.ssh/authorized\_keys.

Conexión a una Maquina virtual Azure desde Linux
------------------------------------------------

Todas las máquinas virtuales con Linux están aprovisionadas con SSH en un puerto en particular, que puede ser diferente del puerto estándar utilizado.

1.  Busque el puerto que desea utilizar para conectarse a la máquina virtual con Linux desde el Portal de administración.
2.  Conéctese a la máquina virtual con Linux mediante ssh. La primera vez que inicie sesión, se le solicitará que acepte la huella digital de la clave pública del host.

        ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  Opcionalmente, puede copiar myPrivateKey.key a \~/.ssh/id\_rsa para que su cliente OpenSSH pueda recuperarla automáticamente sin usar la opción -i.

Obtención de OpenSSL para Windows
---------------------------------

### Uso de msysgit

1.  Descargue e instale msysgit desde la siguiente ubicación: <http://msysgit.github.com/>
2.  Ejecute msys desde el directorio instalado (por ejemplo: c:\\msysgit\\msys.exe).
3.  Cambie al directorio bin escribiendo cd bin.

### Uso de GitHub para Windows

1.  Descargue e instale GitHub para Windows desde la siguiente ubicación: <http://windows.github.com/>
2.  Ejecute Git Shell desde la ruta: menú Inicio \> Todos los programas \> GitHub, Inc.

### Uso de Cygwin

1.  Descargue e instale Cygwin desde la siguiente ubicación: <http://cygwin.com/>
2.  Asegúrese de que el paquete OpenSSL y todas sus dependencias estén instalados.
3.  Ejecute cygwin.

Creación de una clave privada en Windows
----------------------------------------

1.  Siga uno de los conjuntos de instrucciones anteriores para poder ejecutar openssl.exe.
2.  Escriba el siguiente comando:

        openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  La pantalla debería mostrar lo siguiente:

    ![linuxwelcomegit](./media/linux-use-ssh-key/linuxwelcomegit.png)

4.  Responda las preguntas.
5.  Se habrán creado dos archivos: myPrivateKey.key y myCert.pem.
6.  Si va a utilizar la API directamente y no utiliza el Portal de administración, convierta myCert.pem en myCert.cer (certificado X509 con codificación DER) mediante el siguiente comando:

        openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

Creación de un PPK para Putty
-----------------------------

1.  Descargue e instale Puttygen desde la siguiente ubicación: <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>
2.  Ejecute puttygen.exe.
3.  Haga clic en el menú: File \> Load a Private Key.
4.  Busque su clave privada, a la que hemos denominado myPrivateKey.key. Deberá cambiar el filtro de archivos para mostrar **All Files (\*.\*)**.
5.  Haga clic en **Open**. Recibirá una secuencia parecida a la siguiente:

    ![linuxgoodforeignkey](./media/linux-use-ssh-key/linuxgoodforeignkey.png)

6.  Haga clic en **OK**.
7.  Haga clic en **Save Private Key**, que aparece resaltado en la siguiente captura de pantalla:

    ![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtygenprivatekey.png)

8.  Guarde el archivo como PPK.

Uso de Putty para conectarse a una máquina virtual con Linux
------------------------------------------------------------

1.  Descargue e instale Putty desde la siguiente ubicación: <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>
2.  Ejecute putty.exe.
3.  Rellene el nombre del host con la IP desde el Portal de administración.

    ![linuxputtyconfig](./media/linux-use-ssh-key/linuxputtyconfig.png)

4.  Antes de seleccionar **Open**, haga clic en Connection \> SSH \> Auth para elegir su clave. Consulte la siguiente captura de pantalla con el campo que se va a rellenar.

    ![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtyprivatekey.png)

5.  Haga clic en **Open** para conectarse a su máquina virtual.

