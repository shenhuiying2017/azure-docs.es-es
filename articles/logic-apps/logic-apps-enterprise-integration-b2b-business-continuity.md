---
title: "Recuperación ante desastres de cuentas de integración de Logic Apps B2B: Azure App Service | Microsoft Docs"
description: "Recuperación ante desastres de Logic Apps B2B"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: e28c1410145d8da168a73e74251ac037997d1752
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Recuperación ante desastres de Logic Apps B2B entre regiones
Las cargas de trabajo de B2B implican transacciones monetarias como pedidos y facturas. Durante un evento de desastre, es fundamental que una empresa se recupere rápidamente a fin de cumplir los Acuerdos de Nivel de Servicio a nivel de negocio acordados con sus asociados. En este artículo se demuestra cómo crear un plan de continuidad empresarial para cargas de trabajo de B2B. 

* Preparación para la recuperación ante desastres 
* Conmutar por error a la región secundaria durante un desastre 
* Recurrir al evento posterior al desastre de la región primaria

## <a name="disaster-recovery-readiness"></a>Preparación para la recuperación ante desastres  

1. Identifique una región secundaria y cree una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) en ella 

2. Agregue socios, esquemas y acuerdos para los flujos de mensajes necesarios donde el estado de ejecución se tenga que replicar a la cuenta de integración de la región secundaria.

    > [!Tip]
    > Asegúrese de que haya coherencia en la convención de nomenclatura de los artefactos de la cuenta de integración entre regiones. 
    > 
    > 

3. Para extraer el estado de ejecución de la región primaria, cree una aplicación lógica en la región secundaria.  Debe tener un **desencadenador** y una **acción**.  El desencadenador debe conectarse a la cuenta de integración de la región primaria y la acción debe conectarse a la cuenta de integración de la región secundaria.  Según el intervalo de tiempo, el desencadenador sondea la tabla de estado de ejecución de la región primaria, extrae los nuevos registros, si los hubiera, y la acción los actualiza en la cuenta de integración de la región secundaria. Esto ayuda a obtener el estado incremental en tiempo de ejecución de la región primaria a la secundaria.

4. La continuidad empresarial de la cuenta de integración de Logic Apps está diseñada para admitir protocolos basados en B2B: X12, AS2 y EDIFACT.  Para ver los pasos detallados, seleccione los vínculos correspondientes.

5. Se recomienda implementar todos los recursos de la región primaria también en la región secundaria. Los recursos de la región primaria incluyen Azure SQL Database o Azure DocumentDB, Azure Service Bus/Azure Event Hubs para mensajería, Azure API Management y la característica Logic Apps de Azure App Service.   

6. Establezca una conexión desde una región primaria a una región secundaria. Para extraer el estado de ejecución de una región primaria, cree una aplicación lógica en una región secundaria. Debe tener un desencadenador y una acción. El desencadenador se debe conectar a la cuenta de integración de una región primaria. La acción se debe conectar a la cuenta de integración de una región secundaria. Según el intervalo de tiempo, el desencadenador sondea la tabla de estado de ejecución de la región primaria y extrae los nuevos registros, si los hubiera. La acción los actualiza en la cuenta de integración de una región secundaria. Este proceso ayuda a obtener el estado incremental en el entorno de tiempo de ejecución de la región primaria a la secundaria.

La continuidad empresarial en una cuenta de integración de Logic Apps proporciona compatibilidad con los protocolos B2B X12, AS2 y EDIFACT. Para ver los pasos detallados sobre cómo usar X12 y AS2, consulte [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) y [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) en este artículo.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Conmutación por error a una región secundaria durante un evento de desastre
Durante un desastre, si la región primaria no está disponible para la continuidad empresarial, el tráfico se dirige a la región secundaria. Una región secundaria ayuda a que una empresa recupere rápidamente sus funciones para cumplir con el RPO y RTO acordado con sus asociados. También minimiza los esfuerzos de conmutación por error de una región a otra. 

