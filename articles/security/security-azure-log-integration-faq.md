<properties
   pageTitle="Preguntas más frecuentes sobre integración de registro de Azure | Microsoft Azure"
   description="Estas preguntas más frecuentes responden a las preguntas sobre la integración de registro de Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# Preguntas más frecuentes sobre la integración de registro de Azure (P+F)

Estas P+F responden a preguntas acerca de la integración de registro de Azure, un servicio que le permite integrar los registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM). Esta integración proporciona un panel unificado de todos los recursos, locales o en la nube, para que pueda agregar, correlacionar, analizar y alertar de eventos de seguridad asociados a las aplicaciones.

## ¿Cómo puedo ver las cuentas de almacenamiento desde las que el servicio de integración de registro de Azure extrae los registros de máquinas virtuales de Azure?

Ejecute el comando **azlog source list**.

## ¿Cómo se puede actualizar la configuración de proxy?

Si la configuración de proxy no permite el acceso al almacenamiento de Azure directamente, abra el archivo **AZLOG.EXE.CONFIG** en **c:\\Program Files\\Microsoft Azure Log Integration**. Actualice el archivo para que incluya la sección **defaultProxy** con la dirección del proxy de su organización. Después realizar la actualización, detenga e inicie el servicio mediante los comandos **net stop azlog** y **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## ¿Cómo se puede ver la información de suscripción en los eventos de Windows?

Anexe el **subscriptionid** al nombre descriptivo al agregar el origen.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

El archivo XML de eventos presenta los metadatos como se muestra a continuación, incluido el identificador de suscripción.

![XML de eventos][1]

## mensajes de error

### Cuando ejecuto el comando **azlog createazureid**, ¿por qué obtengo el siguiente error?

Error:

  *Failed to create AAD Application (Error al crear la aplicación de AAD): Inquilino 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = 'Forbidden' - Message = 'No tiene privilegios suficientes para completar la operación'.*

**Azlog createazureid** intenta crear una entidad de servicio en todos los inquilinos de Azure AD de las suscripciones a las que el inicio de sesión de Azure tiene acceso. Si el inicio de sesión de Azure es solo el de un usuario invitado en ese inquilino de Azure AD, el comando generará el error 'No tiene privilegios suficientes para completar la operación'. Solicite al administrador de inquilinos que agregue su cuenta como una cuenta de usuario en el inquilino.

### Cuando ejecuto el comando **azlog authorize**, ¿por qué obtengo el siguiente error?

Error:

  *Advertencia de creación de la asignación de rol - AuthorizationFailed: el cliente janedo@microsoft.com' con objeto de identificador 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/ subscriptions/70 d. 95299-d689-4c 97-b971-0d8ff0000000'.*

El comando **Azlog authorize** asigna el rol Lector a la entidad de servicio de Azure AD (creada con **Azlog createazureid**) para las suscripciones proporcionadas. Si el inicio de sesión de Azure no se realiza mediante una cuenta de coadministrador o de propietario de la suscripción, se producirá el error ‘Error de autorización’. Se necesita el control de acceso basado en roles de Azure (RBAC) de una cuenta de coadministrador o de propietario para completar esta acción.

## ¿Dónde puedo encontrar la definición de las propiedades de registro de auditoría?

Consulte:

- [Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)
- [List the management events in a subscription in Azure Insights REST API (Lista de los eventos de administración de una suscripción en la API de REST de Azure Insights)](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## ¿Dónde puedo encontrar detalles sobre las alertas de Azure Security Center?

Consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## ¿Cómo puedo modificar la información recopilada mediante el diagnóstico de máquinas virtuales?

Consulte [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) para más información acerca de cómo obtener, modificar y configurar Diagnósticos de Azure en Windows *(WAD)*. A continuación se muestra un ejemplo:

### Obtención de la configuración de WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### Modificación de la configuración de WAD

El ejemplo siguiente muestra una configuración en la que se recopilan solo los eventos EventID 4624 y EventId 4625 desde el registro de eventos de seguridad. Los eventos de Microsoft Antimalware se recopilan desde el registro de eventos del sistema. Consulte [Consuming events (Eventos de consumo)](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85) para más información sobre el uso de expresiones de XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### Configuración de WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Después de realizar cambios, compruebe la cuenta de almacenamiento para asegurarse de que se recopilan los eventos correctos.

Si tiene dudas sobre la integración del registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

<!---HONumber=AcomDC_0921_2016-->