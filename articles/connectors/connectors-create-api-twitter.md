---
title: Aprenda a usar el conector de Twitter en Logic Apps | Microsoft Docs
description: "Información general del conector de Twitter con parámetros de la API de REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: be8163043535833ce45b3d50939a537406cf8152
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-twitter-connector"></a>Introducción al conector de Twitter
Con el conector de Twitter puede:

* Publicar y obtener tweets
* Acceder a escalas de tiempo, amigos y seguidores
* Ejecutar cualquiera de las demás acciones y desencadenadores que se describen a continuación  

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).  

## <a name="connect-to-twitter"></a>Conexión a Twitter
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.  

### <a name="create-a-connection-to-twitter"></a>Creación de una conexión a Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Uso de un desencadenador de Twitter
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, le mostraré cómo usar el desencadenador **When a new tweet is posted (Cuando se publica un nuevo tweet)** para buscar #Seattle y, si se encuentra #Seattle, actualizar un archivo en Dropbox con el texto del tweet. En un ejemplo de la empresa, puede buscar el nombre de su compañía y actualizar una base de datos SQL con el texto del tweet.

1. Escriba *twitter* en el cuadro de búsqueda del Diseñador de Logic Apps y seleccione el desencadenador **Twitter - When a new tweet is posted (Twitter - Cuando se publica un nuevo tweet)**.   
   ![Imagen de desencadenador de Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Escriba *#Seattle* en el control **Buscar texto**.  
   ![Imagen de desencadenador de Twitter 2](./media/connectors-create-api-twitter/trigger-2.png) 

En este punto, la aplicación lógica se ha configurado con un desencadenador que iniciará una ejecución de los otros desencadenadores y acciones en el flujo de trabajo. 

> [!NOTE]
> Para que una aplicación lógica sea funcional, debe contener al menos un desencadenador y una acción. Siga los pasos que se describen en la sección siguiente para agregar una acción.  
> 
> 

## <a name="add-a-condition"></a>Agregar una condición
Puesto que solo estamos interesados en tweets de usuarios con más de 50 seguidores, primero se debe agregar a la aplicación lógica una condición que confirme el número de seguidores.  

1. Seleccione **+ Nuevo paso** para agregar la acción que quiere que se ejecute cuando se encuentre #Seattle en un nuevo tweet.  
   ![Imagen de acción de Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Seleccione el vínculo **Agregar una condición**.  
   ![Imagen de condición de Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Se abrirá el control **Condición** donde puede comprobar las condiciones como *es igual a*, *es menor que*, *es mayor que*, *contiene*, etc.  
   ![Imagen de condición de Twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Seleccione el control **Elegir un valor**.  
   En este control, puede seleccionar una o varias de las propiedades de cualquier acción o desencadenador anterior como el valor cuya condición se evaluará como True o False.
   ![Imagen de condición de Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Seleccione **…** para expandir la lista de propiedades y ver todas las propiedades que están disponibles.        
   ![Imagen de condición de Twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Seleccione la propiedad **Número de seguidores**.    
   ![Imagen de condición de Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Observe que la propiedad de número de seguidores ahora está en el control de valor.    
   ![Imagen de condición de Twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Seleccione **es mayor que** de la lista de operadores.    
   ![Imagen de condición de Twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Escriba 50 como el operando para el operador *es mayor que*.  
   Se agrega la condición. Guarde su trabajo mediante el vínculo **Guardar** en el menú superior.    
   ![Imagen de condición de Twitter 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Uso de una acción de Twitter
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Ahora que ha agregado un desencadenador, siga estos pasos para incorporar una acción que publicará un nuevo tweet con el contenido de los tweets detectados por el desencadenador. Para los fines de este tutorial, solo se publicarán tweets de los usuarios con más de 50 seguidores.  

En el paso siguiente, agregará una acción de Twitter que publicará un tweet utilizando algunas de las propiedades de cada tweet publicado por un usuario que tenga más de 50 seguidores.  

1. Seleccione **Add an action**(Agregar una acción). Esto abre el control de búsqueda, donde puede buscar otras acciones y desencadenadores.  
   ![Imagen de condición de Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Escriba *twitter* en el cuadro de búsqueda, después, seleccione la acción **Twitter - Post a tweet (Twitter - Publicar un tweet)**. Se abrirá el control **Post a tweet (Publicar un tweet)** donde especificará todos los detalles para el tweet publicado.      
   ![Imagen de acción de Twitter 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Seleccione el control **Tweet text (Texto del tweet)**. Todos los resultados de las acciones y desencadenadores anteriores de la aplicación lógica ya están visibles. Puede seleccionar cualquiera de ellos y usarlos como parte del texto del tweet del nuevo tweet.     
   ![Imagen de acción de Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Seleccione **Nombre de usuario**.   
5. Escriba *dice:* en el control del texto del tweet. Hágalo justo después del nombre de usuario.  
6. Seleccione *Tweet text (Texto del tweet)*.       
   ![Imagen de acción de Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Guarde el trabajo y envíe un tweet con el hashtag #Seattle para activar el flujo de trabajo.  


## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)