Hay una latencia prevista mientras se copian números de control de una región primaria a una secundaria. Para evitar el envío de números de control generados duplicados a los asociados durante un desastre, se recomienda aumentar los números de control en los contratos de región secundaria con [cmdlets de PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Reversión al evento posterior al desastre de la región primaria
Para revertir a una región primaria si está disponible, siga estos pasos:

1. Deje de aceptar mensajes de socios en la región secundaria.  

2. Aumente los números de control generados para todos los contratos de región primaria con [cmdlets de PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Dirigir el tráfico desde la región secundaria a la región primaria.

4. Compruebe que la aplicación lógica creada en la región secundaria para extraer el estado de ejecución de la primaria está habilitada.

## <a name="x12"></a>X12 
La continuidad empresarial de los documentos EDI X12 se basa en los números de control:
* Números de control recibidos (mensajes entrantes) de asociados  
* Números de control generados (mensajes salientes) y enviados a asociados 
    
    > [!Tip]
    > También puede usar la [plantilla de inicio rápido de X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) para crear aplicaciones lógicas. La creación de cuentas de integración primaria y secundaria es un requisito previo para usar la plantilla. La plantilla ayuda a crear dos aplicaciones lógicas, una para números de control recibidos y otra para números de control generados. Las acciones y los desencadenadores respectivos se crean en las aplicaciones lógicas, conectando el desencadenador a la cuenta de integración primaria y la acción a la cuenta de integración secundaria.
    > 
    >

### <a name="control-numbers-received-from-partners"></a>Números de control recibidos de los asociados

1. Habilite las comprobaciones de duplicados en la configuración de recepción del contrato   
![Búsqueda de X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

2. Cree una [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en una región secundaria. 

3. Busque en **X12** y seleccione **X12: Cuando se modifica un número de control recibido**.   
![Búsqueda de X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

4. El desencadenador le pide que establezca una conexión con una cuenta de integración. El desencadenador se debe conectar a la cuenta de integración de una región primaria. Escriba un nombre de conexión, seleccione la **cuenta de integración de la región primaria** en la lista y haga clic en **Crear**.  
![Nombre de la cuenta de integración de la región primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

5. La configuración **DateTime para iniciar la sincronización de números de control** es opcional. La **frecuencia** se puede establecer en **Día**, **Hora**, **Minuto** o **Segundo** con un intervalo.  
![DateTime y frecuencia](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

6. Seleccione **Nuevo paso** > **Agregar una acción**.    
![Agregar una acción](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

7. Busque en **X12** y seleccione **X12: Permite agregar o actualizar un número de control recibido**.   
![Modificación del número de control recibido](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

8. Para conectar una acción a la cuenta de integración de una región secundaria, seleccione **Cambiar conexión** > **Agregar nueva conexión** para ver una lista de las cuentas de integración disponibles. Escriba un nombre de conexión, seleccione la **cuenta de integración de la región secundaria** en la lista y haga clic en **Crear**.   
![Nombre de la cuenta de integración de la región secundaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

9. Seleccione el contenido dinámico y guarde la aplicación lógica. 
![Contenido dinámico](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

10. Según el intervalo de tiempo, el desencadenador sondea la tabla de número de control recibido de la región primaria y extrae los nuevos registros. La acción los actualiza en la cuenta de integración de una región secundaria. Si no hay actualizaciones, el estado del desencadenador aparece como **Omitido**.
![Tabla de número de control](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)

### <a name="control-numbers-generated-and-sent-to-partners"></a>Números de control generados y enviados a los asociados
1. Cree una [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en una región secundaria.

2. Busque en **X12** y seleccione **X12: Cuando se modifica un número de control generado**.  
![Modificación del número de control generado](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. El desencadenador le pide que establezca una conexión con una cuenta de integración. El desencadenador se debe conectar a la cuenta de integración de una región primaria. Escriba un nombre de conexión, seleccione la **cuenta de integración de la región primaria** en la lista y haga clic en **Crear**.   
![Nombre de la cuenta de integración de la región primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. La configuración **DateTime para iniciar la sincronización de números de control** es opcional. La **frecuencia** se puede establecer en **Día**, **Hora**, **Minuto** o **Segundo** con un intervalo.  
![DateTime y frecuencia](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. Seleccione **Nuevo paso** > **Agregar una acción**.  
![Agregar una acción](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. Busque en **X12** y seleccione **X12: Agregar o actualizar un número de control generado**.   
![Incorporación o actualización de número de control generado](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. Para conectar una acción a la cuenta de integración secundaria, seleccione **Cambiar conexión** > **Agregar nueva conexión** para ver una lista de las cuentas de integración disponibles. Escriba un nombre de conexión, seleccione la **cuenta de integración de la región secundaria** en la lista y haga clic en **Crear**.   
![Nombre de la cuenta de integración de la región secundaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. Seleccione el contenido dinámico y guarde la aplicación lógica. 
![Contenido dinámico](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. Según el intervalo de tiempo, el desencadenador sondea la tabla de número de control recibido de la región primaria y extrae los nuevos registros. La acción los actualiza en la cuenta de integración de una región secundaria. Si no hay actualizaciones, el estado del desencadenador aparece como **Omitido**.  
![Tabla de número de control](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

Según el intervalo de tiempo, el estado en tiempo de ejecución incremental se replica de una región primaria a una secundaria. Durante un desastre, si la región primaria no está disponible, el tráfico se dirige a la región secundaria para la continuidad empresarial. 

## <a name="as2"></a>AS2 
La continuidad empresarial de los documentos que usan el protocolo AS2 se basa en el identificador de mensaje y el valor MIC.

> [!Tip]
    > También puede usar la [plantilla de inicio rápido de AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para crear aplicaciones lógicas. La creación de cuentas de integración primaria y secundaria es un requisito previo para usar la plantilla. La plantilla ayuda a crear una aplicación lógica que tiene un desencadenador y una acción. La aplicación lógica crea una conexión desde un desencadenador a una cuenta de integración primaria y una acción a una cuenta de integración secundaria.
    > 
    >

1. Cree una [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en la región secundaria.  

2. Busque en **AS2** y seleccione **AS2: Cuando se crea un valor MIC**.   
![Búsqueda de AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. Un desencadenador le pide que establezca una conexión con una cuenta de integración. El desencadenador se debe conectar a la cuenta de integración de una región primaria. Escriba un nombre de conexión, seleccione la **cuenta de integración de la región primaria** en la lista y haga clic en **Crear**.   
![Nombre de la cuenta de integración de la región primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. La configuración **DateTime para iniciar la sincronización del valor MIC** es opcional. La **frecuencia** se puede establecer en **Día**, **Hora**, **Minuto** o **Segundo** con un intervalo.   
![DateTime y frecuencia](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. Seleccione **Nuevo paso** > **Agregar una acción**.  
![Agregar una acción](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. Busque en **AS2** y seleccione **AS2: Agregar o actualizar un valor MIC**.  
![Incorporación o actualización de MIC](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. Para conectar una acción a la cuenta de integración secundaria, seleccione **Cambiar conexión** > **Agregar nueva conexión** para ver una lista de las cuentas de integración disponibles. Escriba un nombre de conexión, seleccione la **cuenta de integración de la región secundaria** en la lista y haga clic en **Crear**.    
![Nombre de la cuenta de integración de la región secundaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. Seleccione el contenido dinámico y guarde la aplicación lógica.   
![Contenido dinámico](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. Según el intervalo de tiempo, el desencadenador sondea la tabla de la región primaria y extrae los nuevos registros. La acción los actualiza en la cuenta de integración de una región secundaria. Si no hay actualizaciones, el estado del desencadenador aparece como **Omitido**.  
![Tabla de la región primaria](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

Según el intervalo de tiempo, el estado en tiempo de ejecución incremental se replica de la región primaria a la secundaria. Durante un desastre, si la región primaria no está disponible, el tráfico se dirige a la región secundaria para la continuidad empresarial. 

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la [supervisión de mensajes B2B](logic-apps-monitor-b2b-message.md).   


