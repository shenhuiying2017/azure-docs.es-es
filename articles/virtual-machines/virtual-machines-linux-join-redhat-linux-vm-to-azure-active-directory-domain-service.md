---
title: "Unión de una máquina virtual RedHat Linux a un servicio de dominio de Azure Active Directory | Microsoft Docs"
description: "Cómo unir una máquina virtual RedHat Enterprise Linux 7 ya existente a un servicio de dominio de Azure Active Directory."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 847a5391cadb00c68e5ee5e12066fe910fd8a259
ms.openlocfilehash: 017c17dc15596ce05400500d709287b7ce52611e


---

# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Unión de una máquina virtual RedHat Linux a un servicio de dominio de Azure Active Directory

Este artículo muestra cómo unir una máquina virtual de Red Hat Enterprise Linux (RHEL) 7 a un dominio administrado con Azure Active Directory Domain Services (AADDS).  Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);

- [archivos de clave SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md).

- [un controlador de dominio de Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos

_Reemplace los ejemplos por su propia configuración._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Cambiar de la CLI de azure al modo de implementación clásica

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Buscar una imagen y versión de RHEL

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Crear una máquina virtual de Redhat Linux

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>SSH a la VM

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Actualizar paquetes YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Instalar los paquetes necesarios

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Detectar el dominio administrado con Servicios de dominio de AAD

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Inicializar kerberos

Asegúrese de especificar un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD". Solo estos usuarios pueden unir equipos al dominio administrado.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Unir la máquina al dominio

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Comprobar que la máquina está unida al dominio

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Pasos siguientes

* [Red Hat Update Infrastructure (RHUI) para máquinas virtuales Red Hat Enterprise Linux a petición en Azure](virtual-machines-linux-update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configuración de una instancia de Key Vault para máquinas virtuales en Azure Resource Manager](virtual-machines-linux-key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y CLI de Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


