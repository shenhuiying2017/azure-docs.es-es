---
title: Preguntas frecuentes sobre Análisis de tráfico de Azure | Microsoft Docs
description: Conozca las respuestas a las preguntas más frecuentes sobre Análisis de tráfico.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 3ab06b624d1e433641d190d9621592ef83df3344
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Preguntas frecuentes sobre Análisis de tráfico

1.  ¿Cuáles son los requisitos previos para usar Análisis de tráfico?

    Los requisitos previos de Análisis de tráfico son los siguientes:

    - Una suscripción activa a Network Watcher
    - Registros de flujo habilitados para los grupos de seguridad de red que desea supervisar
    - Una cuenta de Azure Storage para almacenar los registros de flujo sin formato
    - Un área de trabajo de Log Analytics (OMS) con acceso de lectura y escritura
    - Su cuenta debe tener asignadas las siguientes acciones en el proveedor de Microsoft.Network:

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  ¿Qué regiones de Azure están disponibles en Análisis de tráfico?

    Aunque se trata de una versión preliminar, puede utilizar Análisis de tráfico con grupos de seguridad de red en cualquiera de las siguientes **regiones admitidas**: Centro-oeste de EE. UU., Este de EE.UU., Este de EE. UU. 2, Centro-norte de EE. UU., Centro-sur de EE. UU., Centro de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Europa Occidental, Europa del Norte, Oeste de Reino Unido, Sur de Reino Unido, Este de Australia y Sudeste de Australia. El área de trabajo de Log Analytics debe encontrarse en las regiones Centro-oeste de EE. UU., Este de EE. UU., Europa Occidental, Sudeste de Australia o Sur de Reino Unido.

3.  ¿Los grupos de seguridad de red en los que he habilitado los registros de flujo pueden estar en otras regiones que no sean las de mi área de trabajo de OMS?

    Sí

4.  ¿Puede haber varios grupos de seguridad de red configurados en una única área de trabajo?

    Sí

5.  ¿Puedo usar un área de trabajo de OMS existente?

    Sí. Si selecciona un área de trabajo existente, asegúrese de que se ha migrado al nuevo lenguaje de consulta. Si no quiere actualizar el área de trabajo, debe crear una nueva. Para más información acerca del nuevo lenguaje de consulta, consulte [Actualización de Azure Log Analytics para la nueva búsqueda de registros](../log-analytics/log-analytics-log-search-upgrade.md).

6.  ¿La cuenta de Azure Storage y el área de trabajo de OMS pueden estar en suscripciones diferentes?

    Sí

7.  ¿Puedo almacenar registros sin formato en una cuenta de almacenamiento de otra suscripción?

    Nº Puede almacenar registros sin formato en cualquier cuenta de almacenamiento en la que haya un grupo de seguridad de red habilitado para los registros de flujo; sin embargo, tanto la cuenta de almacenamiento como los registros sin procesar deben estar en la misma suscripción y región.

8.  Si se produce un error de tipo "No se encuentra" al configurar un grupo de seguridad de red para Análisis de tráfico, ¿cómo lo resuelvo?

    Seleccione una de las regiones admitidas que se indican en la pregunta 2. Si selecciona una región que no es compatible, se producirá un error de tipo "No se encuentra".

9.  En los registros de flujo, el estado del grupo de seguridad de red es "No se puede cargar". ¿Qué tengo que hacer a continuación?

    Para que los registros de flujo funcionen correctamente, el proveedor Microsoft.Insight debe estar registrado. Si no sabe con seguridad si el proveedor Microsoft.Insights está registrado o no en su suscripción, sustituya *xxxxx-xxxxx-xxxxxx-xxxx* en el siguiente comando y ejecute los siguientes comandos en PowerShell:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. He configurado la solución. ¿Por qué no veo nada en el panel?

    La primera vez, el panel puede tardar hasta 30 minutos en aparecer. Antes de generar cualquier informe, la solución tiene que agregar datos suficientes para poder inferir información detallada relevante. 

