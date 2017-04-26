---
title: "Recuperación ante desastres de cuentas de integración de Logic Apps B2B: Azure Logic Apps | Microsoft Docs"
description: "Recuperación ante desastres de Azure Logic Apps B2B"
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
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Recuperación ante desastres de Logic Apps B2B entre regiones
Las cargas de trabajo de B2B implican transacciones monetarias como pedidos y facturas.  Para las empresas es fundamental recuperarse rápidamente después de un desastre a fin de cumplir los SLA de nivel de negocio acordados con sus socios.  En este tema se muestra cómo crear un plan de continuidad empresarial para cargas de trabajo de B2B.  Qué trata

* Crear una cuenta de integración en la región secundaria
* Establecer una conexión desde la región primaria a la secundaria 
* Conmutar por error a la región secundaria durante un desastre
* Recurrir al evento posterior al desastre de la región primaria

## <a name="create-an-integration-account-in-secondary-region"></a>Crear una cuenta de integración en la región secundaria
1. Seleccione una región secundaria y cree una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).  

2. Agregue socios, esquemas y acuerdos para los flujos de mensajes necesarios donde el estado de ejecución se tenga que replicar a la cuenta de integración de la región secundaria.

    > [!Tip]
    > Asegurar la coherencia de la convención de nomenclatura de los artefactos de la cuenta de integración entre regiones 
    > 
    > 

3. La recomendación es implementar todos los recursos de la región primaria (por ejemplo, bases de datos de SQL Azure o DocumentDB, o Service Bus o Event Hubs usados para mensajería, APIM, Logic Apps) también en la región secundaria.  

## <a name="establish-a-connection-from-primary-to-secondary"></a>Establecer una conexión desde la región primaria a la secundaria 
Para extraer el estado de ejecución de la región primaria, cree una aplicación lógica en la región secundaria.  Debe tener un **desencadenador** y una **acción**.  El desencadenador debe conectarse a la cuenta de integración de la región primaria y la acción debe conectarse a la cuenta de integración de la región secundaria.  Según el intervalo de tiempo, el desencadenador sondea la tabla de estado de ejecución de la región primaria, extrae los nuevos registros, si los hubiera, y la acción los actualiza en la cuenta de integración de la región secundaria. Esto ayuda a obtener el estado incremental en tiempo de ejecución de la región primaria a la secundaria.

La continuidad empresarial de la cuenta de integración de Logic Apps está diseñada para admitir protocolos basados en B2B: X12, AS2 y EDIFACT.  Para ver los pasos detallados, seleccione los vínculos correspondientes.

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT (próximamente)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>Conmutar por error a la región secundaria durante un desastre
Durante un desastre, si la región primaria no está disponible para la continuidad empresarial, el tráfico se dirige a la región secundaria. La región secundaria ayuda a recuperar las funciones empresariales rápidamente para cumplir los objetivos de tiempo o punto de recuperación (RPO/RTO) según lo acordado con los socios.  Además, minimiza los esfuerzos de conmutación por error de una región a otra. 

