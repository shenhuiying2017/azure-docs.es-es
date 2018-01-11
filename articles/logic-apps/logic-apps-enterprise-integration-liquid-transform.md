---
title: "Conversión de datos JSON con transformaciones Liquid: Azure Logic Apps | Microsoft Docs"
description: Cree transformaciones o asignaciones para transformaciones JSON avanzadas mediante Logic Apps y una plantilla Liquid.
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Transformaciones JSON avanzadas con una plantilla Liquid
Azure Logic Apps admite transformaciones JSON básicas a través de acciones de operación de datos nativas como escribir o analizar JSON. Ahora, Logic Apps también admite transformaciones JSON avanzadas con plantillas Liquid. [Liquid](https://shopify.github.io/liquid/) es un lenguaje de plantillas de código abierto para aplicaciones web flexibles.
 
En este artículo, aprenderá a usar una asignación o plantilla Liquid, que puede admitir transformaciones JSON más complejas, como iteraciones, flujos de control, variables, etc. Debe definir una asignación de JSON a JSON con esta asignación Liquid y almacenar esa asignación en su cuenta de integración para poder realizar una transformación Liquid en la aplicación lógica.

## <a name="prerequisites"></a>Requisitos previos
Estos son los requisitos previos para usar la acción Liquid:

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede [registrarse para obtener una suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/logic-apps-create-a-logic-app.md).

* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) básica.


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Crear y agregar una plantilla o asignación Liquid a la cuenta de integración

1. Cree la plantilla Liquid de muestra para este ejemplo. La plantilla Liquid define cómo transformar la entrada JSON como se describe aquí:

   ```
   {%- assign deviceList = content.devices | Split: ', ' -%}
    {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
    }
    ```
    > [!NOTE]
    > Si la plantilla Liquid usa algún [filtro](https://shopify.github.io/liquid/basics/introduction/#filters), esos filtros deben comenzar con mayúsculas. 

2. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

3. En el menú principal de Azure, elija **Todos los recursos**. 

4. En el cuadro de búsqueda, proporcione la cuenta de integración. Seleccione su cuenta.

   ![Seleccionar cuenta de integración](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  En el icono de cuenta de integración, seleccione **Asignaciones**.

   ![Seleccionar asignaciones](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Elija **Agregar** y proporcione estos detalles para la asignación:
  * **Nombre**: nombre de la asignación, que es "JsontoJsonTemplate" en este ejemplo.
  * **Tipo de asignación**: tipo de la asignación. Para la transformación de JSON a JSON, debe seleccionar **liquid**.
  * **Asignación**: un archivo de asignación o plantilla Liquid existente para usarlo para la transformación, que es "SimpleJsonToJsonTemplate.liquid" en este ejemplo. Puede usar el selector de archivos para buscar el archivo.

    ![Agregar la plantilla Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>Agregar la acción Liquid para transformar JSON en la aplicación lógica

1. [Creación de una aplicación lógica](logic-apps-create-a-logic-app.md).

2. Agregue el [desencadenador de solicitud](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) a su aplicación lógica.

3. Elija **+ Nuevo paso > Agregar una acción**. Busque *liquid* en el cuadro de búsqueda. Seleccione **Liquid - Transformar de JSON a JSON**.

  ![Buscar acción Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. En el cuadro **Contenido**, seleccione **Cuerpo** en la lista de contenido dinámico o parámetros (la que aparezca). 
  
  ![Seleccionar Cuerpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Desde la lista desplegable **Asignación**, seleccione la plantilla Liquid, que, en este ejemplo, es JsonToJsonTemplate.

  ![Seleccionar asignación](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Si la lista está vacía, probablemente, la aplicación lógica no está vinculada a la cuenta de integración. Para vincular la aplicación lógica a la cuenta de integración que tiene la plantilla o asignación Liquid, siga estos pasos:

   1. En el menú de la aplicación lógica, seleccione **Configuración del flujo de trabajo**. 
   2. En la lista **Seleccione una cuenta de integración**, seleccione su cuenta de integración y elija **Guardar**.

     ![Vincular la aplicación lógica a la cuenta de integración](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>Comprobación de la aplicación lógica

   Publique la entrada JSON en la aplicación lógica desde [Postman](https://www.getpostman.com/postman) u otra herramienta similar. La salida JSON transformada de la aplicación lógica tiene este aspecto:
  
   ![Salida de ejemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  
* [Más información sobre las asignaciones](../logic-apps/logic-apps-enterprise-integration-maps.md "Información sobre las asignaciones de Enterprise Integration")  

