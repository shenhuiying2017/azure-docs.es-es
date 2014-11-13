<properties urlDisplayName="User Names in Linux" pageTitle="Selecci&oacute;n de nombres de usuario para Linux en Azure" metaKeywords="" description="Aprenda a seleccionar nombres de usuario para una m&aacute;quina virtual de Linux en Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Selecci&oacute;n de nombres de usuario para Linux en Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Selección de nombres de usuario para Linux en Azure

Al crear una instancia de máquina virtual de Linux en Azure, tiene la posibilidad de seleccionar el nombre de usuario para el que realizar el aprovisionamiento (el nombre de usuario predeterminado es *azureuser*). En la mayoría de casos este usuario nuevo no existirá en la imagen base y se creará durante el proceso de aprovisionamiento. En algunos casos, si el usuario ya existe en la imagen de la máquina virtual base, el agente Azure Linux sencillamente configurará la contraseña (o la tecla SSH) para ese usuario de acuerdo con la información de configuración de aprovisionamiento que se ha pasado al crear la máquina virtual.

**Sin embargo**, Linux define un conjunto de nombres de usuario que no se deben utilizar al crear usuarios nuevos. El proceso de aprovisionamiento **fallará** si intenta aprovisionar una máquina virtual de Linux con un usuario existente del sistema, que se define como un usuario con UID 0-99. Un ejemplo habitual es el usuario `root`, que tiene UID 0.

-   Consulte también: [Base estándar de Linux - Intervalos de identificadores de usuarios][Base estándar de Linux - Intervalos de identificadores de usuarios]

Las siguientes listas contienen nombres de usuario que debería evitar al realizar el aprovisionamiento de una máquina virtual de Linux. Para una mayor seguridad, se recomienda que **no utilice estos nombres de usuario** al realizar el aprovisionamiento de una máquina virtual de Linux, ya que de lo contrario es posible que el proceso falle.

## openSUSE

-   abrt
-   adm
-   audio
-   bin
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## SLES

-   audio
-   bin
-   cdrom
-   console
-   daemon
-   dialout
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   haldaemon
-   kmem
-   lp
-   lp
-   mail
-   maildrop
-   man
-   messagebus
-   modem
-   news
-   news
-   nobody
-   nogroup
-   polkituser
-   postfix
-   public
-   root
-   shadow
-   sshd
-   sys
-   test
-   trusted
-   tty
-   users
-   utmp
-   uucp
-   uuidd
-   video
-   wheel
-   www
-   wwwrun
-   xok

## CentOS

-   abrt
-   adm
-   audio
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## Ubuntu

-   adm
-   admin
-   audio
-   backup
-   bin
-   cdrom
-   crontab
-   daemon
-   dialout
-   dip
-   disk
-   fax
-   floppy
-   fuse
-   games
-   gnats
-   irc
-   kmem
-   landscape
-   libuuid
-   list
-   lp
-   mail
-   man
-   messagebus
-   mlocate
-   netdev
-   news
-   nobody
-   nogroup
-   operator
-   plugdev
-   proxy
-   root
-   sasl
-   shadow
-   src
-   ssh
-   sshd
-   staff
-   sudo
-   sync
-   sys
-   syslog
-   tape
-   tty
-   users
-   utmp
-   uucp
-   video
-   voice
-   whoopsie
-   www-data

  [Base estándar de Linux - Intervalos de identificadores de usuarios]: http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html
