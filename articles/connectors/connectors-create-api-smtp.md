---
title: Conector de SMTP en Azure Logic Apps | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conexión a SMTP para envío de correo electrónico."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 1cf96bbf8bd215d7ddb3c99860a5cb4e668be3c2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-smtp-connector"></a>Introducción al conector de SMTP
Conexión a SMTP para envío de correo electrónico.

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Conexión a SMTP
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a SMTP, primero es preciso crear una *conexión* a SMTP. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se conecta. Por lo tanto, en el ejemplo de SMTP, escriba las credenciales de su nombre de conexión, la dirección del servidor SMTP y la información de inicio de sesión de usuario para crear una conexión a SMTP.  

### <a name="create-a-connection-to-smtp"></a>Creación de una conexión a SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Uso de un desencadenador de SMTP
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, como SMTP no tiene un desencadenador propio, usaremos el desencadenador **Salesforce - When an object is created (Salesforce - Cuando se crea un objeto)**. Este desencadenador se activa al crear un objeto en Salesforce. En nuestro ejemplo, vamos a configurarlo para que cada vez que se crea un cliente potencial en Salesforce, se produzca una acción *Enviar correo electrónico* a través del conector SMTP con una notificación sobre el cliente potencial que se está creando.

1. Escriba *salesforce* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **Salesforce - When an object is created** (Salesforce: cuando se crea un archivo).  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. Se muestra el control **When an object is created** (Cuando se crea un objeto).
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Seleccione el **tipo de objeto** y luego *Lead* (Cliente potencial) en la lista de objetos. En este paso está indicando que va a crear un desencadenador que enviará una notificación a su aplicación lógica cada vez que se cree un nuevo cliente potencial en Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Se ha creado el desencadenador.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Uso de una acción de SMTP
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Ahora que se ha agregado el desencadenador, siga estos pasos para agregar una acción de SMTP que se produzca cuando se crea un cliente potencial en Salesforce.

1. Seleccione **+ Nuevo paso** para agregar la acción que quiere que se ejecute cuando se cree un cliente potencial.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Seleccione **Add an action**(Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Escriba *smtp* para buscar todas las acciones relacionadas con SMTP.  
4. Seleccione **SMTP - Send Email (SMTP - Enviar correo electrónico)** como la acción que se realizará cuando se crea el cliente potencial. Se abre el bloque de control de acción. Tendrá que establecer la conexión de SMTP en el bloque de diseñador si no lo ha hecho previamente.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Indique la información de correo electrónico que quiera en el bloque **SMTP - Send Email (SMTP - Enviar correo electrónico)**.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Guarde el trabajo para activar el flujo de trabajo.  

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).