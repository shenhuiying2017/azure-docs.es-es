---
title: Terraform con una ranura de implementación del proveedor de Azure
description: Tutorial de Terraform con una ranura de implementación del proveedor de Azure
keywords: terraform, devops, máquina virtual, Azure, ranuras de implementación
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Uso de Terraform para aprovisionar una infraestructura con ranuras de implementación de Azure

Las [ranuras de implementación de Azure](/azure/app-service/web-sites-staged-publishing) le permiten intercambiar entre distintas versiones de la aplicación (como de producción y de ensayo) para minimizar el impacto de las implementaciones rotas. Este artículo muestra un ejemplo del uso de las ranuras de implementación, en el que se le guía a través de la implementación de dos aplicaciones mediante GitHub y Azure. Una aplicación se hospeda en un "espacio de producción", mientras que la segunda se hospeda en un espacio de "ensayo". (los nombres "producción" y "ensayo" son arbitrarios y pueden ser cualesquiera otros que desee que representen el escenario). Una vez que se hayan configurado las ranuras de implementación, puede usar Terraform para intercambiar entre ambas según sea necesario.

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción a Azure**: si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

- **Cuenta de GitHub**: se necesita una cuenta de [GitHub](http://www.github.com) para bifurcar y usar el repositorio de GitHub de prueba.

## <a name="create-and-apply-the-terraform-plan"></a>Creación y aplicar el plan de Terraform

1. Vaya a [Azure Portal](http://portal.azure.com)

1. Abra [Azure Cloud Shell](/azure/cloud-shell/overview) y, si no lo ha hecho anteriormente, seleccione **Bash** como entorno.

    ![Aviso de Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Cambie al directorio `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Cree un directorio llamado `deploy`.

    ```bash
    mkdir deploy
    ```

1. Cree un directorio llamado `swap`.

    ```bash
    mkdir swap
    ```

1. Compruebe que ambos directorios se han creado correctamente mediante el comando de bash `ls`.

    ![Cloud Shell después de crear directorios](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Cambie al directorio `deploy`.

    ```bash
    cd deploy
    ```

1. Mediante el [editor vi](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), cree un archivo denominado `deploy.tf`, que contendrá la [configuración de Terraform](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Entre en el modo de inserción, para lo que debe presionar la tecla de la letra `i`.

1. Pegue el siguiente código en el editor:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Presione la tecla  **&lt;Esc >** para salir del modo de inserción.

1. Guarde el archivo y salga del editor vi, para lo que debe escribir el siguiente comando y, después, presionar **&lt;Entrar>**:

    ```bash
    :wq
    ```

1. Una vez creado el archivo, puede comprobar su contenido.

    ```bash
    cat deploy.tf
    ```

1. Inicialice Terraform.

    ```bash
    terraform init
    ```

1. Cree el plan de Terraform.

    ```bash
    terraform plan
    ```

1. Aprovisione los recursos definidos en el archivo de configuración `deploy.tf`. (para confirmar la acción escriba `yes` en el aviso).

    ```bash
    terraform apply
    ```

1. Cierre la ventana de Cloud Shell.

1. En el menú principal de Azure Portal, seleccione **Grupos de recursos**.

    ![Grupos de recursos de Azure Portal](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. En la pestaña **Grupos de recursos**, seleccione **slotDemoResourceGroup**.

    ![Grupo de recursos creado por Terraform](./media/terraform-slot-walkthru/resource-group.png)

Cuando termine, verá todos los recursos que ha creado Terraform.

![Recursos creados por Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Bifurcación del proyecto de prueba

Para poder probar la creación y cambiar de un espacio de implementación a otro, es preciso necesita bifurcar el proyecto de prueba de GitHub.

1. Vaya al [repositorio awesome-terraform on GitHub](https://github.com/Azure/awesome-terraform).

1. Bifurque el **repositorio awesome-terraform**.

    ![Bifurcar el repositorio awesome-terraform de GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Siga las indicaciones para bifurcar a su entorno.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Implementación desde GitHub a sus ranuras de implementación

Una vez que bifurque el repositorio del proyecto de prueba, configure las ranuras de implementación, para lo que debe seguir estos pasos:

1. En el menú principal de Azure Portal, seleccione **Grupos de recursos**.

1. Seleccione **slotDemoResourceGroup**.

1. Seleccione **slotAppService**.

1. Seleccione **Opciones de implementación**.

    ![Opciones de implementación de un recurso de App Service](./media/terraform-slot-walkthru/deployment-options.png)

1. En la pestaña **Opción de implementación**, seleccione **Elegir origen**y **GitHub**.

    ![Seleccionar origen de la implementación](./media/terraform-slot-walkthru/select-source.png)

1. Una vez que Azure crea la conexión y muestra todas las opciones, seleccione **Autorización**.

1. En la pestaña **Autorización**, seleccione **Autorizar**y especifique las credenciales necesarias para que Azure acceda a su cuenta de GitHub. 

1. Una vez que Azure valida las credenciales de GitHub, se muestra un mensaje que indica que se ha completado el proceso de autorización. Seleccione **Aceptar** para cerrar la pestaña **Autorización**.

1. Seleccione **Choose your organization** (Elegir su organización) y seleccione su organización.

1. Seleccione **Elegir proyecto**.

1. En la pestaña **Elegir proyecto**, seleccione el proyecto **awesome-terraform**.

    ![Elegir el proyecto awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Seleccione **Elegir rama**.

1. En la pestaña **Elegir rama**, seleccione **maestra**.

    ![Elegir la rama maestra](./media/terraform-slot-walkthru/choose-branch-master.png)

1. En la pestaña **Opción de implementación**, haga clic en **Aceptar**.

Ya ha implementado el espacio de producción. Para implementar la zona de ensayo, realice todos los pasos anteriores de esta sección con únicamente las modificaciones siguientes:

- En el paso 3, recurso **slotAppServiceSlotOne**.

- En el paso 13, seleccione la rama "de trabajo", en lugar de la rama maestra.

    ![Elegir rama de trabajo](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Prueba de las implementaciones de aplicaciones

En las secciones anteriores, se configuraron dos ranuras (**slotAppService** y **slotAppServiceSlotOne**), con el fin de poder realizar implementaciones desde distintas bifurcaciones de GitHub. Vamos a realizar una versión preliminar de las aplicaciones web para validar que se han implementado correctamente.

Realice estos pasos dos veces. En la primera, en el paso 3, seleccione **slotAppService** la primera vez, mientras que en la segunda debe seleccionar **slotAppServiceSlotOne**:

1. En el menú principal de Azure Portal, seleccione **Grupos de recursos**.

1. Seleccione **slotDemoResourceGroup**.

1. Seleccione **slotAppService** o **slotAppServiceSlotOne**.

1. En la página de información general, seleccione **URL**.

    ![Seleccione la dirección URL en la pestaña de información general para representar la aplicación](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Azure puede tardar varios minutos en crear e implementar el sitio desde GitHub.
>
>

En el caso de la aplicación web **slotAppService**, verá una página azul con el título **Slot Demo App 1**. En el caso de la aplicación web **slotAppServiceSlotOne**, verá una página verde con el título **Slot Demo App 2**.

![Obtener una versión preliminar de las aplicaciones para probar si se han implementado correctamente](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Intercambio de las dos ranuras de implementación

Para probar el intercambio de las dos ranuras de implementación, siga estos pasos:
 
1. Cambie a la pestaña del explorador en que se ejecuta **slotAppService** (la aplicación con la página azul). 

1. Vuelva a Azure Portal en otra pestaña.

1. Abra Cloud Shell.

1. Cambie al directorio **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Mediante el editor vi, cree un archivo llamado `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Entre en el modo de inserción, para lo que debe presionar la tecla de la letra `i`.

1. Pegue el siguiente código en el editor:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Presione la tecla  **&lt;Esc >** para salir del modo de inserción.

1. Guarde el archivo y salga del editor vi, para lo que debe escribir el siguiente comando y, después, presionar **&lt;Entrar>**:

    ```bash
    :wq
    ```

1. Inicialice Terraform.

    ```bash
    terraform init
    ```

1. Cree el plan de Terraform.

    ```bash
    terraform plan
    ```

1. Aprovisione los recursos definidos en el archivo de configuración `swap.tf`. (para confirmar la acción escriba `yes` en el aviso).

    ```bash
    terraform apply
    ```

1. Cuando Terraform haya acabado de intercambiar las ranuras, vuelva al explorador que representa la página web **slotAppService** y actualice la página. 

La aplicación web del espacio de ensayo **slotAppServiceSlotOne** se ha intercambiado por el espacio de producción y ahora se representa en verde. 

![Las ranuras de implementación se han intercambiado](./media/terraform-slot-walkthru/slots-swapped.png)

Para volver a la versión de producción original de la aplicación, vuelva a aplicar el plan de Terraform creado a partir del archivo de configuración `swap.tf`.

```bash
terraform apply
```

Tras el intercambio se ve la configuración original.