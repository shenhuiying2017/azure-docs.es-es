---
title: "Configuración de SSHD en Azure Linux Virtual Machines | Microsoft Docs"
description: Configure SSHD para cumplir con los procedimientos recomendados de seguridad y bloquear SSH en Azure Linux Virtual Machines.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 08499c4242fdc59ef932d6b8f2e8442e5cdc55b2
ms.openlocfilehash: a606f06de55b1db4392d066a3e1d026eb3b54d74


---

# <a name="configure-sshd-on-azure-linux-vms"></a>Configuración de SSHD en Azure Linux Virtual Machines

Este artículo muestra cómo bloquear el servidor SSH en Linux, para cumplir con los procedimientos recomendados de seguridad y también para acelerar el proceso de inicio de sesión de SSH mediante claves SSH en lugar de contraseñas.  Para bloquear aún más SSHD vamos a impedir que el usuario raíz pueda iniciar sesión, a limitar los usuarios que tienen permiso para iniciar sesión a través de una lista de grupos aprobados, a deshabilitar la versión del protocolo SSH 1, a establecer un bit de clave mínima y a configurar el cierre de sesión automático de los usuarios inactivos.  Los requisitos para este artículo son: una cuenta de Azure ([obtenga una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)) y [archivos de claves SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandos rápidos

Configure `/etc/ssh/sshd_config` con la siguiente configuración:

### <a name="disable-password-logins"></a>Deshabilitación de inicios de sesión con contraseña

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>Deshabilitación del inicio de sesión del usuario raíz

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>Lista de grupos permitidos

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>Lista de usuarios permitidos

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>Deshabilitación del protocolo SSH versión 1

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>Bits de clave mínima

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>Desconexión de usuarios inactivos

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

SSHD es el servidor SSH que se ejecuta en la máquina virtual de Linux.  SSH es un cliente que se ejecuta desde un shell en la estación de trabajo MacBook o Linux o desde un Bash en Windows.  SSH es también el protocolo usado para proteger y cifrar la comunicación entre su estación de trabajo y la máquina virtual Linux haciendo que SSH sea también una VPN (Red privada virtual).

Para este artículo es muy importante mantener abierto un inicio de sesión en la máquina virtual Linux para el tutorial completo.  Una vez establecida una conexión SSH, permanece como una sesión abierta siempre y cuando no se cierre la ventana.  Tener un terminal conectado permite realizar cambios en el servicio SSHD sin que se bloquee si se realiza un cambio importante.  Si la máquina virtual Linux se bloquea con una configuración SSHD dañada, Azure ofrece la posibilidad de restablecer una configuración SSHD dañada con la [extensión de acceso de la máquina virtual de Azure](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Por este motivo, abriremos dos terminales y SSH en la máquina virtual Linux de ambos.  Usaremos el primer terminal para realizar los cambios en el archivo de configuración de SSHD y reiniciar el servicio SSHD.  Usaremos el segundo terminal para probar los cambios una vez que se reinicia el servicio.  Puesto que estamos deshabilitando las contraseñas SSH y confiando estrictamente en las claves SSH, si las claves SSH no son correctas y cierra la conexión con la máquina virtual, la máquina virtual se bloqueará permanentemente y nadie podrá iniciar sesión en ella y solicitar que se elimine y vuelva a crear.

## <a name="disable-password-logins"></a>Deshabilitación de inicios de sesión con contraseña

La forma más rápida de proteger las máquinas virtuales Linux es deshabilitar los inicios de sesión con contraseña.  Cuando se habilitan los inicios de sesión con contraseña, los bots que rastrean la web empiezan inmediatamente a intentar adivinar por fuerza bruta la contraseña para la máquina virtual Linux que usa SSH.  Deshabilitar completamente los inicios de sesión con contraseña, permite que el servidor SSH ignore todos los intentos de inicio de sesión con contraseña.

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>Deshabilitación del inicio de sesión del usuario raíz

De acuerdo con los procedimientos recomendados de Linux, el `root` usuario nunca se debe registrar a través de SSH o mediante `sudo su`.  Todos los comandos que necesitan permisos de nivel raíz se deben ejecutar siempre mediante el comando `sudo` que registra todas las acciones para su futura auditoría.  Impedir que el usuario `root` inicie sesión mediante SSH es un procedimiento recomendado de seguridad que garantiza que solo los usuarios autorizados puedan iniciar sesión con SSH.

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>Lista de grupos permitidos

SSH ofrece un método para restringir los usuarios y grupos a los que se permite o deniega el inicio de sesión mediante SSH.  Esta característica utiliza listas para aprobar o rechazar usuarios y grupos concretos a la hora de iniciar sesión.  La configuración del grupo wheel en la lista `AllowGroups` restringe los inicios de sesión aprobados mediante SSH a solo las cuentas de usuario que se encuentran en este grupo.

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>Lista de usuarios permitidos

Restringir los inicios de sesión SSH solo a los usuarios es una manera más específica de realizar la misma tarea que realiza `AllowGroups`.  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>Deshabilitación del protocolo SSH versión 1

La versión 1 del protocolo SSH no es segura y debería estar deshabilitada.  La versión 2 del protocolo SSH es la versión actual que ofrece una forma segura de iniciar sesión mediante SSH en el servidor.  Deshabilitar la versión 1 de SSH rechazará a todos los clientes SSH que están intentando establecer una conexión con el servidor SSH mediante la versión 1.  Solo se permitirá negociar una conexión con el servidor SSH a las conexiones realizadas mediante la versión 2 de SSH.

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>Bits de clave mínima

De acuerdo con los procedimientos de seguridad recomendados, se deshabilitan los inicios de sesión de SSH con contraseña y solo se permite usar las claves SSH para autenticarse con el servidor SSH.  Estas claves SSH se pueden crear mediante claves de diferente longitud, medidas en bits.  Los procedimientos recomendados establecen que las claves de 2048 bits de longitud son el nivel mínimo de clave aceptable.  Las claves de menos de 2048 bits teóricamente podrían no ser seguras.  Establecer `ServerKeyBits` en `2048` permitirá aprobar las conexiones con claves de 2048 bits o más y denegar las conexiones de menos de 2048 bits.

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>Desconexión de usuarios inactivos

SSH tiene la capacidad de desconectar a los usuarios que tengan conexiones abiertas que hayan permanecido inactivas durante más de un intervalo de segundos determinado.  Mantener las sesiones abiertas solo para aquellos usuarios que están activos limita la exposición de la máquina virtual Linux.

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>Reinicio de SSHD

Para habilitar la configuración en `/etc/ssh/sshd_config` reinicie el proceso SSHD que permite reiniciar el servidor SSH.  La ventana del terminal que utilice para reiniciar el servidor SSH permanecerá abierta sin perder la sesión SSH abierta.  Para probar la nueva configuración del servidor SSH use una segunda ventana o pestaña de terminal.  El uso de un terminal independiente para probar la conexión SSH le permite volver atrás y realizar cambios adicionales en el `/etc/ssh/sshd_config` en el primer terminal, sin que se bloquee por un cambio importante de SSHD.  

### <a name="on-redhat-centos-and-fedora"></a>En Redhat, Centos y Fedora

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>En Debian y Ubuntu

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>Restablecimiento de SSHD mediante acceso restablecido de Azure

Si está bloqueado por un cambio importante en la configuración de SSHD puede utilizar la extensión de acceso de máquina virtual de Azure para restablecer la configuración original de SSHD.

Reemplace los nombres de ejemplo por los suyos propios.

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>Instalación de Fail2ban

Se recomienda encarecidamente instalar y configurar la aplicación de código abierto Fail2ban, que permite bloquear los intentos reiterados de iniciar sesión en la máquina virtual Linux a través de SSH mediante la fuerza bruta.  Fail2ban registra los intentos fallidos reiterados de iniciar sesión mediante SSH y, a continuación, crea reglas de firewall para bloquear la dirección IP desde la que se originan los intentos.

* [Página principal de Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha configurado y bloqueado el servidor SSH en la máquina virtual Linux, hay procedimientos recomendados de seguridad adicionales que puede seguir.  

* [Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux de Azure con la extensión VMAccess](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Acceso y seguridad en plantillas de Azure Resource Manager](virtual-machines-linux-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


