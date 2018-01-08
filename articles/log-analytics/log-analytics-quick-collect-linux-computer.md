---
title: "Recopilación de datos de equipos Linux locales con Azure Log Analytics | Microsoft Docs"
description: "Obtenga información sobre cómo implementar el agente de Log Analytics para Linux y habilitar la recopilación de datos de ese sistema operativo con Log Analytics."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fae88e44ee763a0c59b4ad2c731d77db379aa1ee
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>Recopilación de datos de equipos Linux hospedados en el entorno
[Azure Log Analytics](log-analytics-overview.md) puede recopilar datos directamente de los equipos Linux físicos o virtuales y otros recursos del entorno en un único repositorio para una correlación y análisis detallados.  En esta guía de inicio rápido se muestra cómo configurar y recopilar datos de equipos Linux con unos pasos sencillos.  Para máquinas virtuales Linux de Azure, vea el tema [Recopilación de datos acerca de máquinas virtuales de Azure](log-analytics-quick-collect-azurevm.md).  

Para comprender los requisitos de la red y del sistema a fin de implementar el agente de Linux, vea [Collect data from your environment with Azure Log Analytics](log-analytics-concept-hybrid.md#prerequisites) (Recopilar datos de su entorno con Azure Log Analytics).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en Azure Portal desde [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Crear un área de trabajo
1. En Azure Portal, haga clic en **Más servicios**, en la esquina inferior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.<br><br> ![portal de Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Haga clic en **Crear** y, a continuación, seleccione opciones para los elementos siguientes:

  * Proporcione un nombre para la nueva **Área de trabajo de OMS**, como *DefaultLAWorkspace*. 
  * Seleccione una **suscripción** a la que vincularlo en la lista desplegable si la opción predeterminada seleccionada no es adecuada.
  * Para **Grupo de recursos**, seleccione un grupo de recursos existente que contenga una o más máquinas virtuales de Azure.  
  * Seleccione la **Ubicación** en que están implementadas las VM.  Para obtener más información, consulte en qué [regiones está disponible Log Analytics](https://azure.microsoft.com/regions/services/).
  * Puede elegir entre tres **planes de tarifa** diferentes en Log Analytics, pero, en este caso, seleccionaremos el plan **gratis**.  Para obtener más información sobre planes concretos, consulte los [detalles de precios de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Después de proporcionar la información necesaria en el panel **Área de trabajo de OMS**, haga clic en **Aceptar**.  

Mientras se comprueba la información y se crea el espacio de trabajo, puede realizar un seguimiento de su progreso en **Notificaciones** en el menú. 

## <a name="obtain-workspace-id-and-key"></a>Obtención de la clave y el identificador de área de trabajo
Antes de instalar el agente OMS para Linux, necesita la clave y el identificador de área de trabajo para el área de trabajo de Log Analytics.  El script contenedor del agente necesita esta información para configurar el agente de la forma adecuada y asegurarse de que puede comunicarse correctamente con Log Analytics.  

1. En Azure Portal, haga clic en **Más servicios**, en la esquina inferior izquierda. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
2. En la lista de áreas de trabajo de Log Analytics, seleccione *DefaultLAWorkspace* (creada antes).
3. Seleccione **Configuración avanzada**.<br><br> ![Configuración avanzada de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Seleccione **Orígenes conectados** y **Servidores Linux**.   
5. Encontrará los valores a la derecha de **Id. del área de trabajo** y **Clave principal**. Copie y pegue ambos valores en el editor que prefiera.   

## <a name="install-the-agent-for-linux"></a>Instalación del agente para Linux
Los pasos siguientes configuran la instalación del agente de Log Analytics en Azure y en la nube de Azure Government.  

>[!NOTE]
>No se puede configurar el agente de OMS para Linux para informar a varias áreas de trabajo de Log Analytics.  

1. Para configurar el equipo Linux de modo que se conecte con Log Analytics, ejecute el comando siguiente con el identificador del área de trabajo y la clave principal que ha copiado anteriormente.  Este comando descarga el agente, valida su suma de comprobación y lo instala. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar el equipo Linux de modo que se conecte con Log Analytics en la nube de Azure Government, ejecute el comando siguiente con el identificador del área de trabajo y la clave principal que ha copiado anteriormente.  Este comando descarga el agente, valida su suma de comprobación y lo instala. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>Configurar el agente para que se comunique con un servidor proxy

Siga estos pasos si los equipos Linux necesitan comunicarse a través de un servidor proxy con Log Analytics.  El valor de configuración de proxy tiene la siguiente sintaxis: `[protocol://][user:password@]proxyhost[:port]`.

1. Edite el archivo `/etc/opt/microsoft/omsagent/proxy.conf`. Para ello, ejecute los comandos siguientes y cambie los valores según su configuración específica.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Ejecute el comando siguiente para reiniciar el agente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Recopilación de datos de eventos y rendimiento
Log Analytics puede recopilar eventos de Syslog de Linux, así como de los contadores de rendimiento que especifique para los informes y análisis a largo plazo, y actuar cuando se detecte una condición determinada.  Siga estos pasos para configurar la recopilación de eventos desde Syslog de Linux, así como desde varios contadores de rendimiento comunes, para empezar.  

1. Seleccione **Syslog**.  
2. Para agregar un registro de eventos, escriba el nombre del registro.  Escriba **Syslog** y, a continuación, haga clic en el signo más **+**.  
3. En la tabla, desactive los niveles de gravedad **Información**, **Aviso** y **Depurar**. 
4. Haga clic en **Guardar** en la parte superior de la página para guardar la configuración.
5. Seleccione **Linux Performance Data** (Datos de rendimiento de Linux) para habilitar la recopilación de contadores de rendimiento en un equipo Windows. 
6. La primera vez que se configuran los contadores de rendimiento Linux para un área de trabajo de Log Analytics nueva, se ofrece la opción de crear rápidamente varios contadores comunes. Se muestran todos con una casilla junto a cada uno.<br><br> ![Contadores de rendimiento Windows predeterminados seleccionados](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br><br> Haga clic en **Agregar los contadores de rendimiento seleccionados**.  Se agregan con el valor preestablecido de un intervalo de ejemplo de recopilación de diez segundos.  
7. Haga clic en **Guardar** en la parte superior de la página para guardar la configuración.

## <a name="view-data-collected"></a>Ver datos recopilados
Ahora que ya ha habilitado la recopilación de datos, vamos a ver un sencillo ejemplo de búsqueda de registros para consultar algunos datos del equipo de destino.  

1. En Azure Portal, vaya a Log Analytics y seleccione el área de trabajo que creó antes.
2. Haga clic en el icono **Búsqueda de registros**. A continuación, en el campo de consulta del panel Búsqueda de registros, escriba `Perf` y presione Entrar o haga clic en el botón de búsqueda situado a la derecha del campo de consulta.<br><br> ![Ejemplo de consulta de búsqueda de registros de Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Por ejemplo, la consulta de la imagen siguiente ha devuelto 735 registros de rendimiento.<br><br> ![Resultado de búsqueda de registros de Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede quitar el agente del equipo Linux y eliminar el área de trabajo de Log Analytics.  

Para eliminar el agente, realice los pasos siguientes.

1. Descargue en el equipo el [script universal](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) del agente Linux.
2. Ejecute el archivo .sh del lote con el argumento *--purge* en el equipo, lo que elimina completamente el agente y su configuración.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

Para eliminar el área de trabajo, seleccione el área de trabajo de Log Analytics que ha creado anteriormente y, en la página de recursos, haga clic en **Eliminar**.<br><br> ![Eliminar recurso de Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>pasos siguientes
Ahora que ya recopila datos sobre el funcionamiento y el rendimiento del equipo Linux local, le resultará muy fácil empezar a explorar y analizar los datos que se recopilan, además de tomar las medidas correspondientes a partir de estos. Todo ello, *de forma gratuita*.  

Para obtener información sobre cómo ver y analizar los datos, continúe con el tutorial.   

> [!div class="nextstepaction"]
> [Ver o analizar datos en Log Analytics](log-analytics-tutorial-viewdata.md)