Hay una latencia prevista mientras se copian números de control de la región primaria a la secundaria.  Para evitar el envío de números de control generados duplicados a los socios durante un desastre, se recomienda aumentar los números de control en los **acuerdos de región secundaria** con [cmdlets de PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-primary-region-post-disaster-event"></a>Recurrir al evento posterior al desastre de la región primaria
Si la región primaria está disponible, para recurrir a ella, siga los pasos siguientes     
* Deje de aceptar mensajes de socios en la **región secundaria**.   
* Aumente los números de control generados para todos los **acuerdos de región primaria** con [cmdlets de PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).   
* Dirija el tráfico de la región primaria a la secundaria.   
* Compruebe que la aplicación lógica creada en la región secundaria para extraer el estado de ejecución de la primaria está habilitada.    

## <a name="x12"></a>X12 
La continuidad empresarial de los documentos EDI X12 se ha diseñado basándose en los números de control   
* Números de control recibidos (mensajes entrantes) de socios  
* Números de control generados (mensajes salientes) y enviados a socios  
    
    > [!Tip]
    > También puede usar la [plantilla de inicio rápido de X12](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) para crear Logic Apps.  La creación de cuentas de integración primaria y secundaria es un requisito previo para usar la plantilla.  La plantilla ayuda a crear dos Logic Apps, una para números de control recibidos y otra para números de control generados.  Las acciones y los desencadenadores respectivos se crean en Logic Apps, se conecta el desencadenador a la cuenta de integración primaria y la acción a la cuenta de integración secundaria.
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>Números de control recibidos de los socios
1. Crear una [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en la región secundaria   

2. Busque **X12** y seleccione **X12: Cuando se modifica un número de control recibido**  .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. Si selecciona el desencadenador, se le pide que establezca una conexión a la cuenta de integración. Desencadenador que se va a conectar a la cuenta de integración de la región primaria.  Asigne un nombre de conexión, seleccione la **cuenta de integración de la región primaria** en la lista y haga clic en Crear.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. El campo DateTime para iniciar la sincronización de números de control es opcional.  La frecuencia se puede establecer en **Día**, **Hora**, **Minuto** o **Segundo** con un intervalo.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. Haga clic en **Nuevo paso** y en **Agregar una acción**  .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. Busque **X12** y seleccione **X12: Permite agregar o actualizar un número de control recibido** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. Acción que se va a conectar a la cuenta de integración secundaria.  Seleccione **Cambiar conexión** y **Agregar nueva conexión** muestra las cuentas de integración disponibles.  Asigne un nombre de conexión, seleccione la **cuenta de integración de la región secundaria** en la lista y haga clic en Crear.     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. Seleccionar el contenido dinámico y guardar la aplicación lógica   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. Según el intervalo de tiempo, el desencadenador sondea la tabla de números de control recibidos de la región primaria, extrae los nuevos registros y la acción los actualiza en la cuenta de integración de la región secundaria.  Si no hay actualizaciones, el estado del desencadenador aparece como omitido.
    
    > [!Tip]
    > Si se habilita la comprobación de duplicados en la configuración de recepción del acuerdo, se conserva el estado en tiempo de ejecución de los números de control recibidos y se contribuye a activar los desencadenadores a intervalos regulares
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>Números de control generados y enviados a los socios
1. Crear una [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en la región secundaria  

2. Busque **X12** y seleccione **X12: Cuando se modifica un número de control generado** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. Si selecciona el desencadenador, se le pide que establezca una conexión a la cuenta de integración. Desencadenador que se va a conectar a la cuenta de integración de la región primaria.  Asigne un nombre de conexión, seleccione la **cuenta de integración de la región primaria** en la lista y haga clic en Crear.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. El campo DateTime para iniciar la sincronización de números de control es opcional.  La frecuencia se puede establecer en **Día**, **Hora**, **Minuto** o **Segundo** con un intervalo.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. Haga clic en **Nuevo paso** y en **Agregar una acción** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. Busque **X12** y seleccione **X12: Agregar o actualizar un número de control generado** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. Acción que se va a conectar a la cuenta de integración secundaria.  Seleccione **Cambiar conexión** y **Agregar nueva conexión** muestra las cuentas de integración disponibles.  Asigne un nombre de conexión, seleccione la **cuenta de integración de la región secundaria** en la lista y haga clic en Crear.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. Seleccionar el contenido dinámico y guardar la aplicación lógica   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. Según el intervalo de tiempo, el desencadenador sondea la tabla de números de control recibidos de la región primaria, extrae los nuevos registros y la acción los actualiza en la cuenta de integración de la región secundaria.  Si no hay actualizaciones, el estado del desencadenador aparece como omitido.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

Según el intervalo de tiempo, el estado en tiempo de ejecución incremental se replica de la región primaria a la secundaria.  Durante un desastre, si la región primaria no está disponible, el tráfico se dirige a la región secundaria para la continuidad empresarial. 

## <a name="as2"></a>AS2 
La continuidad empresarial de los documentos que usan el protocolo AS2 está diseñada según el identificador de mensaje y el valor MIC.

> [!Tip]
    > También puede usar la [plantilla de inicio rápido de AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) para crear Logic Apps.  La creación de cuentas de integración primaria y secundaria es un requisito previo para usar la plantilla.  La plantilla ayuda a crear una aplicación lógica que tiene un desencadenador y una acción.  Crea una conexión desde el desencadenador a la cuenta de integración primaria y desde la acción a la cuenta de integración secundaria.
    > 
    >

1. Crear una [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en la región secundaria  

2. Busque **AS2** y seleccione **AS2: Cuando se crea un valor MIC** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. Si selecciona el desencadenador, se le pide que establezca una conexión a la cuenta de integración. Desencadenador que se va a conectar a la cuenta de integración de la región primaria.  Asigne un nombre de conexión, seleccione la **cuenta de integración de la región primaria** en la lista y haga clic en Crear.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. El campo DateTime para iniciar la sincronización del valor MIC es opcional.  La frecuencia se puede establecer en **Día**, **Hora**, **Minuto** o **Segundo** con un intervalo.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. Haga clic en **Nuevo paso** y en **Agregar una acción** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. Busque **AS2** y seleccione **AS2: Agregar o actualizar un valor MIC** .  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. Acción que se va a conectar a la cuenta de integración secundaria.  Seleccione **Cambiar conexión** y **Agregar nueva conexión** muestra las cuentas de integración disponibles.  Asigne un nombre de conexión, seleccione la **cuenta de integración de la región secundaria** en la lista y haga clic en Crear.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. Seleccionar el contenido dinámico y guardar la aplicación lógica   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. Según el intervalo de tiempo, el desencadenador sondea la tabla de la región primaria, extrae los nuevos registros y la acción los actualiza en la cuenta de integración de la región secundaria.  Si no hay actualizaciones, el estado del desencadenador aparece como omitido.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

Según el intervalo de tiempo, el estado en tiempo de ejecución incremental se replica de la región primaria a la secundaria.  Durante un desastre, si la región primaria no está disponible, el tráfico se dirige a la región secundaria para la continuidad empresarial. 

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la [supervisión de mensajes B2B](logic-apps-monitor-b2b-message.md).   
