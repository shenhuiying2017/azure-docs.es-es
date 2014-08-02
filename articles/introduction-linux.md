<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Intro to Linux" pageTitle="Introduction to Linux in Azure - Azure Tutorial" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="" solutions="" manager="" editor="" />

Introducción a Linux en Azure
=============================

En este tema se ofrece información general acerca de algunos aspectos relacionados con el uso de las máquinas virtuales con Linux en la nube de Azure. La implementación de una máquina virtual con Linux es un proceso sencillo cuando se usa una imagen existente de la galería.

Tabla de contenido
------------------

-   [Autenticación: nombres de usuario, contraseńas y claves SSH](#authentication)
-   [Generación y uso de claves SSH para iniciar sesión en máquinas virtuales con Linux](#keygeneration)
-   [Obtención de privilegios de superusuario con el uso de sudo](#superuserprivileges)
-   [Configuración del firewall](#firewallconfiguration)
-   [Cambios del nombre de host](#hostnamechanges)
-   [Captura de imagen de máquina virtual](#virtualmachine)
-   [Acoplamiento de discos](#attachingdisks)

Autenticación: nombres de usuario, contraseńas y claves SSH
-----------------------------------------------------------

Al crear una máquina virtual con Linux con el Portal de administración de Azure, se le pedirá que facilite un nombre de usuario, una contraseńa y (opcionalmente) una clave pública SSH. La elección de un nombre de usuario para implementar una máquina virtual con Linux en Azure está sujeta a la siguiente restricción: no se admiten los nombres de cuentas del sistema que ya existen en la máquina virtual, como root. Si no facilita la clave pública SSH, podrá registrarse en la máquina virtual con el nombre y la contraseńa especificados. Si opta por cargar una clave pública SSH en el Portal de administración, la autenticación basada en contraseńa se deshabilitará y se le pedirá que use la clave privada SSH correspondiente para autenticarse en la máquina virtual e iniciar sesión.

Generación de claves SSH
------------------------

La versión actual del Portal de administración solo acepta claves públicas SSH que estén encapsuladas en un certificado X509. Siga los pasos a continuación para generar y utilizar claves SSH con Azure.

1.  Utilice openssl para generar un certificado X509 con un par de claves RSA de 2048 bits.

         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Responda a las preguntas que le solicite openssl (también puede dejarlas en blanco). La plataforma no utiliza el contenido de estos campos.

2.  Cambie los permisos en la clave privada para asegurarla.

         chmod 600 myPrivateKey.key

3.  Convierta myCert.pem en myCert.cer (certificado X509 con codificación DER).

         openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Cargue myCert.cer mientras se crea la máquina virtual con Linux. El proceso de aprovisionamiento instalará automáticamente la clave pública del certificado en el archivo authorized\_keys para el usuario especificado en la máquina virtual.

5.  Conéctese a la máquina virtual con Linux mediante ssh.

         ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    La primera vez que inicie sesión, se le solicitará que acepte la huella digital de la clave pública del host.

6.  Opcionalmente, puede copiar myPrivateKey.key en \~/.ssh/id\_rsa para que su cliente OpenSSH pueda recuperarla automáticamente sin usar la opción -i.

Obtención de privilegios de superusuario con el uso de sudo
-----------------------------------------------------------

La cuenta de usuario especificada durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. El Agente de Linux de Azure configura esta cuenta para elevar privilegios a root (cuenta de superusuario) con la herramienta sudo. Después de iniciar sesión con esta cuenta de usuario, podrá ejecutar comandos como root con el uso del "comando sudo". También puede obtener un shell root con **sudo -s**.

Configuración del firewall
--------------------------

Azure ofrece un filtro de paquetes de entrada que restringe la conectividad a los puertos especificados en el Portal de administración. De forma predeterminada, el único puerto permitido es SSH. Puede abrir el acceso a puertos adicionales de la máquina virtual con Linux con la incorporación de reglas al Portal de administración.

Las imágenes de Linux de la galería no habilitan el firewall de iptables en las máquinas virtuales con Linux. Si lo desea, el firewall de IPtables puede configurarse para ofrecer capacidades adicionales.

Cambios del nombre de host
--------------------------

Al implementar inicialmente una instancia de una imagen de Linux, se le pide que facilite un nombre de host para la máquina virtual. Cuando la máquina virtual está en ejecución, este nombre de host se publica en los servidores DNS de la plataforma, a fin de que varias máquinas virtuales conectadas entre sí puedan realizar búsquedas de direcciones IP con el uso de nombres de host. Si desea realizar cambios en el nombre de host después de la implementación de una máquina virtual, use el comando **hostname newname**. El Agente de Linux de Azure incluye la funcionalidad para detectar automáticamente este cambio de nombre y configurar correctamente la máquina virtual para que mantenga este cambio y, además, publica este cambio en los servidores DNS de la plataforma. Consulte el archivo Léame del Agente de Linux de Azure para consultar detalles adicionales y opciones de configuración en relación con esta funcionalidad.

Captura de imagen de máquina virtual
------------------------------------

Azure ofrece la capacidad de capturar el estado de una máquina virtual existente en una imagen que posteriormente puede usarse para implementar instancias adicionales de máquina virtual. El Agente de Linux de Azure puede usarse para la reversión de la personalización realizada durante el proceso de aprovisionamiento. Puede seguir los siguientes pasos para capturar una máquina virtual como una imagen:

1.  Ejecute **waagent -deprovision** para deshacer la personalización del aprovisionamiento. O ejecute **waagent -deprovision+user** para eliminar opcionalmente la cuenta de usuario especificada durante el aprovisionamiento y todos los datos asociados.

2.  Apague/desconecte la máquina virtual mediante las herramientas o el Portal de administración.

3.  Haga clic en Capturar en el Portal de administración o use las herramientas para capturar la máquina virtual como una imagen.

Acoplamiento de discos
----------------------

Esto se trata paso a paso en el tutorial sobre cómo [crear una máquina virtual con Linux](../virtual-machine-from-gallery/).

