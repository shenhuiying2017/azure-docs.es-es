---
description: archivo de inclusión
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 552e93e9bd1b17c73fb1638fbae2ac30b051c261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
1. En el explorador, abra la [imagen de Azure Marketplace para Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Seleccionar **Obtenerla ahora**.

    ![Seleccione Obtenerla ahora para iniciar el proceso de instalación de la imagen de Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Después de revisar la información acerca de los precios, términos y detalles, seleccione **Continuar**.

    ![Información acerca de los precios y términos de la imagen de Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Seleccione **Crear** para configurar el servidor de Jenkins en Azure Portal. 

    ![Instale la imagen de Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. En la pestaña **Aspectos básicos**, especifique los siguientes valores:

    - **Nombre**: escriba `Jenkins`.
    - **Nombre de usuario**: escriba el nombre de usuario que se usa al iniciar sesión en la máquina virtual en la que se ejecuta Jenkins. El nombre de usuario debe cumplir [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
    - En **Tipo de autenticación**, seleccione **Clave pública SSH**.
    - **Clave pública SSH**: copie y pegue una clave pública RSA en formato de una sola línea (que comience con `ssh-rsa`) o formato PEM de varias líneas. Puede generar claves SSH mediante ssh-keygen en Linux y macOS, o PuTTYGen en Windows. Para más información sobre las claves SSH y Azure, consulte el artículo [Uso de claves SSH con Windows en Azure](/azure/virtual-machines/linux/ssh-from-windows).
    - **Suscripción**: seleccione la suscripción de Azure en la que quiere instalar Jenkins.
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos que actúa como contenedor lógico para la colección de recursos que componen la instalación Jenkins.
    - **Ubicación**: seleccione **Este de EE. UU.**.

    ![Escriba la información del grupo de recursos y la autenticación de Jenkins en la pestaña Aspectos básicos.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Seleccione **Aceptar** para continuar a la pestaña **Configuración adicional**. 

1. En la pestaña **Configuración adicional**, especifique los siguientes valores:

    - **Tamaño**: seleccione la opción de tamaño correcta para la máquina virtual de Jenkins.
    - **Tipo de disco de la máquina virtual**: especifique cualquier HDD (unidad de disco duro) o SSD (unidad de estado sólido) para indicar qué tipo de disco de almacenamiento se permite para la máquina virtual de Jenkins.
    - **Red virtual** (opcional): seleccione **Red virtual** para modificar la configuración predeterminada.
    - **Subredes**: seleccione **Subredes**, compruebe la información y seleccione **Aceptar**.
    - **Dirección IP pública**: por defecto, el nombre de la dirección IP es el nombre de Jenkins que especificó en la página anterior con el sufijo -IP. Puede seleccionar la opción para cambiar este comportamiento predeterminado.
    - **Etiqueta de nombre de dominio**: especifique el valor para la dirección URL completa a la máquina virtual de Jenkins.
    - **Jenkins release type** (Tipo de versión de Jenkins): seleccione el tipo de versión deseado entre las opciones: `LTS`, `Weekly build` o `Azure Verified`. Las opciones `LTS` y `Weekly build` se explican en el artículo [Jenkins LTS Release Line](https://jenkins.io/download/lts/) (Línea de versiones de Jenkins LTS). La opción `Azure Verified` hace referencia a una [versión de Jenkins LTS](https://jenkins.io/download/lts/) que se ha verificado que funciona en Azure. 

    ![Especifique la configuración de la máquina virtual para Jenkins en la pestaña Configuración.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Seleccione **Aceptar** para continuar con la pestaña **Configuración de integración**.

1. En la pestaña **Configuración de integración**, especifique los siguientes valores:

    - **Entidad de servicio**: la entidad de servicio se agrega a Jenkins como credencial para la autenticación con Azure. `Auto` significa que MSI (Managed Service Identity) creará la entidad de seguridad. `Manual` significa que usted debe crear la entidad de seguridad. 
        - **Id. de aplicación** y **Secreto**: si selecciona la opción `Manual` en **Entidad de servicio**, deberá especificar los valores de `Application ID` y `Secret` de la entidad de servicio. Al [crear una entidad de servicio](/cli/azure/create-an-azure-service-principal-azure-cli), observe que el rol predeterminado es **Colaborador**, que es suficiente para trabajar con recursos de Azure.
    - **Enable Cloud Agents** (Habilitar agentes en la nube): especifique la plantilla de nube predeterminada para los agentes, donde `ACI` hace referencia a Azure Container Instance y `VM` a las máquinas virtuales. También puede especificar `No` si no desea volver a habilitar un agente en la nube.

1. Seleccione **Aceptar** para continuar a la pestaña **Resumen**.

1. Cuando se muestra la pestaña **Resumen**, se valida la información especificada. Cuando vea el mensaje de **validación superada** (en la parte superior de la pestaña), seleccione **Aceptar**. 

    ![La pestaña Resumen muestra y valida las opciones seleccionadas.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Cuando se muestre la pestaña **Crear**, seleccione **Crear** para crear la máquina virtual de Jenkins. Cuando el servidor esté listo, se mostrará una notificación en Azure Portal.

    ![Notificación de que Jenkins está listo.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)