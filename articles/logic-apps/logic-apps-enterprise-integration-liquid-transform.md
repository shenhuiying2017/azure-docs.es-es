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
ms.openlocfilehash: 4bb003afd757faac675a9af8599a781247717a64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Realice transformaciones JSON avanzadas con una plantilla Liquid

Azure Logic Apps admite transformaciones JSON básicas a través de acciones de operación de datos nativas como **escribir** o **analizar JSON**. Para las transformaciones JSON avanzadas, puede usar las plantillas Liquid con las aplicaciones lógicas. 
[Liquid](https://shopify.github.io/liquid/) es un lenguaje de plantillas de código abierto para aplicaciones web flexibles.
 
En este artículo, aprenderá a usar una asignación o plantilla Liquid, que puede admitir transformaciones JSON más complejas, como iteraciones, flujos de control, variables, etc. Debe definir una asignación de JSON a JSON con una asignación Liquid y almacenar esa asignación en su cuenta de integración antes de poder realizar una transformación Liquid en la aplicación lógica.

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede [registrarse para obtener una suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) básica


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Creación de una plantilla o asignación Liquid para la cuenta de integración

1. Cree la plantilla Liquid de muestra para este ejemplo. La plantilla Liquid define cómo transformar la entrada JSON como se describe aquí:

   ``` json
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

2. Inicie sesión en el [Azure Portal](https://portal.azure.com).

3. En el menú principal de Azure, elija **Todos los recursos**. 

4. En el cuadro de búsqueda, busque y seleccione la cuenta de integración.

   ![Seleccionar cuenta de integración](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  En el icono de cuenta de integración, seleccione **Asignaciones**.

   ![Seleccionar asignaciones](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Elija **Agregar** y proporcione estos detalles para la asignación:

   * **Nombre**: nombre de la asignación, que es "JsontoJsonTemplate" en este ejemplo
   * **Tipo de asignación**: tipo de la asignación. Para la transformación de JSON a JSON, debe seleccionar **liquid**.
   * **Asignación**: un archivo de asignación o plantilla Liquid existente para usarlo para la transformación, que es "SimpleJsonToJsonTemplate.liquid" en este ejemplo. Puede usar el selector de archivos para buscar el archivo.

   ![Agregar la plantilla Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Agregar la acción Liquid a la transformación JSON

1. [Crear una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Agregue el [desencadenador de solicitud](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) a su aplicación lógica.

3. Elija **+ Nuevo paso > Agregar una acción**. En el cuadro de búsqueda, escriba *Liquid* y seleccione **Liquid: Transformar de JSON a JSON**.

   ![Búsqueda y selección de la acción Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. En el cuadro **Contenido**, seleccione **Cuerpo** en la lista de contenido dinámico o parámetros (la que aparezca).
  
   ![Selección de cuerpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Desde la lista **Asignación**, seleccione la plantilla Liquid, que, en este ejemplo, es "JsonToJsonTemplate".

   ![Selección de asignación](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Si la lista está vacía, probablemente, la aplicación lógica no está vinculada a la cuenta de integración. 
   Para vincular la aplicación lógica a la cuenta de integración que tiene la plantilla o asignación Liquid, siga estos pasos:

   1. En el menú de la aplicación lógica, seleccione **Configuración del flujo de trabajo**.
   2. En la lista **Seleccione una cuenta de integración**, seleccione su cuenta de integración y elija **Guardar**.

   ![Vincular la aplicación lógica a la cuenta de integración](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Comprobación de la aplicación lógica

Publique la entrada JSON en la aplicación lógica desde [Postman](https://www.getpostman.com/postman) u otra herramienta similar. La salida JSON transformada de la aplicación lógica tiene este aspecto:
  
![Salida de ejemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Más ejemplos de acciones Liquid
Liquid no está limitado únicamente a transformaciones JSON. Estos son otras acciones de transformación disponibles que utilizan Liquid.

* Transformación de JSON en texto
  
  Esta es la plantilla Liquid utilizada en este ejemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Estos son ejemplos de entrada y salida:
  
   ![Ejemplo de salida JSON a texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformación XML a JSON
  
  Esta es la plantilla Liquid utilizada en este ejemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Estos son ejemplos de entrada y salida:

   ![Ejemplo de salida XML a JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformación XML a texto
  
  Esta es la plantilla Liquid utilizada en este ejemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Estos son ejemplos de entrada y salida:

   ![Ejemplo de salida XML a texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>pasos siguientes

* [Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  
* [Más información sobre las asignaciones](../logic-apps/logic-apps-enterprise-integration-maps.md "Información sobre las asignaciones de Enterprise Integration")  

