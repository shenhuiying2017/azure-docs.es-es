<properties
    pageTitle="Recopilación de datos de Azure Storage en la información general de Log Analytics | Microsoft Azure"
    description="Los recursos de Azure usan Diagnósticos de Azure para escribir registros y métricas en una cuenta de Azure Storage. Log Analytics puede indexar estos datos y facilitar su búsqueda."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Recopilación de datos de Azure Storage en la información general de Log Analytics

Muchos recursos de Azure pueden escribir registros y métricas en una cuenta de Azure Storage. Log Analytics consume estos datos y facilita la supervisión de los recursos de Azure.

Para escribir datos en Azure Storage, un recurso puede usar Diagnósticos de Azure o su propio método. Estos datos pueden escribirse en diversos formatos en una de las siguientes ubicaciones:

+ Tabla de Azure
+ Blob de Azure
+ EventHub

Log Analytics es compatible con los servicios de Azure que escriben datos mediante [registros de diagnóstico de Azure](../azure-portal/monitoring-overview-of-diagnostic-logs.md). Además, Log Analytics admite otros servicios que generan registros y métricas en diferentes formatos y ubicaciones.  

>[AZURE.NOTE] Al enviar diagnósticos a una cuenta de almacenamiento y por las lecturas de Log Analytics de los datos de la cuenta de almacenamiento se le cobrarán las tarifas de datos de Azure habituales por el almacenamiento y las transacciones.

![Diagrama de Azure Storage](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Recursos de Azure compatibles

Log Analytics pueden recopilar datos de los siguientes recursos de Azure:

| Tipo de recurso | Registros (categorías de diagnóstico) | Solución de Log Analytics |
| --------------------------------------- | -------------------------------- | --------------- |
| Application Insights | Disponibilidad <br> Eventos personalizados <br> Excepciones <br> Solicitudes <br> | Application Insights (versión preliminar) |
| Administración de API | | *Ninguno* (versión preliminar) |
| Automatización <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (versión preliminar) |
| Almacén de claves <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (versión preliminar) |
| Puerta de enlace de aplicaciones <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (versión preliminar) |
| Grupo de seguridad de red (NSG) <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (versión preliminar) |
| Service Fabric                          | ETWEvent <br> Evento operativo <br> Eventos de Reliable Actors <br> Evento de Reliable Services| ServiceFabric (versión preliminar) |
| Máquinas virtuales | Syslog de Linux <br> Evento de Windows <br> Registro de IIS <br> Windows ETWEvent | *Ninguna* |
| Roles web <br> Roles de trabajo | Syslog de Linux <br> Evento de Windows <br> Registro de IIS <br> Windows ETWEvent | *Ninguna* |

>[AZURE.NOTE] Para la supervisión de máquinas virtuales de Azure (Linux y Windows), se recomienda instalar la [extensión para máquinas virtuales de Log Analytics](log-analytics-azure-vm-extension.md). El agente proporciona información más detallada sobre las máquinas virtuales que los diagnósticos que se escriben en Azure Storage.

Puede ayudarnos a dar prioridad a otros registros para que OMS los analice; para ello, vote en nuestra [página de comentarios](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).


- Para más información sobre cómo Log Analytics lee los registros de los servicios de Azure compatibles con los [registros de diagnóstico de Azure](../azure-portal/monitoring-overview-of-diagnostic-logs.md), consulte [Análisis de los registros de diagnóstico de Azure mediante Log Analytics](log-analytics-azure-storage-json.md):
  - Almacén de claves de Azure
  - Automatización de Azure
  - Puerta de enlace de aplicaciones
  - Grupos de seguridad de red
- Consulte [Use blob storage for IIS and table storage for events](log-analytics-azure-storage-iis-table.md) (Uso de Blob Storage para IIS y de Table Storage para eventos) para más información acerca de cómo Log Analytics lee los registros para servicios de Azure que escriben diagnósticos en Table Storage o registros ISS en Blob Storage, por ejemplo:
  - Service Fabric
  - Roles web
  - Roles de trabajo
  - Máquinas virtuales


Application Insights es una versión preliminar privada y utiliza la exportación continua a Blob Storage. Para unirse a versión preliminar privada, póngase en contacto con el equipo de cuentas de Microsoft o consulte los detalles en el [sitio de comentarios](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Análisis de los registros de diagnóstico de Azure mediante Log Analytics](log-analytics-azure-storage-json.md) para aprender a leer los registros de servicios de Azure que escriben diagnósticos en formato JSON en Blob Storage.
- Consulte [Use blob storage for IIS and table storage for events](log-analytics-azure-storage-iis-table.md) (Uso de Blob Storage para IIS y de Table Storage para eventos) para aprender a leer los registros para servicios de Azure que escriben diagnósticos en Table Storage o registros ISS en Blob Storage.
- [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) para más información sobre los datos.
- [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para analizar los datos.



<!--HONumber=Oct16_HO2-->


