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
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Aplicaciones administradas de Azure en Marketplace

Como se indica en el artículo [Introducción a las aplicaciones administradas](managed-application-overview.md), las aplicaciones administradas en Microsoft Azure Marketplace permiten que los proveedores de servicios administrados (MSP), los proveedores de software independientes (ISV) y los integradores de sistemas (SI) ofrezcan sus soluciones a todos los clientes de Azure. Estas soluciones reducen la sobrecarga de mantenimiento para los clientes. Los publicadores pueden vender infraestructura y software a través de Marketplace y vincularles servicios y asistencia operativa. 

En este artículo se explica cómo un MSP, un ISV o un SI pueden publicar una aplicación en Marketplace y ponerla a disposición de los clientes.  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>Requisitos previos para publicar una aplicación administrada

Requisitos previos para publicar en Marketplace

* Requisitos previos técnicos

    *  Para obtener información sobre la estructura y la sintaxis básicas de las plantillas de Resource Manager, vea el artículo sobre la [creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
    *  Para ver soluciones de plantilla completas, vea [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/en-us/documentation/templates/) o el [repositorio de plantillas de inicio rápido](https://github.com/azure/azure-quickstart-templates).
    *  Para obtener información sobre la creación de la interfaz para los clientes que implementan la aplicación a través de Marketplace, vea el artículo sobre la [creación de un archivo de definición de interfaz de usuario](managed-application-createuidefinition-overview.md).

* Requisitos previos no técnicos (empresariales)

    *   La empresa o sus subsidiarias deben encontrarse en uno de los países de origen de venta admitidos por Marketplace.
    *   El producto debe contar con una licencia que sea compatible con los modelos de facturación admitidos por Marketplace.
    *   Usted es el responsable de poner el soporte técnico a disposición de los clientes de forma comercialmente razonable. Puede ser gratuito, de pago o a través del soporte técnico de la comunidad.
    *   Asimismo, es responsable de la concesión de licencias para su software y las dependencias de software de terceros.
    *   Debe proporcionar contenido que cumpla los criterios para que su oferta se publique en Azure Marketplace y en Azure Portal.
    *   Debe aceptar los términos del acuerdo del publicador y de las directivas de participación de Azure Marketplace.
    *   También debe aceptar los Términos de uso, la Declaración de privacidad de Microsoft y el Contrato del programa Microsoft Azure Certified.

## <a name="how-to-create-a-new-azure-application-offer"></a>Cómo crear una oferta de aplicación de Azure

Una vez que haya cumplido los requisitos previos, estará listo para empezar a crear una oferta de aplicación administrada. Hagamos un repaso rápido a los conceptos de oferta y SKU.

### <a name="offer"></a>Oferta

Una oferta de aplicación administrada se corresponde con una clase de oferta de producto de un publicador. Si tiene un nuevo tipo de solución o aplicación que quiere que esté disponible en Marketplace, puede configurarla como una nueva oferta. Una oferta es una colección de SKU. Cada oferta aparece como su propia entidad en Marketplace.

### <a name="sku"></a>SKU

Una SKU es la unidad de compra más pequeña de una oferta. Dentro de la misma clase de producto (oferta), las SKU permiten distinguir entre las diferentes características admitidas, independientemente de si la oferta es administrada o no administrada y de los modelos de facturación admitidos.

La SKU aparece debajo de la oferta principal de Marketplace como una entidad de compra propia en Azure Portal.

### <a name="set-up-offer"></a>Configurar la oferta

1.  Inicie sesión en [Cloud Partner Portal](https://cloudpartner.azure.com/).
2.  En la barra de navegación izquierda, haga clic en **+ Nueva oferta** y seleccione **Aplicaciones de Azure**.

    ![Nueva oferta](./media/managed-application-author-marketplace/newOffer.png)

3.  Ahora se abre una vista del **Editor** para una nueva oferta, donde puede empezar a crearla.

    ![Configuración de oferta](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  Los formularios que se tienen que rellenar aparecen a la izquierda en la vista del **Editor**. Cada formulario consta de un conjunto de campos que hay que rellenar. Los campos obligatorios están marcados con un asterisco rojo (*).

 **Hay cuatro formularios principales para crear una aplicación administrada.**

    *   Configuración de oferta
    *   SKU
    *   Marketplace
    *   Soporte técnico

Estos formularios se describen con más detalle en las secciones siguientes.

## <a name="offer-settings-form"></a>Formulario de configuración de oferta

El formulario de configuración de oferta es un formulario básico en el que se especifica la configuración de la oferta. Los diferentes campos son:

* Id. de oferta: se trata de un identificador único de la oferta en un perfil del publicador. Este identificador se muestra en las direcciones URL de producto, las plantillas de Resource Manager y los informes de facturación. Puede contener solo caracteres alfanuméricos en minúscula o guiones (-). El identificador, que tendrá 50 caracteres como máximo, no puede terminar con un guion. Este campo queda bloqueado en cuanto se lanza una oferta.
* Id. de publicador: esta lista desplegable permite elegir el perfil del publicador en el que quiere publicar esta oferta. Este campo queda bloqueado en cuanto se lanza una oferta.
* Nombre: se trata del nombre para mostrar de la oferta. Es el nombre que se muestra en Marketplace y en el portal. Puede tener un máximo de 50 caracteres. Aquí se recomienda incluir un nombre de marca que identifique el producto. No incluya aquí el nombre de su empresa a menos sea así como se comercializa. Si comercializa esta oferta en su propio sitio web, asegúrese de que el nombre sea exactamente el mismo que aparece en el sitio web.

Seleccione **Guardar** para guardar lo que ha hecho hasta ahora. El siguiente paso consiste en agregar SKU para la oferta.

## <a name="skus-form"></a>Formulario de SKU

Seleccione el formulario **SKU**. Aquí puede ver la opción para crear una **Nueva SKU**. Al seleccionar esta opción, puede especificar un **identificador de SKU**.

![Seleccionar nueva SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

El **identificador de SKU** es el identificador único de la SKU dentro de una oferta. Este identificador se muestra en las direcciones URL de producto, las plantillas de Resource Manager y los informes de facturación. Puede contener solo caracteres alfanuméricos en minúscula o guiones (-). El identificador, que tendrá 50 caracteres como máximo, no puede terminar con un guion. Este campo queda bloqueado en cuanto se lanza una oferta. Puede tener varias SKU dentro de una oferta. Necesita una SKU para cada imagen que pretende publicar.

Después de seleccionar **Nueva SKU**, debe rellenar el formulario siguiente:

![Proporcionar nueva SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Cómo rellenar la sección de detalles de la SKU

* Título: dé un título a esta SKU. Esto es lo que se muestra en la galería para este elemento.
* Resumen: proporcione un breve resumen de esta SKU. Este texto aparece justo debajo del título.
* Descripción: proporcione una descripción detallada de la SKU.
* Tipo de SKU: los valores permitidos son **Aplicación administrada** y **Plantillas de solución**. En este caso, seleccione **Aplicación administrada**.

### <a name="how-to-fill-package-details-section"></a>Cómo rellenar la sección de detalles del paquete

En la sección del paquete debe rellenar los campos que se indican a continuación.

![Paquete](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**Versión actual**: proporcione una versión para el paquete que vaya a cargar. Debe tener este formato: `{number}.{number}.{number}{number}`.

**Archivo de paquete**: este paquete contiene los siguientes archivos, comprimidos en un archivo ZIP.

* **applianceMainTemplate.json**: archivo de plantilla de implementación que se usa para implementar la solución o la aplicación. Encontrará más detalles sobre cómo crear archivos de plantilla de implementación en [Creación de la primera plantilla de Azure Resource Manager](resource-manager-create-first-template.md).
* **appliancecreateUIDefinition.json**: Azure Portal usa este archivo para generar la interfaz de usuario para el aprovisionamiento de esta solución o aplicación. Para obtener más información, vea [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* **mainTemplate.json**: archivo de plantilla que solo contiene el recurso Microsoft.Solution/appliances. A continuación se indican las principales propiedades de este recurso que debe tener en cuenta.

En mainTemplate se incluyen las siguientes propiedades:

*  kind: use **Marketplace** para las aplicaciones administradas en Marketplace.
*  ManagedResourceGroupId: grupo de recursos de la suscripción del cliente donde se implementan todos los recursos definidos en applianceMainTemplate.json.
*  PublisherPackageId: cadena que identifica el paquete de forma única. Especifique el valor con el formato `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.
  Puede obtener en el portal de publicación los valores de publisherId y OfferId.

  ![Identificador de oferta](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  Puede obtener el identificador de SKU tal como se muestra en la imagen siguiente:

  ![Identificador de SKU](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  Puede obtener la versión del paquete tal como se muestra en la imagen siguiente:

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

**Authorizations**: esta propiedad define quién obtiene acceso a qué nivel de acceso en los recursos de las suscripciones de los clientes. Permite que el publicador administre la aplicación en nombre del cliente.

* PrincipalId: esta propiedad es el identificador de Azure Active Directory de un usuario, de un grupo de usuarios o de una aplicación al que se han concedido determinados permisos (como se describe en la definición de roles) en los recursos de la suscripción de los clientes.
* Role Definition: esta propiedad es una lista de todos los roles RBAC integrados que proporciona Azure AD. Puede seleccionar el rol que resulte más adecuado y que le permita administrar los recursos en nombre del cliente.

Se pueden agregar varias autorizaciones. Aun así, se recomienda crear un grupo de usuarios de Active Directory y especificar su identificador en **PrincipalId**. Permite agregar más usuarios al grupo de usuarios sin necesidad de actualizar la SKU.

Para obtener más información sobre RBAC, vea [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulario de Marketplace

En el formulario de Marketplace se solicitan campos que aparecen en [Azure Marketplace](https://azuremarketplace.microsoft.com) y en [Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Id. de suscripción de versión preliminar

Escriba aquí una lista de identificadores de suscripciones de Azure a los que le gustaría conceder acceso a la oferta una vez publicada. Estas suscripciones permitidas le dan la posibilidad de probar la oferta en versión preliminar antes de publicarla. En el portal de asociados puede habilitar como permitidas hasta 100 suscripciones.

### <a name="suggested-categories"></a>Categorías sugeridas

Seleccione hasta cinco categorías de la lista proporcionada con las que la oferta puede asociarse mejor. Las categorías seleccionadas se usarán para asignar la oferta a las categorías de productos disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com) y el [portal](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

En el resumen de la aplicación administrada, se muestran los campos siguientes:

![Resumen de Marketplace](./media/managed-application-author-marketplace/publishvm10.png)

En la información general de la aplicación administrada, se muestran los campos siguientes:

![Información general de Marketplace](./media/managed-application-author-marketplace/publishvm11.png)

En los planes y precios de la aplicación administrada, se muestran los campos siguientes:

![Planes de Marketplace](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Portal de Azure

En el resumen de la aplicación administrada, se muestran los campos siguientes:

![Resumen del portal](./media/managed-application-author-marketplace/publishvm12.png)

En la información general de la aplicación administrada, se muestran los campos siguientes:

![Información general del portal](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Directrices para logotipos

Siga estas directrices para todos los logotipos cargados en Cloud Partner Portal:

*   El diseño de Azure tiene una paleta de colores simple. Utilice pocos colores primarios y secundarios en el logotipo.
*   Los colores del tema del portal son el blanco y el negro. Por lo tanto, evite emplear estos colores como fondo de los logotipos. Use un color que haga que los logotipos destaquen en el portal. Nosotros recomendamos usar colores primarios simples. **Si utiliza un fondo transparente, asegúrese de que el texto y los logotipos no sean de color azul, blanco o negro.**
*   No utilice un fondo degradado en el logotipo.
*   Evite colocar texto, incluso la empresa o el nombre de marca, en el logotipo. El aspecto del logotipo debe ser "plano" y debe evitar degradados.
*   Asegúrese de que el logotipo no quede estirado.

#### <a name="hero-logo"></a>Logotipo de imagen prominente

El logotipo de imagen prominente es opcional. El publicador puede decidir no cargar un logotipo de imagen prominente. Sin embargo, una vez que cargue la imagen prominente, esta no se puede eliminar. A partir de ese momento, el asociado debe seguir las instrucciones de Marketplace para imágenes prominentes.

##### <a name="guidelines-for-the-hero-logo-icon"></a>Instrucciones para el logotipo de imagen prominente

*   El nombre para mostrar del publicador, el título del plan y el resumen largo de la oferta se muestran en color blanco. Por lo tanto, debe evitar usar colores claros en el fondo de la imagen prominente. Los fondos transparentes y de color negro y blanco no pueden usarse en las imágenes prominentes.
*   El nombre para mostrar del publicador, el título del plan, el resumen largo de la oferta y el botón Crear se incrustan mediante programación dentro del logotipo de imagen prominente cuando se publica la oferta. Por lo tanto, no hace falta especificarlos cuando diseñe el logotipo de imagen prominente. Deje espacio vacío a la derecha, ya que es ahí donde incluimos mediante programación el texto (es decir, el nombre para mostrar del publicador, el título del plan y el resumen largo de la oferta). El espacio vacío para el texto debe tener un tamaño de 415 x 100 a la derecha (con un desplazamiento de 370 píxeles a la izquierda).

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Formulario de soporte técnico

El siguiente formulario que debe rellenar es el de soporte técnico. En este formulario se solicitan los contactos de soporte técnico de la compañía, como la información de contacto de ingeniería y la información de contacto de asistencia al cliente.

## <a name="how-to-publish-an-offer"></a>Publicación de una oferta

Después de completar todas las secciones, seleccione **Publicar** para que la oferta esté a disposición de los clientes.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](managed-application-overview.md).
* Para obtener información sobre cómo usar una aplicación administrada de Marketplace, vea [Consume Azure managed applications in the Marketplace](managed-application-consume-marketplace.md) (Usar aplicaciones administradas de Azure en Marketplace).
* Para obtener información sobre cómo publicar una aplicación administrada del catálogo de servicios, vea [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para obtener información sobre cómo usar una aplicación administrada del catálogo de servicios, vea [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).

