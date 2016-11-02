<properties
		pageTitle="Agregar un usuario a una máquina virtual de Linux en Azure | Microsoft Azure"
		description="Agregue un usuario a una máquina virtual de Linux en Azure."
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="08/18/2016"
		ms.author="v-livech"
/>

# Agregar un usuario a una máquina virtual de Azure

Una de las primeras tareas en la nueva máquina virtual Linux consiste en crear un nuevo usuario. En este artículo se explica cómo crear una cuenta de usuario de sudo, establecer la contraseña, añadir claves públicas SSH y, por último, usar `visudo` para permitir sudo sin contraseña.

Requisitos previos: [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/), [claves públicas y privadas de SSH](virtual-machines-linux-mac-create-ssh-keys.md), un grupo de recursos de Azure y la CLI de Azure instalada, que se debe cambiar al modo de Azure Resource Manager mediante `azure config mode arm`.

## Comandos rápidos

```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## Tutorial detallado

### Introducción

Una de las primeras tareas que se suelen realizar en un servidor nuevo es agregar una cuenta de usuario. Se deben deshabilitar los datos de inicio de sesión raíz y no se debe usar la cuenta raíz con el servidor Linux, solo sudo. La manera adecuada de administrar y usar Linux consiste en proporcionar a un usuario privilegios de escalado raíz con sudo.

Con el comando `useradd` estamos agregando las cuentas de usuario a la máquina virtual Linux. Si se ejecuta `useradd`, se modifica `/etc/passwd`, `/etc/shadow`, `/etc/group` y `/etc/gshadow`. Estamos agregando una marca de la línea de comandos al comando `useradd` para agregar el nuevo usuario al grupo de sudo correcto en Linux. Aunque `useradd` crea una entrada en `/etc/passwd`, no asigna una contraseña a la nueva cuenta de usuario. Estamos creando una contraseña inicial para el nuevo usuario mediante un sencillo comando `passwd`. El último paso es modificar las reglas de sudo para permitir que el usuario ejecute comandos con privilegios de sudo sin tener que escribir una contraseña para cada comando. El inicio de sesión con la clave privada, se da por supuesto que esa cuenta de usuario está protegida frente a actores perjudiciales y permitirá el acceso sudo sin contraseña.

### Agregar un solo usuario de sudo a una máquina virtual de Azure

Inicie sesión en la VM de Azure mediante claves SSH. Si no ha configurado el acceso de clave pública SSH, lea primero el artículo [Using Public Key Authentication with Azure](http://link.to/article) (Uso de la autenticación de clave pública con Azure).

El comando `useradd` realiza las tareas siguientes:

- crear una nueva cuenta de usuario
- crear un nuevo grupo de usuarios con el mismo nombre
- agregar una entrada en blanco a `/etc/passwd`
- agregar una entrada en blanco a `/etc/gpasswd`

La marca de la línea de comandos `-G` agrega la nueva cuenta de usuario al grupo de Linux adecuado y le concederá a la nueva cuenta de usuario privilegios de escalado de raíz.

#### Agregar el usuario

```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### Establecer una contraseña

El comando `useradd` crea el usuario y agrega una entrada a `/etc/passwd` y `/etc/gpasswd`, pero realmente no establece la contraseña. La contraseña se agrega a la entrada con el comando `passwd`.

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Ahora tenemos un usuario con privilegios de sudo en el servidor.

### Agregar la clave pública SSH a la nueva cuenta de usuario

En el equipo, use el comando `ssh-copy-id` con la nueva contraseña.

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### Usar visudo para permitir el uso de sudo sin contraseña

El uso de `visudo` para editar el archivo `/etc/sudoers` agrega algunos niveles de protección frente a la modificación incorrecta de este archivo importante. Al ejecutar `visudo`, el archivo `/etc/sudoers` se bloquea para garantizar que ningún otro usuario pueda realizar cambios mientras se está editando activamente. `visudo` también comprueba si existen errores en el archivo `/etc/sudoers` cuando se intenta guardar o salir, por lo que no puede guardar un archivo de sudoers dañado.

Ya tenemos usuarios en el grupo predeterminado correcto para el acceso sudo. Ahora vamos a habilitar estos grupos para usar sudo sin contraseña.

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### Comprobar el usuario, las claves SSH y sudo

```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

<!---HONumber=AcomDC_0824_2016-->