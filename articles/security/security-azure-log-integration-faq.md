---
title: "Preguntas más frecuentes sobre Azure Log Integration | Microsoft Docs"
description: "Este artículo responde a las preguntas sobre Azure Log Integration."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 08/07/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: bfdc7154160bb6bb7dc9c46eb2352ce74310c4de
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="azure-log-integration-faq"></a>Preguntas más frecuentes sobre Azure Log Integration
En este artículo se responden algunas preguntas frecuentes sobre Azure Log Integration. 

Azure Log Integration es un servicio del sistema operativo Windows que puede usar para integrar los registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM). Esta integración le proporciona un panel unificado de todos los recursos, locales o en la nube. Puede agregar, correlacionar, analizar y alertar sobre eventos de seguridad asociados con las aplicaciones.

## <a name="is-the-azure-log-integration-software-free"></a>¿Es el software Integración de registro de Azure gratuito?
Sí. No hay ningún cargo por el software Integración de registro de Azure.

## <a name="where-is-azure-log-integration-available"></a>¿Dónde se encuentra disponible la integración de registro de Azure?

Está actualmente disponible en la versión comercial de Azure y en Azure Government. No se encuentra disponible ni en China ni en Alemania.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>¿Cómo puedo ver las cuentas de almacenamiento desde las que el servicio Azure Log Integration extrae los registros de máquinas virtuales de Azure?
Ejecute el comando **azlog source list**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>¿Cómo puedo saber de qué suscripción son los registros de integración de Azure?

En el caso de los registros de auditoría que se colocan en los directorios **AzureResourcemanagerJson**, el identificador de la suscripción está en el nombre del archivo de registro. Esto también se aplica en el caso de los registros de la carpeta **AzureSecurityCenterJson**. Por ejemplo:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Los registros de auditoría de Azure Active Directory incluyen el identificador del inquilino como parte del nombre.

Los registros de diagnóstico que se leen desde una instancia de Event Hub no incluyen el identificador de suscripción como parte del nombre. En su lugar, incluyen el nombre descriptivo especificado como parte de la creación del origen de la instancia de Event Hub. 

## <a name="how-can-i-update-the-proxy-configuration"></a>¿Cómo se puede actualizar la configuración de proxy?
Si la configuración de proxy no permite el acceso a Azure Storage directamente, abra el archivo **AZLOG.EXE.CONFIG** en **c:\Program Files\Microsoft Azure Log Integration**. Actualice el archivo para que incluya la sección **defaultProxy** con la dirección del proxy de su organización. Después realizar la actualización, detenga e inicie el servicio mediante los comandos **net stop azlog** y **net start azlog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>¿Cómo se puede ver la información de suscripción en los eventos de Windows?
Al agregar el origen, anexe el identificador de suscripción al nombre descriptivo:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
El archivo XML de eventos presenta los siguientes metadatos, incluido el identificador de suscripción:

![XML de eventos][1]

## <a name="error-messages"></a>mensajes de error
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Cuando ejecuto el comando **azlog createazureid**, ¿por qué obtengo el siguiente error?
Error:

  *Failed to create AAD Application (Error al crear la aplicación de AAD): Inquilino 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = 'Forbidden' - Message = 'No tiene privilegios suficientes para completar la operación'.*

El comando **Azlog createazureid** intenta crear una entidad de servicio en todos los inquilinos de Azure AD de las suscripciones a las que el inicio de sesión de Azure tiene acceso. Si el inicio de sesión de Azure es solo el de un usuario invitado en ese inquilino de Azure AD, el comando generará el error "No tiene privilegios suficientes para completar la operación". Solicite al administrador de inquilinos que agregue su cuenta como una cuenta de usuario en el inquilino.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Cuando ejecuto el comando **azlog authorize**, ¿por qué obtengo el siguiente error?
Error:

  *Advertencia de creación de la asignación de rol - AuthorizationFailed: el cliente janedo@microsoft.com con objeto de identificador fe9e03e4-4dad-4328-910f-fd24a9660bd2 no tiene autorización para realizar la acción Microsoft.Authorization/roleAssignments/write en el ámbito /subscriptions/70d95299-d689-4c97-b971-0d8ff0000000.*

El comando **azlog authorize** asigna el rol Lector a la entidad de servicio de Azure AD (creada con **azlog createazureid**) para las suscripciones proporcionadas. Si el inicio de sesión de Azure no se realiza mediante una cuenta de coadministrador o de propietario de la suscripción, se producirá el error "Error de autorización". Se necesita el control de acceso basado en roles de Azure (RBAC) de una cuenta de coadministrador o de propietario para completar esta acción.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>¿Dónde puedo encontrar la definición de las propiedades de registro de auditoría?
Consulte:

* [Operaciones de auditoría con Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Lista de los eventos de administración de una suscripción en la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>¿Dónde puedo encontrar detalles sobre las alertas de Azure Security Center?
Consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>¿Cómo puedo modificar la información recopilada mediante el diagnóstico de máquinas virtuales?
Consulte [Uso de PowerShell para habilitar Azure Diagnostics en una máquina virtual con Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para más información acerca de cómo obtener, modificar y configurar Azure Diagnostics en Window. 

En el ejemplo siguiente se muestra la configuración de Azure Diagnostics:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

En el ejemplo siguiente se modifica la configuración de Azure Diagnostics. En esta configuración, se recopilan solo los eventos ID 4624 e ID 4625 del registro de eventos de seguridad. Los eventos de Microsoft Antimalware para Azure se recopilan desde el registro de eventos del sistema. Para más información sobre el uso de expresiones XPath, consulte [Consumo de eventos](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

En el ejemplo siguiente se establece la configuración de Azure Diagnostics:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Después de realizar cambios, compruebe la cuenta de almacenamiento para asegurarse de que se recopilan los eventos correctos.

Si tiene algún problema durante la instalación y configuración, abra una [solicitud de soporte técnico](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Seleccione **Azure Log Integration** como el servicio para el que está solicitando el soporte técnico.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>¿Puedo usar Azure Log Integration para integrar los registros de Network Watcher en mi SIEM?

Azure Network Watcher genera grandes cantidades de información de registro. Estos registros no están diseñados para enviarse a un SIEM. El único destino admitido para los registros de Network Watcher es una cuenta de almacenamiento. Azure Log Integration no admite la lectura de estos registros ni su envío a un SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
