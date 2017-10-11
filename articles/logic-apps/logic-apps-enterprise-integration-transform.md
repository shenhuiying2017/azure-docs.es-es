---
title: "Conversión de datos XML con transformaciones: Azure Logic Apps | Microsoft Docs"
description: "Crear transformaciones o mapas para convertir datos XML de un formato a otro en las aplicaciones lógicas mediante el SDK de Enterprise Integration"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fb6027769377b3527b11f7831dab3bb8d7061c84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="enterprise-integration-with-xml-transforms"></a>Integración de empresas con transformaciones XML
## <a name="overview"></a>Información general
El conector de transformación de integración de empresas convierte los datos de un formato a otro. Por ejemplo, puede darse el caso de que tenga un mensaje entrante en el que la fecha actual tenga el formato añoMesDía. Puede utilizar una transformación para cambiar el formato de la fecha a mesDíaAño.

## <a name="what-does-a-transform-do"></a>¿Para qué sirve una transformación?
Una Transformación, que se conoce también como asignación, está formada por un esquema XML de origen (la entrada) y un esquema XML de destino (la salida). Puede utilizar las diferentes funciones integradas para administrar o controlar los datos, incluidos aspectos como las manipulaciones de cadenas, las asignaciones condicionales, las expresiones aritméticas, los formateadores de tiempo y fecha e, incluso, las construcciones en bucle.

## <a name="how-to-create-a-transform"></a>¿Cómo se crea una transformación?
Puede crear una asignación o una transformación mediante el [SDK de integración de empresas](https://aka.ms/vsmapsandschemas)de Visual Studio. Cuando haya terminado de crear y probar la transformación, cargue la transformación en la cuenta de integración. 

## <a name="how-to-use-a-transform"></a>Procedimiento para utilizar una transformación
Cuando cargue la transformación o la asignación en la cuenta de integración, podrá emplearla para crear una aplicación lógica. Esta ejecuta las transformaciones siempre que se desencadena la aplicación lógica (y que haya contenido de entrada que deba transformarse).

**Estos son los pasos para utilizar una transformación**:

### <a name="prerequisites"></a>Requisitos previos

* Crear una cuenta de integración y agregarle una asignación.  

Ahora que ha tenido en cuenta los requisitos previos, tendrá que crear la Aplicación lógica:  

1. Cree una aplicación lógica y [vincúlela a la cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una aplicación lógica") que contenga la asignación.
2. Agregue un desencadenador de tipo **Solicitud** a su aplicación lógica.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Agregue la acción **Transform XML** (Transformar XML), pero seleccione antes **Agregar una acción**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Escriba la palabra *transform* en el cuadro de búsqueda para filtrar todas las acciones por la que desee usar.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Seleccione la acción **Transform XML** (Transformar XML)   
6. Agregue el **CONTENIDO** XML que vaya a transformar. Puede usar cualquier dato XML que reciba en la solicitud HTTP como valor de **CONTENIDO**. En este ejemplo, seleccione el cuerpo de la solicitud HTTP que desencadenó la Aplicación lógica.
7. Seleccione el nombre de la **ASIGNACIÓN** que quiera usar para realizar la transformación. La asignación ya debe estar en la cuenta de integración. En el paso anterior, ya concedió a la Aplicación lógica acceso a la cuenta de integración que contiene la asignación.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Guarde el trabajo  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

En este momento, ya ha terminado de configurar su asignación. En una aplicación real, puede almacenar los datos transformados en una aplicación LOB como SalesForce. Puede agregar fácilmente una acción para enviar el resultado de la transformación a Salesforce. 

Ahora puede probar la transformación realizando una solicitud al punto de conexión HTTP.  

## <a name="features-and-use-cases"></a>Características y casos de uso
* La transformación creada en una asignación puede ser simple, como copiar un nombre y una dirección de un documento a otro. O bien puede crear transformaciones más complejas mediante las operaciones de asignación integradas.  
* Hay varias operaciones de asignación o funciones a las que se puede acceder fácilmente, por ejemplo, cadenas, funciones de fecha hora, etc.  
* Puede realizar una copia de datos directa entre los esquemas. En el Asignador que incluye el SDK, es tan sencillo como dibujar una línea que conecte los elementos del esquema de origen con sus equivalentes en el de destino.  
* Al crear una asignación, verá una representación gráfica de esta, que muestra todos los vínculos y relaciones que cree.
* Utilice la característica Comprobar asignación para agregar un mensaje XML de ejemplo. Con un solo clic, puede probar la asignación creada y ver el resultado generado.  
* Cargue asignaciones que ya existan.  
* Es compatible con el formato XML.

## <a name="learn-more"></a>Más información
* [Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  
* [Más información sobre las asignaciones](../logic-apps/logic-apps-enterprise-integration-maps.md "Información sobre las asignaciones de Enterprise Integration")  