11.  Si aparece un mensaje similar al siguiente: "No encontramos ninguna dato en esta área de trabajo para el intervalo de tiempo especificado. Pruebe a cambiar el intervalo de tiempo o seleccione otra área de trabajo", ¿cómo puedo solucionarlo?

        Pruebe lo siguiente:
        - Intente cambiar el intervalo de tiempo en la barra superior.
        - Seleccione otra área de trabajo de Log Analytics en la barra superior.
        - Intente obtener acceso a Análisis de tráfico después de 30 minutos, si se ha habilitado recientemente.
    
        Si el problema persiste, comparta su experiencia en el [foro de UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Si aparece un mensaje similar al siguiente: "1) Analizando registros de flujo de grupos de seguridad de red por primera vez. Este proceso puede tardar de 20 a 30 minutos en completarse. Compruébelo de nuevo más tarde. 2) Si el paso anterior no funciona y el área de trabajo está en la SKU gratuita, compruebe aquí el uso del área de trabajo para validar el excedente de la cuota y consulte también las preguntas más frecuentes para más información", ¿cómo puedo solucionarlo?

        Este error puede producirse por las siguientes razones:
        - Es posible que el análisis de tráfico se haya habilitado hace poco tiempo y todavía no esté agregando datos suficientes para inferir información detallada relevante.
        - El área de trabajo de OMS está en la SKU gratuita y ha superado los límites de la cuota. En este caso, es posible que deba usar un área de trabajo que esté en una SKU con mayor capacidad.
    
        Si el problema persiste, comparta su experiencia en el [foro de UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Si aparece un mensaje similar al siguiente: "Parece que tenemos información sobre los recursos (topología), pero no sobre los flujos. Mientras tanto, haga clic aquí para ver los datos de los recursos y consulte las preguntas más frecuentes para obtener más información", ¿cómo lo soluciono?

        Puede ver información sobre los recursos en el panel; sin embargo, no aparece ninguna estadística relacionada con los flujos. Es posible que estos datos no aparezcan porque no hay flujos de comunicación entre los recursos. Espere 60 minutos y vuelva a comprobar el estado. Si no sabe con seguridad si hay flujos de comunicación entre los recursos, comparta su experiencia en el [foro de UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14. ¿Puedo configurar Análisis de tráfico mediante PowerShell o una plantilla de Azure Resource Manager?

    No, Análisis de tráfico solo se puede configurar con Azure Portal.

15.  ¿Qué precio tiene Análisis de tráfico?

        Análisis de tráfico se mide para mejorar la reducción de los registros y almacenar los registros mejorados en un área de trabajo de Log Analytics. Mientras esté en versión preliminar, no se cobra por el Análisis de tráfico para mejorar los registros reducidos; sin embargo, la retención de datos en un área de trabajo está sujeta a facturación a las tasas publicadas. Esta respuesta se actualizará una vez que el precio del Análisis de tráfico esté disponible.

16.  ¿Cómo puedo navegar con el teclado en la vista del mapa geográfico?

        La página del mapa geográfico se compone de dos secciones principales:
    
        - **Banner**: el banner situado en la parte superior del mapa geográfico permite seleccionar filtros de distribución del tráfico mediante botones, como Implementación, No Deployment (No implementación), Activo, Inactivo, Análisis de tráfico habilitado, Análisis de tráfico no habilitado, Traffic from countries (Tráfico de países), Inofensivo, Malintencionado, Tráfico malintencionado permitido y la información de la leyenda. Cuando se selecciona un botón específico, se aplica en el mapa el filtro correspondiente; por ejemplo, si un usuario selecciona el botón de filtro "Activo" situado en el banner, se resaltarán en el mapa los centros de datos "activos" de la implementación.
        - **Mapa**: en la sección del mapa situada bajo el banner se muestra la distribución del tráfico entre los centros de datos y los países de Azure.
    
        **Navegación por el banner con el teclado**
    
        - De forma predeterminada, el filtro seleccionado para el banner en la página del mapa geográfico es el del botón "Centros de datos de Azure".
        - Si desea navegar a otro botón de filtros, puede utilizar la tecla `Tab` o `Right arrow` para pasar al siguiente. Para navegar hacia atrás, utilice la tecla `Shift+Tab` o `Left arrow`. La prioridad de desplazamiento en la navegación hacia delante es de izquierda a derecha y de arriba abajo.
        - Presione la tecla de flecha `Enter` o `Down` para aplicar el filtro seleccionado. En función del filtro y de la implementación, se resaltarán uno o varios nodos de la sección del mapa.
            - Para cambiar entre el **banner** y el **mapa**, presione `Ctrl+F6`.
        
        **Navegación por el mapa con el teclado**
    
        - Cuando hay un filtro seleccionado en el banner y se presiona `Ctrl+F6`, el foco se mueve a uno de los nodos resaltados (**Centro de datos de Azure** o **País o región**) de la vista del mapa.
        - Si desea navegar a otros nodos resaltados del mapa, puede usar las teclas `Tab` o `Right arrow` para moverse hacia adelante o las teclas `Shift+Tab` o `Left arrow` para moverse hacia atrás.
        - Para seleccionar un nodo resaltado del mapa, utilice la tecla `Enter` o `Down arrow`.
        - Cuando se selecciona uno de estos nodos, el foco se desplaza al **cuadro de herramientas de información** del nodo. De forma predeterminada, el foco se desplaza al botón Cerrar del **cuadro de herramientas de información**. Si desea navegar dentro de la vista del **cuadro**, utilice las teclas `Right` y `Left arrow` para moverse hacia delante y hacia atrás, respectivamente. Si presiona `Enter`, conseguirá el mismo efecto que si selecciona el botón con el foco en el **cuadro de herramientas de información**.
        - Si presiona `Tab` mientras el foco se encuentra en el **cuadro de herramientas de información**, el foco se desplazará a los puntos de conexión que están en el mismo continente que el nodo seleccionado. Puede utilizar las teclas `Right` y `Left arrow` para navegar por estos puntos de conexión.
        - Si desea navegar a otros puntos de conexión del flujo o a otro clúster de continentes, utilice `Tab` para moverse hacia delante y `Shift+Tab` para moverse hacia atrás.
        - Una vez que el foco esté en `Continent clusters`, utilice las techas de flecha `Enter` o `Down` para resaltar los puntos de conexión del clúster de continentes. Si desea navegar por los puntos de conexión y el botón Cerrar del cuadro informativo del clúster de continentes, utilice la tecla `Right` o `Left arrow` para moverse hacia delante y hacia atrás, respectivamente. Puede utilizar `Shift+L` en cualquier punto de conexión para cambiar a la línea de conexión que une el nodo seleccionado con el punto de conexión. Si vuelve a presionar `Shift+L`, accederá al punto de conexión seleccionado.
        
        En cualquier etapa:
    
        - `ESC` contrae la selección expandida.
        - La tecla `UP Arrow` realiza la misma acción que `ESC`. La tecla `Down arrow` realiza la misma acción que `Enter`.
        - Use `Shift+Plus` para acercar el zoom y `Shift+Minus` para alejarlo.
