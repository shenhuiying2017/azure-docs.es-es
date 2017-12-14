---
title: "Azure Active Directory Domain Services: Unión de una máquina virtual CentOS a un dominio administrado | Microsoft Docs"
description: "Unión de una máquina virtual CentOS Linux a Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 65a9e4267c8883db5c8d8bfc5e0167577cd969d3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Unión de una máquina virtual CentOS Linux a un dominio administrado
Este artículo muestra cómo unir una máquina virtual CentOS Linux en Azure a un dominio administrado de Azure AD Domain Services.

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:
1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](active-directory-ds-getting-started.md).
4. Asegúrese de que ha configurado las direcciones IP del dominio administrado como servidores DNS de la red virtual. Para más información, consulte [cómo actualizar la configuración de DNS para la red virtual de Azure](active-directory-ds-getting-started-dns.md)
5. Complete los pasos necesarios para [sincronizar contraseñas para el dominio administrado de Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Aprovisionamiento de una máquina virtual CentOS Linux
Aprovisione una máquina virtual CentOS Linux en Azure mediante cualquiera de los métodos siguientes:
* [Portal de Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI de Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implemente la máquina virtual en la **misma red virtual en la que ha habilitado Azure AD Domain Services**.
> * Elija una **subred diferente** de aquella en la que ha habilitado Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conexión remota a la máquina virtual de Linux recién aprovisionada
Se ha aprovisionado la máquina virtual CentOS en Azure. La siguiente tarea consiste en conectar de forma remota a la máquina virtual mediante la cuenta de administrador local creada al aprovisionar la máquina virtual.

Siga las instrucciones que aparecen en el artículo [cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configuración del archivo hosts en la máquina virtual Linux
En el terminal SSH, edite el archivo /etc/hosts y actualice la dirección IP y el nombre de host de la máquina.

```
sudo vi /etc/hosts
```

En el archivo hosts, escriba el siguiente valor:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
En este caso, "contoso100.com" es el nombre de dominio DNS del dominio administrado. "contoso-centos" es el nombre de host de la máquina virtual CentOS que va a unir al dominio administrado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalación de los paquetes necesarios en la máquina virtual de Linux
A continuación, instale los paquetes necesarios para unirse a un dominio en la máquina virtual. En el terminal SSH, escriba el siguiente comando para instalar los paquetes necesarios:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Unión de la máquina virtual de Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

1. Detecte el dominio administrado con Servicios de dominio de AAD. En el terminal SSH, escriba el siguiente comando:

    ```
    sudo realm discover CONTOSO100.COM
    ```

      > [!NOTE]
      > **Solución de problemas:** Si la *detección de dominio kerberos* no puede encontrar el dominio administrado:
        * Ensure that the domain is reachable from the virtual machine (try ping).
        * Check that the virtual machine has indeed been deployed to the same virtual network in which the managed domain is available.
        * Check to see if you have updated the DNS server settings for the virtual network to point to the domain controllers of the managed domain.
      >

2. Inicialice Kerberos. En el terminal SSH, escriba el siguiente comando:

    > [!TIP]
    > * Especifique un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD".
    > * Especifique el nombre de dominio en mayúsculas o kinit generará un error.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Una la máquina al dominio. En el terminal SSH, escriba el siguiente comando:

    > [!TIP]
    > Utilice la misma cuenta de usuario que ha especificado el paso anterior ("kinit").
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Debe obtener un mensaje (Máquina inscrita correctamente en el dominio kerberos) cuando la máquina está unida correctamente al dominio administrado.


## <a name="verify-domain-join"></a>Verificación de la unión a un dominio
Verifique si la máquina se ha unido correctamente al dominio administrado. Conéctese a la máquina virtual CentOS unida al dominio con otra conexión SSH. Utilice una cuenta de usuario del dominio y, a continuación, compruebe si la cuenta de usuario se ha resuelto correctamente.

1. En el terminal SSH, escriba el comando siguiente para conectarse a la máquina virtual CentOS unida al dominio con SSH. Use una cuenta de dominio que pertenezca al dominio administrado (por ejemplo, "bob@CONTOSO100.COM" en este caso).
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. En el terminal SSH, escriba el comando siguiente para ver si el directorio principal se ha inicializado correctamente.
    ```
    pwd
    ```

3. En el terminal SSH, escriba el comando siguiente para ver si los miembros del grupo se están resolviendo correctamente.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Solución de problemas de unión al dominio
Consulte el artículo [Solución de problemas de unión al dominio](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join) .

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Inicio de sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Instalación de Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7: guía de integración de Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
