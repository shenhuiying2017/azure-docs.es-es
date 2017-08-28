---
title: Aplicaciones administradas de Azure en Marketplace | Microsoft Docs
description: "En este artículo se describen las aplicaciones administradas de Azure que están disponibles a través de Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.contentlocale: es-es
ms.lasthandoff: 08/11/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Aplicaciones administradas de Azure en Marketplace

 Los MSP, ISV e integradores de sistemas (SI) pueden usar las aplicaciones administradas de Azure para ofrecer sus soluciones a todos los clientes de Azure Marketplace. Estas soluciones reducen la sobrecarga de mantenimiento para los clientes. Los publicadores pueden vender la infraestructura y el software a través de Marketplace. Pueden asociar los servicios y el soporte técnico operativo a las aplicaciones administradas. Para más información, consulte [Información general sobre las aplicaciones administradas](managed-application-overview.md).

En este artículo se explica cómo un MSP, un ISV o un SI pueden publicar una aplicación en Marketplace y ponerla a disposición de los clientes.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Requisitos previos para publicar una aplicación administrada

Requisitos previos para publicar en Marketplace:

* Requisitos previos técnicos

    *  Para obtener información sobre la estructura y la sintaxis básicas de las plantillas de Resource Manager, consulte [Plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
    *  Para ver soluciones de plantilla completas, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/en-us/documentation/templates/) o el [repositorio de plantillas de inicio rápido](https://github.com/azure/azure-quickstart-templates).
    *  Para obtener información sobre cómo crear la interfaz para los clientes que implementan la aplicación a través de Marketplace, consulte el artículo sobre la [creación de un archivo de definición de interfaz de usuario](managed-application-createuidefinition-overview.md).

* Requisitos previos no técnicos (empresariales)

    *   Su empresa o sus subsidiarias deben encontrarse en uno de los países donde las ventas se permiten en Marketplace.
    *   El producto debe contar con una licencia que sea compatible con los modelos de facturación admitidos por Marketplace.
    *   Usted es el responsable de poner el soporte técnico a disposición de los clientes de forma comercialmente razonable. Puede ser gratuito, de pago o a través del soporte técnico de la comunidad.
    *   Asimismo, es responsable de la concesión de licencias para su software y las dependencias de software de terceros.
    *   Debe proporcionar contenido que cumpla los criterios para que su oferta se publique en Marketplace y en Azure Portal.
    *   Debe aceptar los términos del acuerdo del publicador y de las directivas de participación de Azure Marketplace.
    *   También debe aceptar los Términos de uso, la Declaración de privacidad de Microsoft y el Contrato del programa Microsoft Azure Certified.

## <a name="create-a-new-azure-application-offer"></a>Creación de una nueva oferta de aplicación de Azure

Después de cumplir los requisitos previos, está listo para crear la oferta de la aplicación administrada. Hagamos un repaso rápido a los conceptos de oferta y de SKU.

### <a name="offer"></a>Oferta

Una oferta de aplicación administrada se corresponde con una clase de oferta de producto de un publicador. Si tiene un nuevo tipo de solución o aplicación que quiere que esté disponible en Marketplace, puede configurarla como una nueva oferta. Una oferta es una colección de SKU. Cada oferta aparece como su propia entidad en Marketplace.

### <a name="sku"></a>SKU

Una SKU es la unidad de compra más pequeña de una oferta. Puede usar una SKU dentro de la misma clase de producto (oferta) para diferenciar entre:

* Distintas características que se admiten.
* Si la oferta es administrada o no administrada.
* Los modelos de facturación que se admiten.

La SKU aparece debajo de la oferta principal en Marketplace. como una entidad de compra propia en Azure Portal.

### <a name="set-up-an-offer"></a>Configuración de una oferta

1. Inicie sesión en [Cloud Partner Portal](https://cloudpartner.azure.com/).

2. En el panel de navegación de la izquierda, seleccione **+ Oferta nueva** > **Aplicaciones de Azure**.

    ![Nueva oferta](./media/managed-application-author-marketplace/newOffer.png)

3. Rellene los formularios que aparecen a la izquierda en la vista **Editor**. Los campos obligatorios están marcados con un asterisco rojo (*).

    ![Configuración de oferta](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    Para crear una aplicación administrada se usan cuatro formularios básicos:

    a. Configuración de oferta

    b. SKU

    c. Marketplace

    d. Soporte técnico

Estos formularios se describen con más detalle en las secciones siguientes.

## <a name="offer-settings-form"></a>Formulario de configuración de oferta
Utilice este formulario básico para especificar la configuración de la oferta.

1. Rellene el formulario **Configuración de oferta**. Los diferentes campos son:

    a. **Id. de oferta**: se trata del identificador único que identifica la oferta en un perfil del publicador. Este identificador se muestra en las direcciones URL de producto, las plantillas de Resource Manager y los informes de facturación. Puede contener solo caracteres alfanuméricos en minúscula o guiones (-). El identificador no puede terminar con un guión. Está limitado a un máximo de 50 caracteres. En cuanto se lanza una oferta, este campo se bloquea.

    b. **Id. de publicador**: use esta lista desplegable para elegir el perfil del publicador en el que quiere publicar esta oferta. En cuanto se lanza una oferta, este campo se bloquea.

    c. **Nombre**: este nombre para mostrar para su oferta aparece en Marketplace y en el portal. Puede tener un máximo de 50 caracteres. Incluya un nombre de marca que identifique el producto. No incluya aquí el nombre de su empresa a menos que sea así como se comercializa. Si comercializa esta oferta en su propio sitio web, asegúrese de que el nombre sea exactamente el mismo que aparece en él.

2. Seleccione **Guardar** para guardar lo que ha hecho hasta ahora. 

## <a name="skus-form"></a>Formulario de SKU
El siguiente paso consiste en agregar SKU para la oferta.

1. Seleccione **SKU** > **SKU nueva**. 

    ![Selección de SKU nueva](./media/managed-application-author-marketplace/newOffer_skus.png)

2. Escriba un **Identificador de SKU**. Se trata del identificador único de la SKU dentro de una oferta. Este identificador se muestra en las direcciones URL de producto, las plantillas de Resource Manager y los informes de facturación. Puede contener solo caracteres alfanuméricos en minúscula o guiones (-). El identificador, que tendrá 50 caracteres como máximo, no puede terminar con un guion. En cuanto se lanza una oferta, este campo se bloquea. Puede tener varias SKU dentro de una oferta. Necesita una SKU para cada imagen que pretenda publicar.

3. Rellene la sección de **detalles de la SKU** en el formulario siguiente:

    ![Proporcionar una nueva SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

    Rellene los siguientes campos:
    
    a. **Título**: escriba un título para esta SKU. Este título aparece en la galería para este elemento.

    b. **Resumen**: proporcione un breve resumen de esta SKU. Este texto aparece debajo del título.

    c. **Descripción**: escriba una descripción detallada de la SKU.

    d. **Tipo de SKU**: los valores permitidos son **Aplicación administrada** y **Plantillas de solución**. En este caso, seleccione **Aplicación administrada**.

4. Rellene la sección de **detalles del paquete** en el formulario siguiente:

    ![Paquete](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    Rellene los siguientes campos:

    a. **Versión actual**: escriba una versión para el paquete que vaya a cargar. Debe tener este formato: `{number}.{number}.{number}{number}`.

    b. **Seleccione un archivo de paquete**: este paquete contiene los siguientes archivos, comprimidos en un archivo ZIP:
    * **applianceMainTemplate.json**: archivo de plantilla de implementación que se usa para implementar la solución o la aplicación. Para obtener información sobre cómo crear archivos de plantilla de implementación en [Creación de la primera plantilla de Azure Resource Manager](resource-manager-create-first-template.md).
    * **appliancecreateUIDefinition.json**: Azure Portal usa este archivo para generar la interfaz de usuario que se emplea para el aprovisionamiento de esta solución o aplicación. Para más información, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
    * **mainTemplate.json**: este archivo de plantilla solo contiene el recurso Microsoft.Solution/appliances. En el archivo mainTemplate se incluyen las siguientes propiedades:

        *  **kind**: use **Marketplace** para las aplicaciones administradas en Marketplace.
        *  **ManagedResourceGroupId**: este grupo de recursos de la suscripción del cliente es donde se implementan todos los recursos definidos en applianceMainTemplate.json.
        *  **PublisherPackageId**: esta cadena identifica el paquete de forma única. Especifique el valor con el formato `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.

Obtenga el **identificador de oferta** y el **identificador de publicador** desde el portal de publicación, como se muestra en la siguiente imagen:

![Id. de oferta](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
Obtenga el **identificador de SKU** tal como se muestra en la imagen siguiente:

![Identificador de SKU](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
Obtenga la **versión** del paquete tal como se muestra en la imagen siguiente:

![Versión del paquete](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  Según los ejemplos anteriores, el valor de **PublisherPackageId** es `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`.

  Ejemplo de mainTemplate.json:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

Este paquete debe contener todos los demás scripts o plantillas anidadas que sean necesarios para aprovisionar correctamente esta aplicación. Los archivos mainTemplate.json, applianceMainTemplate.json y applianceCreateUIDefinition.json deben estar presentes en la carpeta raíz.

* **Authorizations**: esta propiedad define quién obtiene acceso a qué nivel de acceso en los recursos de las suscripciones de los clientes. El publicador puede usarla para administrar la aplicación en nombre del cliente.
* **PrincipalId**: esta propiedad es el identificador de Azure Active Directory de un usuario, de un grupo de usuarios o de una aplicación al que se han concedido determinados permisos en los recursos de la suscripción de los clientes. La definición de rol describe los permisos. 
* **Role Definition**: esta propiedad es una lista de todos los roles de control de acceso basado en roles (RBAC) integrados que proporciona Azure AD. Puede seleccionar el rol que resulte más adecuado para administrar los recursos en nombre del cliente.

Puede agregar varias autorizaciones. Se recomienda que cree un grupo de usuarios de AD y especifique su identificador en **PrincipalId**. De esta manera, puede agregar más usuarios al grupo de usuarios sin necesidad de actualizar la SKU.

Para más información sobre RBAC, consulte [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulario de Marketplace

En el formulario de Marketplace se solicitan campos que aparecen en [Azure Marketplace](https://azuremarketplace.microsoft.com) y en [Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Id. de suscripción de versión preliminar

Escriba una lista de identificadores de suscripciones de Azure que puedan tener acceso a la oferta una vez publicada. Puede usar estas suscripciones de lista blanca para probar la versión preliminar de la oferta antes de activarla. Puede compilar una lista blanca de hasta 100 suscripciones en el portal de asociados.

### <a name="suggested-categories"></a>Categorías sugeridas

Seleccione hasta cinco categorías de la lista con las que la oferta pueda asociarse mejor. Estas categorías se usan para asignar la oferta a las categorías de productos disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com) y en [Azure Portal](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

En el resumen de la aplicación administrada, se muestran los campos siguientes:

![Resumen de Marketplace](./media/managed-application-author-marketplace/publishvm10.png)

La pestaña **Información general** de la aplicación administrada muestra los campos siguientes:

![Introducción a Marketplace](./media/managed-application-author-marketplace/publishvm11.png)

La pestaña **Planes y precios** de la aplicación administrada muestra los campos siguientes:

![Planes de Marketplace](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

En el resumen de la aplicación administrada, se muestran los campos siguientes:

![Resumen del portal](./media/managed-application-author-marketplace/publishvm12.png)

La información general de la aplicación administrada muestra los campos siguientes:

![Información general de Azure Portal](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Directrices para logotipos

Siga estas directrices para cualquier logotipo que cargue en Cloud Partner Portal:

*   El diseño de Azure tiene una paleta de colores simple. Utilice pocos colores primarios y secundarios en el logotipo.
*   Los colores del tema del portal son el blanco y el negro. No use estos colores como fondo de los logotipos. Use un color que haga que su logotipo destaque en el portal. Nosotros recomendamos usar colores primarios simples. *Si usa un fondo transparente, asegúrese de que el logotipo y el texto no son blancos, negros ni azules.*
*   No utilice un fondo degradado en el logotipo.
*   No coloque texto en el logotipo, ni siquiera el nombre de su empresa o de la marca. La apariencia del logotipo debe ser homogénea y evitar degradados.
*   Asegúrese de que el logotipo no quede estirado.

#### <a name="hero-logo"></a>Logotipo de imagen prominente

El logotipo de imagen prominente es opcional. El publicador puede decidir no cargar un logotipo de imagen prominente. Una vez cargado el icono de imagen prominente, no se puede eliminar. A partir de ese momento, el asociado debe seguir las instrucciones de Marketplace para los iconos de imágenes prominentes.

Siga estas instrucciones para el icono de imagen prominente:

*   El nombre para mostrar del publicador, el título del plan y el resumen largo de la oferta se muestran en color blanco. Por lo tanto, no use un color claro para el fondo del icono de imagen prominente. Los fondos transparentes y de color negro o blanco no pueden utilizarse en los iconos de imagen prominente.
*   Una vez publicada la oferta, el nombre para mostrar del publicador, el título del plan, el resumen largo de la oferta y el botón **Crear** se incrustan mediante programación dentro del logotipo de imagen prominente. Por lo tanto, no escriba ningún texto mientras diseña el logotipo de imagen prominente. Dado que el texto se incluye mediante programación en ese espacio, deje espacios en blanco a la derecha. El espacio en blanco para el texto debe ser de 415 x 100 píxeles en la derecha. Se ve compensado por 370 píxeles desde la izquierda.

    ![Ejemplo de logotipo de imagen prominente](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Formulario de soporte técnico

Rellene el formulario **Soporte técnico** con los contactos de soporte técnico de su empresa. Esta información podría contener contactos de ingeniería y contactos de soporte técnico al cliente.

## <a name="publish-an-offer"></a>Publicación de una oferta

Después de rellenar todas las secciones, seleccione **Publicar** para comenzar el proceso que hace que la oferta esté a disposición de los clientes.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](managed-application-overview.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
* Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).

