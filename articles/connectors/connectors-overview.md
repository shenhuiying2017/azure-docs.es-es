---
title: "Información general sobre los conectores de Logic Apps | Microsoft Docs"
description: "Información general sobre los conectores que pueden utilizarse en una aplicación lógica"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 6f7d8f99bfa09847c01831a06efa8b94c1c0a89a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="using-connectors-in-a-logic-app"></a>Uso de conectores en una aplicación lógica
Los conectores proporcionan acceso rápido a eventos, datos y acciones en servicios, protocolos y plataformas.  [Aquí encontrará](apis-list.md) una lista completa de los conectores que Logic Apps admite.  Los conectores pueden usarse como desencadenador o acción de una aplicación lógica. Para utilizarlos, puede ser necesario configurar una *conexión* (por ejemplo, al autorizar a una cuenta de Twitter para que tenga acceso o publique en su nombre).

## <a name="basics"></a>Aspectos básicos
Los conectores son servicios hospedados a los que se puede acceder desde una aplicación lógica para integrar otros servicios, como Dynamics, Azure, Salesforce, [etc](apis-list.md).  Microsoft se encarga de implementarlos y administrarlos, de modo que usted puede crear sus flujos de trabajo de integración sin preocuparse del escalado, el rendimiento y la seguridad.  Para agregar un conector a una aplicación lógica, puede buscar y seleccionar un desencadenador o una acción de conector en **Mostrar las API administradas por Microsoft**.

![Menú de acción para seleccionar un desencadenador][1]

Cada desencadenador o acción de conector tendrá un conjunto de propiedades que deben configurarse.  Puede hacer clic en los botones de información para consultar los detalles de la acción o ver la documentación relacionada [para obtener más información](apis-list.md).

Si desea realizar la integración con un servicio o API que todavía no es un conector, puede ampliar las aplicaciones lógicas mediante un [conector personalizado](../logic-apps/logic-apps-create-api-app.md) o llamar directamente al servicio a través de un protocolo, como HTTP.

## <a name="triggers"></a>Desencadenadores
Algunos conectores tienen un desencadenador, lo que significa que un evento de dicho conector desencadenará una aplicación lógica y pasará los datos como parte del desencadenador.  En una aplicación lógica, los desencadenadores constituyen siempre el primer paso.  Algunos desencadenadores comunes son operaciones, como:

* Periodicidad: se ejecuta cada hora
* Cuando se recibe una solicitud HTTP
* Cuando se agregar un elemento a una cola
* Cuando se recibe un correo electrónico

Algunos desencadenadores se activan en cuanto tiene lugar un evento a través del envío de una notificación a la aplicación lógica, mientras que otros tienen un intervalo de periodicidad configurado que establece la frecuencia con la que la aplicación lógica consulta el servicio en busca de un evento (cada 15 segundos, como máximo).  

Una vez que se recibe el evento, se desencadena la ejecución de la aplicación lógica y se inician las acciones del flujo de trabajo.  También puede acceder a los datos del desencadenador a través del flujo de trabajo (por ejemplo, el desencadenador 'con un nuevo tweet' pasará el tweet al proceso de ejecución).

## <a name="actions"></a>Acciones
La mayoría de los conectores tienen una o varias acciones que se pueden ejecutar durante el flujo de trabajo.  Las acciones son los pasos que tienen lugar una vez que la ejecución se ha activado a partir de un desencadenador.  Para agregar una acción, haga clic en el botón **Nuevo paso** y busque el conector que desea usar.  Una vez seleccionado (y después de configurar las [conexiones](#connections) que podría necesitar), aparecerá la tarjeta de la acción, donde puede definir la configuración.  Puede seleccionar datos de los pasos anteriores. Para ello, haga clic en cualquiera de los tokens de los resultados o especifique cualquier otra configuración, si es necesario.

![Configuración de una acción de conector][2]

## <a name="connections"></a>Conexiones
Para poder utilizar la mayoría de los conectores, es necesario configurar una *conexión* .  Una *conexión* es la configuración de inicio de sesión necesaria para acceder al conector.  En el caso de los conectores que usan OAuth, crear una conexión significa iniciar sesión en el servicio (por ejemplo, Office 365, Salesforce o GitHub), donde el token de acceso puede cifrarse y almacenarse de forma segura en un almacén secreto de Azure.  Otros conectores (por ejemplo, FTP y SQL) necesitan una conexión con una determinada configuración; por ejemplo, la dirección del servidor, el nombre de usuario y la contraseña.  Estos detalles sobre la configuración de la conexión también se cifran y se almacenan de forma segura.  Las conexiones pueden acceder al servicio mientras este lo permita.  En el caso de las conexiones de Azure Active Directory OAuth, como Office 365 y Dynamics, el token de acceso se puede actualizar indefinidamente.  Otros servicios pueden imponer restricciones en el tiempo durante el que puede utilizarse un token sin actualizarlo.  Por lo general, algunas acciones, como los cambios de contraseña, invalidarán todos los tokens de acceso.  

Para ver y administrar las conexiones en Azure, haga clic en **Examinar** y seleccione **Conexiones de API**.  En el recurso API Connections (Conexiones de API), puede ver, editar, actualizar o volver a autorizar cualquier conexión que haya creado.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Ejemplos de aplicaciones lógicas y escenarios comunes](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Información general sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
