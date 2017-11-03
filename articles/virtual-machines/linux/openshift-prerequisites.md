---
title: Requisitos previos para OpenShift en Azure | Microsoft Docs
description: Requisitos previos para implementar OpenShift en Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Requisitos previos comunes para OpenShift en Azure

Al implementar OpenShift en Azure, hay algunos requisitos previos comunes independientemente de si está implementando OpenShift Origin o OpenShift Container Platform.

La instalación de OpenShift se realiza a través de cuadernos de estrategias de Ansible. Ansible usa SSH para conectarse a todos los hosts que formarán parte del clúster para completar los pasos de instalación.
Cuando se inicia la conexión SSH a los hosts remotos, no hay ninguna manera de escribir una frase de contraseña. Por este motivo, la clave privada no puede tener una frase de contraseña asociada o se producirá un error en la implementación.
Dado que todas las máquinas virtuales se implementan a través de plantillas de Resource Manager, se usa la misma clave pública para tener acceso a todas las máquinas virtuales. Es necesario insertar la clave privada correspondiente en la máquina virtual que está ejecutando todos los cuadernos de estrategias.
Para hacerlo de forma segura, se usa Azure Key Vault para pasar la clave privada a la máquina virtual.

Si es necesario el almacenamiento persistente de contenedores, también se necesitan volúmenes persistentes. Estos volúmenes persistentes necesitan estar respaldados por algún tipo de almacenamiento persistente. OpenShift es compatible con discos de Azure (VHD) para esta funcionalidad, pero Azure debe estar configurado en primer lugar como proveedor en la nube. En este modelo, OpenShift:

- Creará un objeto de disco duro virtual en una cuenta de Azure Storage.
- Montará el disco duro virtual en una máquina virtual y formateará el volumen.
- Montará el volumen en el pod.

Para que esto funcione, OpenShift necesita permisos para realizar las tareas anteriores en Azure. Para lograr esto, se necesita una entidad de servicio. La entidad de servicio es una cuenta de seguridad de Azure Active Directory con permisos para los recursos.
La entidad de servicio debe tener acceso a las cuentas de Storage y a las máquinas virtuales que componen el clúster. Si todos los recursos de clúster de OpenShift se implementan en un único grupo de recursos, pueden concederse permisos a la entidad de servicio para ese grupo de recursos.

En esta guía se describe cómo crear los artefactos asociados con los requisitos previos.

> [!div class="checklist"]
> * Crear un valor de KeyVault para administrar las claves SSH para el clúster de OpenShift.
> * Crear una entidad de servicio para que la use el proveedor en la nube de Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 
Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla, o bien haga clic en **Pruébelo** para usar Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se recomienda usar un grupo de recursos exclusivo para hospedar Key Vault, independiente del grupo de recursos en el que se implementarán los recursos del clúster de OpenShift. 

En el ejemplo siguiente se crea un grupo de recursos denominado *keyvaultrg* en la ubicación *eastus*.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Cree un valor de KeyVault para almacenar las claves SSH para el clúster con el comando [az keyvault create](/cli/azure/keyvault#create). El nombre de Key Vault debe ser único globalmente.

En el ejemplo siguiente se crea un almacén de claves denominado *keyvault* en el grupo de recursos *keyvaultrg*.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Creación de una clave SSH 
Se necesita una clave SSH para proteger el acceso al clúster de OpenShift Origin. Cree un par de claves SSH con el comando `ssh-keygen` (en Linux o Mac).
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> El par de claves SSH que cree no puede tener una frase de contraseña.

Para más información sobre las claves SSH en Windows consulte [cómo se crean las claves SSH en Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Almacenamiento de la clave privada de SSH en Key Vault
La implementación de OpenShift utiliza la clave SSH que creó para proteger el acceso al maestro de OpenShift. Para permitir que la implementación recupere de forma segura la clave SSH, almacénela en Key Vault con el comando siguiente:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio 
OpenShift se comunica con Azure mediante un nombre de usuario y una contraseña, o a través de una entidad de servicio. Las entidades de servicio de Azure son identidades de seguridad que pueden usarse con aplicaciones, servicios y herramientas de automatización como OpenShift. El usuario controla los permisos y los define con respecto a cuáles son las operaciones que la entidad de servicio puede realizar en Azure. Para incrementar la seguridad más allá de un nombre de usuario y una contraseña, en este ejemplo se crea una entidad de servicio básica.

Cree una entidad de servicio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) y genere las credenciales necesarias para OpenShift.

En el ejemplo siguiente se crea una entidad de servicio y se le asignan permisos de colaborador a un grupo de recursos denominado myResourceGroup. Si usa Windows, ejecute ```az group show --name myResourceGroup --query id``` por separado y use el resultado para alimentar la opción --scopes.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Tome nota de la propiedad de appId devuelta por el comando.
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > No cree una contraseña que no sea segura.  Siga la guía de las [restricciones y reglas de contraseña de Azure AD](/azure/active-directory/active-directory-passwords-policy).

Para más información, sobre las entidades de servicio, consulte [Creación de una entidad de servicio de Azure con la CLI de Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>Pasos siguientes

En este artículo se trataron los temas siguientes:
> [!div class="checklist"]
> * Crear un valor de KeyVault para administrar las claves SSH para el clúster de OpenShift.
> * Crear una entidad de servicio para que la use el proveedor en la nube de Azure.

Ahora ya puede implementar un clúster de OpenShift

- [Deploy OpenShift Origin](./openshift-origin.md) (Implementar OpenShift Origin)
- [Deploy OpenShift Container Platform](./openshift-container-platform.md) (Implementar OpenShift Container Platform)

