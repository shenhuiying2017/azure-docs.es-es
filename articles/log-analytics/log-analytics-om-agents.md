---
title: "Conexión de Operations Manager con Log Analytics | Microsoft Docs"
description: "Para mantener su inversión existente en System Center Operations Manager y usar las funcionalidades ampliadas con Log Analytics, puede integrar Operations Manager con el área de trabajo OMS."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2017
ms.author: magoedte
ms.openlocfilehash: 6db47c7baa0a345a32d26d56e843acd0204ae50b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="connect-operations-manager-to-log-analytics"></a>Conexión de Operations Manager con Log Analytics
Para mantener su inversión existente en System Center Operations Manager y usar las funcionalidades ampliadas con Log Analytics, puede integrar Operations Manager con el área de trabajo OMS.  Esto le permite aprovechar las oportunidades de OMS mientras sigue utilizando Operations Manager para:

* Seguir supervisando el estado de los servicios de TI con Operations Manager
* Mantener la integración con sus soluciones ITSM compatibles con la administración de incidentes y problemas
* Administrar el ciclo de vida de los agentes implementados en máquinas virtuales IaaS en una nube pública y en entornos locales supervisados con Operations Manager

La integración con System Center Operations Manager agrega valor a su estrategia de operaciones de servicio al usar la velocidad y la eficacia de OMS para recopilar, almacenar y analizar datos de Operations Manager.  OMS ayuda a correlacionar y a avanzar para identificar los errores de los problemas y a sacar a la superficie reapariciones para respaldar el proceso de administración de problema existente.   La flexibilidad del motor de búsqueda para examinar el rendimiento, eventos y datos de alertas con abundantes paneles y funcionalidades de informes para exponer estos datos de maneras significativas muestra la solidez que ofrece OMS como complemento de Operations Manager.

Los agentes que informan al grupo de administración de Operations Manager recopilan datos de los servidores en función de los orígenes de datos de Log Analytics y de las soluciones habilitadas en su suscripción de OMS.  Dependiendo de la solución habilitada, los datos de estas soluciones se envían directamente desde un servidor de administración de Operations Manager al servicio web de OMS o bien, debido al volumen de los datos recopilados en el sistema administrado por agentes, se envían directamente desde el agente al servicio web de OMS. El servidor de administración reenvía los datos de OMS directamente al servicio web de OMS. No se escriben nunca en la base de datos de Operations Manager u OperationsManagerDW.  Cuando un servidor de administración pierde conectividad con el servicio web de OMS, almacena en caché los datos localmente hasta que la comunicación se restablezca con OMS.  Si el servidor de administración está sin conexión debido a un mantenimiento planeado o a una interrupción imprevista, otro servidor de administración del grupo de administración reanudará la conectividad de OMS.  

