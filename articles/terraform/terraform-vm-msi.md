---
title: Uso de una imagen de Azure Marketplace para crear una máquina virtual Linux de Terraform con Managed Service Identity
description: Use una imagen de Marketplace para crear una máquina virtual Linux de Terraform con Managed Service Identity y la administración del estado remoto de forma que pueda implementar fácilmente recursos en Azure.
keywords: terraform, devops, MSI, virtual machine, remote state, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: 5f0ee2904c1072a5ad8c5f7ae1c90e649cc4813c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413803"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Uso de una imagen de Azure Marketplace para crear una máquina virtual Linux de Terraform con Managed Service Identity

En este artículo, se explica cómo usar una [imagen de Marketplace de Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para crear una máquina virtual Ubuntu Linux (16.04 LTS) con la versión más reciente de [Terraform](https://www.terraform.io/intro/index.html) instalada y configurada mediante [Managed Service Identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Esta imagen también configura un back-end remoto para habilitar la administración del [estado remoto](https://www.terraform.io/docs/state/remote.html) mediante Terraform. 

La imagen de Marketplace de Terraform permite empezar a trabajar fácilmente con Terraform en Azure sin necesidad de instalar ni configurar Terraform manualmente. 

No se aplica ningún cargo de software por esta imagen de máquina virtual de Terraform. Solo tendrá que abonar las cuotas de uso del hardware de Azure, que se calculan en función del tamaño de la máquina virtual que se aprovisiona. Para más información acerca de las cuotas de proceso, consulte la [página de precios de las máquinas virtuales Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>requisitos previos
Para poder crear una máquina virtual Linux de Terraform, debe tener una suscripción a Azure. Si aún no tiene una, consulte [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Creación de la máquina virtual de Terraform 

Estos son los pasos que debe seguir para crear una instancia de una máquina virtual Linux de Terraform: 

1. En Azure Portal, vaya a la lista [Crear un recurso](https://ms.portal.azure.com/#create/hub).

2. En la barra de búsqueda de **Buscar en el Marketplace**, busque **Terraform**. Seleccione la plantilla de **Terraform**. 

3. En la pestaña de detalles de Terraform que encontrará en la parte inferior derecha, seleccione el botón **Crear**.

    ![Creación de una máquina virtual de Terraform](media\terraformmsi.png)

4. En las secciones siguientes, se proporciona información acerca de cada uno de los pasos del asistente para crear la máquina virtual Linux de Terraform. En la siguiente sección, se muestran los datos necesarios para configurar cada uno de estos pasos.

## <a name="details-on-the-create-terraform-tab"></a>Datos de la pestaña Create Terraform (Crear Terraform)

Especifique los datos siguientes en la pestaña **Create Terraform** (Crear Terraform):

1. **Aspectos básicos**
    
   * **Nombre**: nombre de la máquina virtual de Terraform.
   * **Nombre de usuario**: primer identificador de inicio de sesión de la cuenta.
   * **Contraseña**: primera contraseña de la cuenta. (Puede usar una clave pública SSH en lugar de una contraseña).
   * **Suscripción**: suscripción en la que se va a crear y facturar la máquina. Debe tener privilegios de creación de recursos en esta suscripción.
   * **Grupo de recursos**: especifique un grupo de recursos nuevo o existente.
   * **Ubicación**: seleccione el centro de datos más adecuado. Normalmente, será el centro de datos que tenga la mayor parte de los datos o el que esté más cerca de su ubicación física para disfrutar de un acceso más rápido a la red.

2. **Configuración adicional**

   * **Tamaño**: tamaño de la máquina virtual. 
   * **Tipo de disco de máquina virtual**: SSD o HDD.

3. **Resumen Terraform**

   * Compruebe que toda la información que ha especificado es correcta. 

4. **Comprar**

   * Para iniciar el proceso de aprovisionamiento, seleccione **Comprar**. Se proporciona un vínculo a los términos de la transacción. No se aplicará ningún otro cargo a la máquina virtual más que los aplicables por la capacidad de proceso correspondiente en función del tamaño de servidor elegido en el paso de selección de tamaño.

La imagen de máquina virtual de Terraform realiza los pasos siguientes:

* Crea una máquina virtual con una identidad asignada por el sistema basada en la imagen de Ubuntu 16.04 LTS.
* Instala la extensión MSI en la máquina virtual para permitir que se emitan tokens de OAuth para los recursos de Azure.
* Asigna permisos de RBAC a la identidad administrada y le concede derechos de propietario para el grupo de recursos.
* Crea una carpeta con la plantilla de Terraform (tfTemplate).
* Establece la configuración previa del estado remoto de Terraform con el back-end de Azure.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Configuración de una máquina virtual Linux de Terraform y acceso a ella

Después de crear la máquina virtual, puede iniciar sesión en ella mediante SSH. Utilice las credenciales de la cuenta que creó en la sección "Aspectos básicos" del paso 3 con la interfaz de shell de texto. En Windows, puede descargar una herramienta de cliente SSH como [Putty](http://www.putty.org/).

Después de utilizar SSH para conectarse a la máquina virtual, tendrá que conceder permisos de colaborador a toda la suscripción de Managed Service Identity en la máquina virtual. 

Los permisos de colaborador ayudan a MSI en la máquina virtual a usar Terraform para crear recursos fuera del grupo de recursos de la máquina virtual. Puede hacerlo fácilmente; basta con ejecutar un script una sola vez. Use el comando siguiente:

`. ~/tfEnv.sh`

El script anterior usa el mecanismo de [inicio de sesión interactivo de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) para autenticarse con Azure y asignar el permiso de colaborador de Managed Service Identity de la máquina virtual en toda la suscripción. 

 La máquina virtual tiene un back-end de estado remoto de Terraform. Para habilitarlo en la implementación de Terraform, copie el archivo remoteState.tf del directorio tfTemplate en la raíz de los scripts de Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Para más información acerca de Remote State Management, consulte [esta página sobre el estado remoto de Terraform](https://www.terraform.io/docs/state/remote.html). La clave de acceso de almacenamiento se expone en este archivo y debe excluirse antes de confirmar los archivos de configuración de Terraform en el control de código fuente.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a configurar una máquina virtual Linux de Terraform en Azure. Aquí tiene algunos recursos adicionales donde encontrará más información acerca de Terraform en Azure: 

 [Centro de Terraform en Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentación del proveedor de Azure para Terraform](http://aka.ms/terraform)  
 [Origen del proveedor de Azure para Terraform](http://aka.ms/tfgit)  
 [Módulos de Azure para Terraform](http://aka.ms/tfmodules)
 

















