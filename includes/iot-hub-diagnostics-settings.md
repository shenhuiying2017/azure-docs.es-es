### <a name="enable-logging-with-diagnostics-settings"></a>Habilitación del registro con la configuración de diagnóstico

1. Inicie sesión en [Azure Portal][lnk-portal] y vaya a IoT Hub.
1. Seleccione **Configuración de diagnóstico**.
1. Seleccione **Activar diagnósticos**.

   ![Activación del diagnóstico][1]

1. Asigne un nombre a la configuración de diagnóstico.
1. Elija dónde desea enviar los registros. También puede seleccionar cualquier combinación de las tres opciones:
   * Archivar en una cuenta de almacenamiento
   * Transmitir a un centro de eventos
   * Enviar a Log Analytics
1. Elija qué operaciones desea supervisar y habilite los registros para esas operaciones. Las operaciones sobre las que puede informar la configuración de diagnóstico son:
   * Conexiones
   * Telemetría de dispositivo
   * Mensajes de nube a dispositivo
   * Operaciones de identidad de dispositivos
   * Cargas de archivos
   * Enrutamiento de mensajes
   * Operaciones gemelas de la nube al dispositivo
   * Operaciones gemelas del dispositivo a la nube
   * Operaciones gemelas
   * Operaciones de trabajos
   * Métodos directos  
1. Guarde la configuración nueva. 

Si desea activar la configuración de diagnóstico con PowerShell, utilice el código siguiente:

```
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

La nueva configuración surte efecto en unos 10 minutos. Después, los registros aparecen en el destino de archivo configurado, en la hoja **Configuración de diagnóstico**. Para más información sobre la configuración de diagnósticos, vea [Recopile y use los datos de registro provenientes de los recursos de Azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
