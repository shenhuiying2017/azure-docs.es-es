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
ms.openlocfilehash: 5e287cd29fb305e78fe6338782838929007b17fc
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Requisitos previos comunes para la implementación de OpenShift en Azure

En este artículo se describen los requisitos previos comunes para implementar OpenShift Origin u OpenShift Container Platform en Azure.

La instalación de OpenShift usa cuadernos de estrategias de Ansible. Ansible usa Secure Shell (SSH) para conectarse a todos los hosts del clúster y completar los pasos de instalación.

Cuando se inicia la conexión SSH a los hosts remotos, no se puede escribir una contraseña. Por este motivo, la clave privada no puede tener una contraseña asociada o se producirá un error en la implementación.

Dado que todas las máquinas virtuales (VM) se implementan a través de plantillas de Azure Resource Manager, se usa la misma clave pública para acceder a todas ellas. También es necesario insertar la clave privada correspondiente en la máquina virtual que ejecuta todos los cuadernos de estrategias. Para hacerlo de forma segura, se usa Azure Key Vault para pasar la clave privada a la máquina virtual.

Si es necesario el almacenamiento persistente de contenedores, también se necesitan volúmenes persistentes. OpenShift es compatible con discos duros virtuales (VHD) de Azure para esta funcionalidad, pero Azure debe configurarse primero como proveedor de nube. 

En este modelo, OpenShift:

- Crea un objeto de VHD en una cuenta de Azure Storage.
- Monta el VHD en una máquina virtual y formatea el volumen.
- Monta el volumen en el pod.

Para que esta configuración funcione, OpenShift necesita permisos para realizar las tareas anteriores en Azure. Para lograrlo, se necesita una entidad de servicio. La entidad de servicio es una cuenta de seguridad de Azure Active Directory con permisos para los recursos.

La entidad de servicio debe tener acceso a las cuentas de almacenamiento y a las máquinas virtuales que componen el clúster. Si todos los recursos de clúster de OpenShift se implementan en un único grupo de recursos, pueden concederse permisos a la entidad de servicio para ese grupo de recursos.

En esta guía se describe cómo crear los artefactos asociados con los requisitos previos.

> [!div class="checklist"]
> * Crear un almacén de claves para administrar las claves SSH para el clúster de OpenShift.
> * Crear una entidad de servicio para que la use el Proveedor de soluciones en la nube de Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure 
Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla, o bien haga clic en **Pruébelo** para usar Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Debe usar un grupo de recursos dedicado para hospedar el almacén de claves. Este grupo es independiente del grupo de recursos en el que se implementan los recursos del clúster de OpenShift. 

En el ejemplo siguiente se crea un grupo de recursos denominado *keyvaultrg* en la ubicación *eastus*:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Cree un almacén de claves para almacenar las claves SSH para el clúster con el comando [az keyvault create](/cli/azure/keyvault#create). El nombre del almacén de claves debe ser único globalmente.

En el ejemplo siguiente se crea un almacén de claves denominado *keyvault* en el grupo de recursos *keyvaultrg*:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Creación de una clave SSH 
Se necesita una clave SSH para proteger el acceso al clúster de OpenShift Origin. Cree un par de claves SSH con el comando `ssh-keygen` (en Linux o macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> El par de claves SSH no puede tener una contraseña.

Para obtener más información sobre las claves SSH en Windows, consulte el artículo sobre la [creación de claves SSH en Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Almacenamiento de la clave privada SSH en Azure Key Vault
La implementación de OpenShift utiliza la clave SSH que creó para proteger el acceso al maestro de OpenShift. Para permitir que la implementación recupere de forma segura la clave SSH, almacénela en Key Vault con el comando siguiente:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio 
OpenShift se comunica con Azure mediante un nombre de usuario y una contraseña, o bien a través de una entidad de servicio. Las entidades de servicio de Azure son identidades de seguridad que pueden usarse con aplicaciones, servicios y herramientas de automatización como OpenShift. El usuario controla los permisos y los define con respecto a cuáles son las operaciones que la entidad de servicio puede realizar en Azure. Para incrementar la seguridad más allá de un nombre de usuario y una contraseña, en este ejemplo se crea una entidad de servicio básica.

Cree una entidad de servicio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) y genere las credenciales necesarias para OpenShift.

En el ejemplo siguiente se crea una entidad de servicio y se le asignan permisos de colaborador para un grupo de recursos denominado myResourceGroup. Si usa Windows, ejecute ```az group show --name myResourceGroup --query id``` por separado y use el resultado para alimentar la opción --scopes.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Tome nota de la propiedad appId que devuelve el comando:
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
 > Asegúrese de crear una contraseña segura. Siga la guía de las [restricciones y reglas de contraseña de Azure AD](/azure/active-directory/active-directory-passwords-policy).

Para obtener más información sobre las entidades de servicio, consulte [Creación de una entidad de servicio de Azure con la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>pasos siguientes

En este artículo se trataron los temas siguientes:
> [!div class="checklist"]
> * Crear un almacén de claves para administrar las claves SSH para el clúster de OpenShift.
> * Crear una entidad de servicio para que la use el Proveedor de soluciones en la nube de Azure.

A continuación, implemente un clúster de OpenShift:

- [Deploy OpenShift Origin](./openshift-origin.md) (Implementar OpenShift Origin)
- [Deploy OpenShift Container Platform](./openshift-container-platform.md) (Implementar OpenShift Container Platform)

