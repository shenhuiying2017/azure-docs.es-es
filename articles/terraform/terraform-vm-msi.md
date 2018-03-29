---
title: Uso de una imagen de Azure Marketplace para crear una máquina virtual Linux de Terraform con Managed Service Identity
description: Use una imagen de Marketplace para crear una máquina virtual Linux de Terraform con Managed Service Identity y administración del estado remoto con el fin de implementar fácilmente recursos en Azure.
keywords: terraform, devops, MSI, virtual machine, remote state, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Uso de una imagen de Azure Marketplace para crear una máquina virtual Linux de Terraform con Managed Service Identity

En este artículo se muestra cómo usar una [imagen de Marketplace de Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) para crear una máquina virtual `Ubuntu Linux VM (16.04 LTS)` con la versión más reciente de [Terraform](https://www.terraform.io/intro/index.html) instalada y configurada mediante [Managed Service Identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Esta imagen también configura un back-end remoto para habilitar la administración del [estado remoto](https://www.terraform.io/docs/state/remote.html) mediante Terraform. La imagen de Marketplace de Terraform permite iniciarse fácilmente en el uso de Terraform en Azure en cuestión de minutos, sin tener que instalar Terraform ni configurar manualmente la autenticación. 

No se aplica ningún cargo de software por esta imagen de máquina virtual de Terraform. Solo se pagan las cuotas de uso del hardware de Azure, que se calculan en función del tamaño de la máquina virtual aprovisionada. Se puede encontrar más información sobre las cuotas de proceso en la [página de precios de Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>requisitos previos
Antes de poder crear una máquina virtual Linux de Terraform, debe tener una suscripción a Azure. Si ya tiene una, consulte [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Creación de la máquina virtual de Terraform 

Estos son los pasos para crear una instancia de una máquina virtual Linux de Terraform. 

1. Vaya a la lista [Crear un recurso](https://ms.portal.azure.com/#create/hub) en Azure Portal.
2. Busque `Terraform` en la barra de búsqueda `Search the Marketplace`. Seleccione la plantilla `Terraform`. Seleccione el botón **Crear** en la pestaña de detalles de Terraform del lado inferior derecho.
![texto alternativo](media\terraformmsi.png)
3. Las secciones siguientes proporcionan entradas para cada uno de los pasos del asistente (**enumerados en la parte derecha**) para crear la máquina virtual Linux de Terraform.  Estas son las entradas necesarias para configurar cada uno de estos pasos:

## <a name="details-in-create-terraform-tab"></a>Detalles de la pestaña de creación de Terraform

Estos son los datos que es necesario especificar en la pestaña Create Terraform (Crear Terraform).

a. **Aspectos básicos**
    
* **Nombre**: nombre de la máquina virtual de Terraform.
* **Nombre de usuario**: identificador de acceso de la primera cuenta.
* **Contraseña**: contraseña de la primera cuenta (puede usar una clave pública SSH en lugar de una contraseña).
* **Suscripción**: si tiene más de una suscripción, seleccione aquella en la que se creará y facturará la máquina. Debe tener privilegios de creación de recursos en esta suscripción.
* **Grupo de recursos**: puede crear uno o usar un grupo ya existente.
* **Ubicación**: seleccione el centro de datos más adecuado. Normalmente, es el centro de datos que tenga la mayoría de los datos o el que esté más cerca de su ubicación física para disfrutar de un acceso más rápido a la red.

b. **Configuración adicional**

* Tamaño: tamaño de la máquina virtual.
* Tipo de disco de máquina virtual: elija entre SSD y HDD.

c. **Resumen Terraform**

* Compruebe que toda la información que ha especificado es correcta. 

d. **Comprar**

* Para iniciar el aprovisionamiento, haga clic en Comprar. Se proporciona un vínculo a los términos de la transacción. No se aplica ningún cargo adicional a la máquina virtual que no sea el del proceso por el tamaño de servidor elegido en el paso de selección de tamaño.

La imagen de máquina virtual de Terraform realiza los pasos siguientes:

* Crea una máquina virtual con una identidad asignada por el sistema basada en la imagen de Ubuntu 16.04 LTS.
* Instala la extensión MSI en la máquina virtual para permitir que se emitan tokens de OAuth para los recursos de Azure.
* Asigna permisos de RBAC a la identidad administrada y le concede derechos de propietario para el grupo de recursos.
* Crea una carpeta con la plantilla de Terraform (tfTemplate).
* Configura previamente el estado remoto de Terraform con el back-end de Azure.

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Configuración de una máquina virtual Linux de Terraform y acceso a ella

Después de crear la máquina virtual, puede iniciar sesión en ella mediante SSH. Utilice las credenciales de la cuenta que creó en la sección Aspectos básicos del paso 3 para la interfaz de shell de texto. En Windows, puede descargar una herramienta de cliente SSH como [Putty](http://www.putty.org/).

Una vez que ha usado `SSH` para conectarse a la máquina virtual, debe conceder permisos de colaborador a Managed Service Identity para toda la suscripción en la máquina virtual. Los permisos de colaborador ayudan a MSI en la máquina virtual a usar Terraform para crear recursos fuera del grupo de recursos de la máquina virtual. Para conseguir fácilmente esta acción, ejecute un script una vez. Este es el comando para hacerlo.

`. ~/tfEnv.sh`

El script anterior usa el mecanismo de [inicio de sesión interactivo de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) para autenticarse con Azure y asignar el permiso de colaborador de Managed Service Identity de la máquina virtual para toda la suscripción. 

 La máquina virtual tiene creado un back-end de estado remoto de Terraform y, para habilitarlo en la implementación de Terraform, es necesario copiar el archivo remoteState.tf del directorio tfTemplate a la raíz de los scripts de Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Puede leer más sobre la administración del estado remoto [aquí](https://www.terraform.io/docs/state/remote.html). La clave de acceso de almacenamiento se expone en este archivo y debe protegerse con cuidado en el control de código fuente.  

## <a name="next-steps"></a>Pasos siguientes
En este artículo aprendió a configurar una máquina virtual Linux de Terraform en Azure. Aquí tiene algunos recursos adicionales para aprender más sobre Terraform en Azure. 

 [Centro de Terraform en Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentación del proveedor de Azure para Terraform](http://aka.ms/terraform)  
 [Origen del proveedor de Azure para Terraform](http://aka.ms/tfgit)  
 [Módulos de Azure para Terraform](http://aka.ms/tfmodules)
 

















