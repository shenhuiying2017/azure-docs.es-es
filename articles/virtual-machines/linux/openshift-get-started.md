---
title: "Implementación de OpenShift Origin en Azure | Microsoft Docs"
description: "Aprenda a implementar OpenShift Origin en máquinas virtuales de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.contentlocale: es-es
ms.lasthandoff: 06/21/2017

---

# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Implementación de OpenShift Origin en máquinas virtuales de Azure 

[OpenShift Origin](https://www.openshift.org/) es una plataforma de contenedor de código abierto basada en [Kubernetes](https://kubernetes.io/). Simplifica el proceso de implementación, escalado y el funcionamiento de las aplicaciones multiempresa. 

Esta guía describe cómo implementar OpenShift Origin en máquinas virtuales de Azure mediante la CLI de Azure y las plantillas de Azure Resource Manager. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un valor de KeyVault para administrar las claves SSH para el clúster de OpenShift.
> * Implementar un clúster de OpenShift en máquinas virtuales de Azure. 
> * Instalar y configurar la [CLI de OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) para administrar el clúster.
> * Personalizar la implementación de OpenShift.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial de inicio rápido se necesita la CLI de Azure 2.0.8 o una versión superior. Para encontrar la versión ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 
Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla, o bien haga clic en **Pruébelo** para usar Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Cree un valor de KeyVault para almacenar las claves SSH para el clúster con el comando [az keyvault create](/cli/azure/keyvault#create).  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Creación de una clave SSH 
Se necesita una clave SSH para proteger el acceso al clúster de OpenShift Origin. Cree un par de claves SSH usando el comando `ssh-keygen`. 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> El par de claves SSH que cree no puede tener una frase de contraseña.

Para más información sobre las claves SSH en Windows consulte [cómo se crean las claves SSH en Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Almacenamiento de la clave privada de SSH en Key Vault
La implementación de OpenShift utiliza la clave SSH que creó para proteger el acceso al maestro de OpenShift. Para habilitar a la implementación para que recupere de forma segura la clave SSH, almacene la clave en Key Vault con el comando siguiente.

# <a name="enabled-for-template-deployment"></a>Habilitación para la implementación de plantilla
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio 
OpenShift se comunica con Azure mediante un nombre de usuario y una contraseña, o a través de una entidad de servicio. Las entidades de servicio de Azure son identidades de seguridad que pueden usarse con aplicaciones, servicios y herramientas de automatización como OpenShift. El usuario controla los permisos y los define con respecto a cuáles son las operaciones que la entidad de servicio puede realizar en Azure. Para incrementar la seguridad más allá de un nombre de usuario y una contraseña, en este ejemplo se crea una entidad de servicio básica.

Cree una entidad de servicio con [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) y produzca las credenciales necesarias para OpenShift:

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
Tome nota de la propiedad de appId devuelta por el comando.
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > No cree una contraseña que no sea segura.  Siga la guía de las [restricciones y reglas de contraseña de Azure AD](/azure/active-directory/active-directory-passwords-policy).

Para más información, sobre las entidades de servicio, consulte [Creación de una entidad de servicio de Azure con la CLI de Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>Implementación de la plantilla de OpenShift Origin
A continuación implemente OpenShift Origin mediante una plantilla de Azure Resource Manager. 

> [!NOTE] 
> El comando siguiente requiere az CLI 2.0.8 o una versión posterior. Puede comprobar la versión de az CLI con el comando `az --version`. Para actualizar la versión de la CLI, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

Use el valor `appId` de la entidad de servicio que creó anteriormente para el parámetro `aadClientId`.

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
La implementación puede tardar hasta 20 minutos en completarse. La dirección URL de la consola de OpenShift y el nombre DNS del maestro OpenShift se imprime en el terminal cuando se haya completado la implementación.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>Conexión con el clúster de OpenShift
Cuando se haya completado la implementación, conéctese a la consola de OpenShift con el explorador usando `OpenShift Console Uri`. Como alternativa, puede conectarse al maestro OpenShift mediante el siguiente comando.

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el clúster de OpenShift y todos los recursos relacionados.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:
> [!div class="checklist"]
> * Crear un valor de KeyVault para administrar las claves SSH para el clúster de OpenShift.
> * Implementar un clúster de OpenShift en máquinas virtuales de Azure. 
> * Instalar y configurar la [CLI de OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) para administrar el clúster.

Ahora el clúster de OpenShift Origin está implementado. Puede seguir los tutoriales de OpenShift para aprender a implementar una primera aplicación y a utilizar las herramientas de OpenShift. Para empezar puede consultar la [Introducción a OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html). 