El siguiente diagrama muestra la conexión entre los agentes y servidores de administración de un grupo de administración de System Center Operations Manager y OMS, incluidos los puertos y la dirección.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Si las directivas de seguridad de TI no permiten que los equipos de la red se conecten a Internet, se pueden configurar los servidores de administración para que se conecten a OMS Gateway a fin de recibir información de configuración y enviar los datos recopilados según la solución que haya habilitado.  Para ver información adicional y pasos sobre cómo configurar el grupo de administración de Operations Manager para que se comunique a través de una puerta de enlace de OMS con el servicio OMS, vea [Conexión de equipos sin acceso a OMS mediante la puerta de enlace de OMS](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Requisitos del sistema
Antes de comenzar, revise los detalles siguientes para comprobar que cumple los requisitos previos.

* OMS solo admite Operations Manager 2016, Operations Manager 2012 SP1 UR6 y versiones posteriores y Operations Manager 2012 R2 UR2 y versiones posteriores.  Se agregó compatibilidad con proxy en Operations Manager 2012 SP1 UR7 y Operations Manager 2012 R2 UR3.
* Todos los agentes de Operations Manager deben cumplir los requisitos mínimos de compatibilidad. Asegúrese de que los agentes están actualizados con los requisitos mínimos ya que, de lo contrario, se podría producir un error en el agente de tráfico de Windows y el registro de eventos de Operations Manager se podría llenar con muchos errores.
* Una suscripción de OMS.  Para más información, consulte [Introducción a Log Analytics](log-analytics-get-started.md).

### <a name="network"></a>Red
A continuación se muestra la información de configuración de proxy y firewall requerida para que el agente de Operations Manager, los servidores de administración y la Consola del operador se comuniquen con OMS.  El tráfico de cada componente es de salida (de la red al servicio OMS).     

|Recurso | Número de puerto| Omitir inspección de HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |yes|  
|\*.oms.opinsights.azure.com| 443|Sí|  
|\*blob.core.windows.net| 443|Sí|  
|\*.azure-automation.net| 443|Sí|  
|**Servidor de administración**|||  
|\*.service.opinsights.azure.com| 443||  
|\*blob.core.windows.net| 443| Sí|  
|\*.ods.opinsights.azure.com| 443| Sí|  
|*.azure-automation.net | 443| Sí|  
|**Consola de Operations Manager a OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 y 443||  
|\*.microsoft.com| 80 y 443||  
|\*.microsoftonline.com| 80 y 443||  
|\*.mms.microsoft.com| 80 y 443||  
|login.windows.net| 80 y 443||  


## <a name="connecting-operations-manager-to-oms"></a>Conexión de Operations Manager con OMS
Ejecute la siguiente serie de pasos para configurar el grupo de administración de Operations Manager para conectarse a una de las áreas de trabajo de OMS.

1. En la consola de Operations Manager, seleccione el área de trabajo **Administración** .
2. Expanda el nodo de Operations Management Suite y haga clic en **Conexión**.
3. Haga clic en el vínculo **Register to Operations Management Suite** (Registrar en Operations Management Suite).
4. En la página **Operations Management Suite Onboarding Wizard** (Asistente para incorporación a Operations Management Suite), escriba la dirección de correo electrónico o el número de teléfono y la contraseña de la cuenta de administrador que está asociada a su nueva área de trabajo de OMS y haga clic en **Iniciar sesión**.
5. Una vez correctamente autenticado, en la página **Operations Management Suite Onboarding Wizard: Select Workspace** (Asistente para incorporación a Operations Management Suite: Seleccionar área de trabajo), se le pedirá que seleccione el área de trabajo de OMS.  Si tiene más de un área de trabajo, seleccione el que desee registrar con el grupo de administración de Operations Manager en la lista desplegable y haga clic en **Siguiente**.
   
   > [!NOTE]
   > Operations Manager solo admite un área de trabajo de OMS a la vez. Se quitan de OMS la conexión y los equipos que se registraron en OMS con el área de trabajo anterior.
   > 
   > 
6. En la página **Operations Management Suite Onboarding Wizard: Summary** (Asistente para incorporación a Operations Management Suite: Resumen), confirme la configuración y, si es correcta, haga clic en **Crear**.
7. En la página **Operations Management Suite Onboarding Wizard: Finish** (Asistente para incorporación a Operations Management Suite: Finalizar), haga clic en **Cerrar**.

### <a name="add-agent-managed-computers"></a>Adición de equipos administrados por agente
Después de configurar la integración con el área de trabajo OMS, esto solo establece una conexión con OMS, no se recopilan datos de los agentes que informan al grupo de administración. Esto no será posible hasta que configure qué equipos específicos administrados por agente recopilan los datos para Log Analytics. Puede seleccionar los objetos de equipo individualmente o un grupo que contenga objetos de equipos Windows. No puede seleccionar un grupo que contenga instancias de otra clase, como discos lógicos o bases de datos SQL.

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración** .
2. Expanda el nodo de Operations Management Suite y haga clic en **Conexión**.
3. Haga clic en el vínculo **Agregar un equipo/grupo** debajo del título Acciones en el lado derecho del panel.
4. En el cuadro de diálogo **Búsqueda de equipos**, puede buscar equipos o grupos supervisados por Operations Manager. Seleccione los equipos o grupos que quiera incorporar a OMS, haga clic en **Agregar** y, después, haga clic en **Aceptar**.

Puede ver los equipos y grupos configurados para recopilar datos desde el nodo Equipos administrados de Operations Management Suite en el área de trabajo **Administración** de la consola de Operations.  Desde aquí puede agregar o quitar equipos y grupos según sea necesario.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Configuración del proxy de OMS en la consola de Operations
Realice los pasos siguientes si un servidor proxy interno se encuentra entre el grupo de administración y el servicio web de OMS.  Esta configuración se administra centralmente desde el grupo de administración y se distribuye en sistemas administrados por agente que se incluyen en el ámbito para recopilar datos para OMS.  Esto es beneficioso cuando determinadas soluciones omiten el servidor de administración y envían datos directamente al servicio web de OMS.

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración** .
2. Expanda Operations Management Suite y haga clic en **Conexiones**.
3. En la vista Conexión de OMS, haga clic en **Configurar servidor proxy**.
4. En la página **Operations Management Suite Wizard: Proxy Server** (Asistente para Operations Management Suite: Servidor proxy), seleccione **Use a proxy server to access the Operations Management Suite** (Use un servidor proxy para acceder a Operations Management Suite), escriba la dirección URL con el número de puerto, por ejemplo, http://corpproxy:80 y después haga clic en **Finalizar**.

Si el servidor proxy requiere autenticación, realice los pasos siguientes para configurar las credenciales y las opciones necesarias para realizar la propagación a los equipos administrados que notifican a OMS en el grupo de administración.

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración** .
2. En **RunAs Configuration**, seleccione **Perfiles**.
3. Abra el perfil de **proxy del perfil Ejecutar como de System Center Advisor** .
4. En el Asistente para crear perfiles de ejecución, haga clic en Agregar para usar una cuenta de ejecución. Puede crear una [cuenta de ejecución](https://technet.microsoft.com/library/hh321655.aspx) o usar una existente. Esta cuenta debe contar con los permisos suficientes para pasar a través del servidor proxy.
5. Para establecer la cuenta que se va a administrar, elija **una clase seleccionada, un grupo o un objeto**, haga clic en **Seleccionar** y, después, en **Agrupar**. Para abrir el cuadro **Búsqueda de grupos**.
6. Busque y seleccione **Microsoft System Center Advisor Monitoring Server Group**(Grupo de servidores de supervisión de Microsoft System Center Advisor).  Haga clic en **Aceptar** después de seleccionar el grupo para cerrar el cuadro **Búsqueda de grupos**.
7. Haga clic en **Aceptar** para cerrar el cuadro **Agregar una ejecución como cuenta**.
8. Haga clic en **Guardar** para completar el asistente y guardar los cambios.

Después de crear la conexión y de configurar qué agentes van a recopilar los datos e informar sobre ellos en OMS, se aplica la siguiente configuración en el grupo de administración, no necesariamente en orden:

* Se crea la cuenta de ejecución **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** .  Está asociada con el perfil de ejecución **Microsoft System Center Advisor Run As Profile Blob** (Blob del perfil de ejecución de Microsoft System Center Advisor) y está dirigida a dos clases: **Servidor de recopilación** y **Grupo de administración de Operations Manager**.
* Se crean dos conectores.  El primero se denomina **Microsoft.SystemCenter.Advisor.DataConnector** y se configura automáticamente con una suscripción que reenvía todas las alertas generadas desde instancias de todas las clases en el grupo de administración a Log Analytics de OMS. El segundo conector es **Advisor Connector**, que es responsable de comunicarse con el servicio web de OMS y de compartir datos.
* Se agregan los agentes y los grupos que haya seleccionado para recopilar datos del grupo de administración al **grupo de servidores de supervisión de Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Actualizaciones del módulo de administración
Después de finalizar la configuración, el grupo de administración de Operations Manager establece una conexión con el servicio de OMS.  El servidor de administración se sincroniza con el servicio web y recibe información de configuración actualizada en forma de módulos de administración para las soluciones que ha habilitado que se integran con Operations Manager.   Operations Manager busca actualizaciones para estos módulos de administración y los descargará e importará automáticamente cuando estén disponibles.  Hay dos reglas en particular que controlan este comportamiento:

* **Microsoft.SystemCenter.Advisor.MPUpdate** : Actualiza los módulos de administración base de OMS. Se ejecuta cada 12 horas de forma predeterminada.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : Actualiza los módulos de administración de las soluciones habilitados en el área de trabajo. Se ejecuta cada cinco (5) minutos de forma predeterminada.

Puede invalidar estas dos reglas para evitar la descarga automática mediante la deshabilitación o modificación de la frecuencia de sincronización del servidor de administración con OMS para determinar si está disponible un nuevo módulo de administración y si debe descargarse.  Siga los pasos [Cómo reemplazar una regla o monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar el parámetro **Frequency** con un valor en segundos para cambiar la programación de sincronización o modificar el parámetro **Enabled** para deshabilitar las reglas.  Dirige las invalidaciones para todos los objetos de la clase del grupo de administración de Operations Manager.

Si desea seguir el proceso de control de cambios existente para controlar las versiones del módulo de administración en el grupo de administración de producción, puede deshabilitar las reglas y habilitarlas durante determinadas horas cuando se permiten las actualizaciones. Si tiene un desarrollo o un grupo de administración de control de calidad en su entorno y tiene conectividad a Internet, puede configurar ese grupo de administración con un área de trabajo de OMS para admitir este escenario.  Esto le permite revisar y evaluar las versiones iterativas de los módulos de administración de OMS antes de publicarlas en el grupo de administración de producción.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Cambio de un grupo de Operations Manager a una nueva área de trabajo de OMS
1. Inicie sesión en su suscripción de OMS y cree un área de trabajo en [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Abra la consola de Operations Manager con una cuenta que sea miembro del rol Administradores de Operations Manager y seleccione el área de trabajo **Administración** .
3. Expanda Operations Management Suite y seleccione **Conexiones**.
4. Seleccione el vínculo **Re-configure Operation Management Suite** (Volver a configurar Operation Management Suite) en el centro del panel.
5. Siga la página **Operations Management Suite Onboarding Wizard** (Asistente para incorporación a Operations Management Suite) y escriba la dirección de correo electrónico o el número de teléfono y la contraseña de la cuenta de administrador que está asociada a su nueva área de trabajo de OMS.
   
   > [!NOTE]
   > En la página **Operations Management Suite Onboarding Wizard: Select Workspace** (Asistente para incorporación a Operations Management Suite: Seleccionar área de trabajo) se muestra el área de trabajo existente que está en uso.
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>Validación de la integración de Operations Manager con OMS
Hay varias maneras en las que puede comprobar que la integración de OMS con Operations Manager es correcta.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Para confirmar la integración desde el portal de OMS
1. En el portal de OMS, haga clic en el icono **Configuración**.
2. Seleccione **Connected Sources**(Orígenes conectados).
3. En la tabla de System Center Operations Manager, verá el nombre del grupo de administración con el número de agentes y el estado de cuando se recibieron los datos por última vez.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. Tenga en cuenta el valor de **Id. de área de trabajo** en el lado izquierdo de la página Configuración.  Lo validará con el grupo de administración de Operations Manager a continuación.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar la integración desde la consola de Operations
1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración** .
2. Seleccione **Módulos de administración** y, en el cuadro de texto **Buscar:**, escriba **Advisor** o **Intelligence**.
3. En función de las soluciones habilitadas, verá el módulo de administración correspondiente en los resultados de búsqueda.  Por ejemplo, si ha habilitado la solución Alert Management, el módulo de administración Alert Management de Microsoft System Center Advisor estará en la lista.
4. Desde la vista **Supervisión**, desplácese hasta la vista **Operations Management Suite\Estado de mantenimiento**.  Seleccione un servidor de administración en el panel **Management Server State** (Estado del servidor de administración) y, en el panel **Vista de detalle**, confirme que el valor de la propiedad **URI del servicio de autenticación** coincide con el identificador de área de trabajo de OMS.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>Eliminación de la integración con OMS
Cuando ya no necesite la integración entre el grupo de administración de Operations Manager y el área de trabajo de OMS, es necesario realizar varios pasos para quitar correctamente la conexión y la configuración del grupo de administración. El siguiente procedimiento actualiza el área de trabajo de OMS. Para ello, elimina la referencia del grupo de administración, elimina los conectores de OMS y, luego, elimina los módulos de administración que admiten OMS.   

Los módulos de administración para las soluciones que ha habilitado y que se integran con Operations Manager, así como los módulos de administración necesarios para admitir la integración con el servicio OMS, no se pueden eliminar fácilmente del grupo de administración.  Esto es porque algunos de los módulos de administración de OMS tienen dependencias en otros módulos de administración relacionados.  Para eliminar los módulos de administración con una dependencia en otros módulos de administración, descargue el script para [quitar un módulo de administración con dependencias](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) desde el centro de scripts de TechNet.  

1. Abra el shell de comandos de Operations Manager con una cuenta que sea miembro del rol Administradores de Operations Manager.
   
    > [!WARNING]
    > Compruebe que no tiene ningún módulo de administración personalizado con la palabra Advisor o IntelligencePack en el nombre antes de continuar. De lo contrario, los siguientes pasos los eliminarán del grupo de administración.
    > 

2. Desde el símbolo del shell de comandos, escriba `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Después, escriba `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Para quitar los módulos de administración restantes que tienen una dependencia en otros módulos de administración de System Center Advisor, use el script *RecursiveRemove.ps1* que descargó antes desde el centro de scripts de TechNet.  
 
    > [!NOTE]
    > No elimine los módulos de administración de Microsoft System Center Advisor ni Microsoft System Center Advisor Internal.  
    >  

5. Abra la consola de operaciones de Operations Manager con una cuenta que sea miembro del rol Administradores de Operations Manager.
6. Bajo **Administración**, seleccione el nodo **Módulos de administración** y, en el cuadro **Buscar**, escriba **Advisor** y compruebe que los siguientes módulos de administración todavía se importan en el grupo de administración:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal
7. En el portal de OMS, haga clic en el icono **Configuración**.
8. Seleccione **Connected Sources**(Orígenes conectados).
9. En la tabla de la sección de System Center Operations Manager, verá el nombre del grupo de administración que desea quitar del área de trabajo.  En la columna **Last Data** (Últimos datos), haga clic en **Quitar**.  
   
    > [!NOTE]
    > El vínculo **Quitar** no estará disponible hasta después de 14 días si no hay ninguna actividad detectada en el grupo de administración conectado.  
    > 

10. Aparecerá una ventana que le pide que confirme que desea continuar con la eliminación.  Haga clic en **Sí** para continuar. 

Para eliminar los dos conectores (Microsoft.SystemCenter.Advisor.DataConnector y Advisor Connector), guarde el siguiente script de PowerShell en el equipo y ejecútelo mediante los ejemplos siguientes:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> El equipo desde el que se ejecuta este script, si no es un servidor de administración, debe tener el shell de comandos de Operations Manager instalado según la versión del grupo de administración.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

En el futuro, si pretende volver a conectar el grupo de administración a un área de trabajo de OMS, necesitará volver a importar el archivo de módulo de administración `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` desde el paquete acumulativo de actualizaciones más reciente aplicado al grupo de administración.  Puede encontrar este archivo en la carpeta `%ProgramFiles%\Microsoft System Center 2012` o `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Pasos siguientes
Para agregar funcionalidad y recopilar información, vea [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).


