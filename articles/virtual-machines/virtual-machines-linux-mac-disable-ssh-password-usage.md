<properties
	pageTitle="Deshabilitación de las contraseñas SSH en la máquina virtual de Linux mediante la configuración de SSHD | Microsoft Azure"
	description="Proteja su máquina virtual de Linux en Azure mediante la deshabilitación de los inicios de sesión mediante contraseña para SSH."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="v-vlivech"/>

# Deshabilitación de las contraseñas SSH en la máquina virtual de Linux mediante la configuración de SSHD

Este artículo se centra en cómo bloquear la seguridad de inicio de sesión de la máquina virtual de Linux. En cuanto se abre el puerto SSH 22 al mundo, los bots intentan iniciar sesión adivinando las contraseñas. Lo que haremos en este artículo es deshabilitar los inicios de sesión de contraseña a través de SSH. Si quitamos completamente la capacidad de usar contraseñas, protegemos la máquina virtual de Linux de este tipo de ataque por fuerza bruta. La ventaja es que configuraremos SSHD de Linux para permitir solo los inicios de sesión a través de claves públicas y privadas de SSH, que son sin duda la forma más segura de iniciar sesión en Linux. Las combinaciones posibles para adivinar la clave privada son inmensas y, por lo tanto, disuade a los bots de incluso en molestarse en obtener las claves SSH por fuerza bruta.


## Objetivos

- Configurar SSHD para impedir:
  - Inicios de sesión de contraseña
  - Inicio de sesión de usuario raíz
  - Autenticación de desafío/respuesta
- Configurar SSHD para permitir:
  - solo inicios de sesión de claves de SSH
- Reiniciar SSHD mientras sigue conectado
- Probar la nueva configuración SSHD

## Introducción

[SSH definido](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD es el servidor SSH que se ejecuta en la máquina virtual de Linux. SSH es un cliente que se ejecuta desde un shell en la estación de trabajo MacBook o Linux. SSH es también el protocolo usado para proteger y cifrar la comunicación entre su estación de trabajo y la máquina virtual de Linux.

Para este artículo es muy importante mantener abierto un inicio de sesión en la máquina virtual de Linux para el tutorial completo. Por este motivo, abriremos dos terminales y SSH en la máquina virtual de Linux de ambos. Usaremos el primer terminal para realizar los cambios en el archivo de configuración de SSHD y reiniciar el servicio SSHD. Usaremos el segundo terminal para probar los cambios una vez que se reinicia el servicio. Puesto que estamos deshabilitando las contraseñas SSH y confiando estrictamente en las claves SSH, si las claves SSH no son correctas y cierra la conexión con la máquina virtual, la máquina virtual se bloqueará permanentemente y nadie podrá iniciar sesión en ella y solicitar que se elimine y vuelva a crear.

## Requisitos previos

- [Creación de claves SSH en Linux y Mac para máquinas virtuales de Linux en Azure](link)
- Cuenta de Azure
  - [registro de versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)
  - [Portal de Azure](http://portal.azure.com)
- Máquina virtual de Linux que se ejecuta en Azure
- Par de claves públicas y privadas SSH en `~/.ssh/`
- Clave pública SSH en `~/.ssh/authorized_keys` en la máquina virtual de Linux
- Derechos de SUDO en la máquina virtual
- Puerto 22 abierto

## Comandos rápidos

_Administradores con experiencia de Linux que desean que la versión TLDR comience aquí. Para cualquier otra persona que desee que la explicación detallada y el tutorial omitan esta sección._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## Tutorial detallado

Inicie sesión en la máquina virtual de Linux en el terminal 1 (T1). Inicie sesión en la máquina virtual de Linux en el terminal 2 (T2).

En T2, vamos a editar el archivo de configuración SSHD.

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Desde aquí, editaremos solo la configuración para deshabilitar las contraseñas y habilitar los inicios de sesión de claves SSH. Hay muchas opciones de configuración en este archivo que debe investigar y cambiar para que Linux y SSH proporcionen la seguridad que necesita.

#### Desactivación de inicios de sesión de contraseña

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### Habilitación de la autenticación de clave pública

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### Deshabilitación del inicio de sesión raíz

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### Deshabilitación de la autenticación de desafío/respuesta

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### Reinicio de SSHD

Desde el shell T1, compruebe que la sesión sigue iniciada. Esto es importante, por lo que no bloquee la máquina virtual si las claves SSH no son correctas, ya que las contraseñas están ahora deshabilitadas. Si la configuración es incorrecta en la máquina virtual de Linux, puede usar T1 para corregir shd\_config, ya que la sesión seguirá iniciada y SSH mantendrá la conexión activa durante el reinicio del servicio de SSHD.

Desde la ejecución de T2:

##### En la familia Debian

```
username@macbook$ sudo service ssh restart
```

##### En la familia RedHat

```
username@macbook$ sudo service sshd restart
```

Las contraseñas ahora están deshabilitadas en la máquina virtual, por lo que se protegen de los intentos de inicio de sesión de contraseña por fuerza bruta. Si solo se permiten las claves SSH, podrá iniciar sesión más rápido y de forma mucho más segura.

<!---HONumber=AcomDC_0323_2016-->