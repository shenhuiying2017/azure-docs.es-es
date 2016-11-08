---
title: Documentación de Azure Government | Microsoft Docs
description: Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure
services: Azure-Government
cloud: gov
documentationcenter: ''
author: scooxl
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/05/2016
ms.author: scooxl

---
# <a name="azure-government-management-and-security"></a>Administración y seguridad de Azure Government
## <a name="key-vault"></a>Almacén de claves
Para más información sobre este servicio y cómo usarlo, consulte la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentación pública de Azure Key Vault. </a>

### <a name="data-considerations"></a>Consideraciones de datos
La información siguiente identifica el límite de Azure Government para el Almacén de claves de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Todos los datos cifrados con una clave del Almacén de claves de Azure pueden contener datos regulados o controlados. |Los metadatos del Almacén de claves de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener el Almacén de claves.  No inserte datos regulados o controlados en los siguientes campos: nombres de grupos de recursos, nombres del Almacén de claves o nombres de suscripciones. |

El Almacén de claves está disponible en Azure Government con carácter general. Como es público, no hay ninguna extensión, por lo que solo está disponible mediante PowerShell y la CLI.

## <a name="log-analytics"></a>Log Analytics
Log Analytics está disponible con carácter general en Azure Government. 

### <a name="differences-from-public-azure"></a>Diferencias con Azure público
Las siguientes características y soluciones de Log Analytics no están disponibles actualmente en Azure Government. Esta lista se actualiza cuando cambia el estado de las características y soluciones.

* Soluciones que están en versión preliminar en la versión pública de Azure pública, como por ejemplo:
  * Solución de supervisión de red
  * Solución Azure Networking Analytics
  * Solución de Office 365
  * Solución Upgrade Analytics de Windows 10
  * Application Dependency Monitoring
  * Application Insights
  * Registros de actividad de Azure
  * Análisis de Azure Automation
  * Análisis de Key Vault
* Soluciones y características que requieren Azure Automation, como por ejemplo:
  * Administración de actualizaciones
  * Administración de cambios
  * Alertas que desencadenan un Runbook de Azure Automation
* Soluciones y características que requieren actualizaciones a software local, como por ejemplo:
  * Integración con System Center Operations Manager 2016
  * Grupos de equipos de System Center Configuration Manager
  * Solución Surface Hub
* Características de versión preliminar de Azure público, como por ejemplo:
  * Exportación de datos a PowerBI
* Integración de Azure Portal
  * La selección de las cuentas de Azure Storage que se van a supervisar se debe realizar con plantillas de Resource Manager o PowerShell
  * La selección de las máquinas virtuales para habilitar el agente de Log Analytics se debe realizar con plantillas de Resource Manager o PowerShell
  * Métricas de Azure y diagnósticos de Azure
* Aplicaciones móviles de OMS
* Extensión de VM de Agente de Linux de OMS
* Datos de uso

Las características siguientes de Log Analytics se comportan de otra manera en Azure Government:

* El agente de Windows se debe descargar del [portal de Log Analytics](https://oms.microsoft.us) para Azure Government.
* La carga de datos mediante la API Data Collector requiere el uso de la dirección URL de Azure Government, https://*workspaceId*.ods.opinsights.azure.us donde *workspaceId* es el id. de espacio de trabajo del portal de OMS. 
* Para conectar el servidor de administración de System Center Operations Manager a Log Analytics, debe descargar e importar módulos de administración actualizados.
  1. Descargue y guarde los [módulos de administración actualizados](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Descomprima el archivo descargado.
  3. Importe los módulos de administración en Operations Manager. Para más información sobre cómo importar un módulo de administración de un disco, consulte el tema [Importación de un módulo de administración de Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) en el sitio web de Microsoft TechNet.
  4. Para conectar Operations Manager a Log Analytics, siga los pasos descritos en [Conexión de Operations Manager con Log Analytics](../log-analytics/log-analytics-om-agents.md). 

### <a name="frequently-asked-questions"></a>Preguntas más frecuentes
* ¿Se pueden migrar datos desde Log Analytics en Azure público a Azure Government?
  * No. No se pueden migrar datos ni su área de trabajo desde Azure público a Azure Government.
* ¿Se puede cambiar entre las áreas de trabajo de Azure público y Azure Government desde el portal de Log Analytics de OMS?
  * No. Los portales de Azure público y Azure Government son independientes y no comparten información. 

Para más información, consulte [Documentación pública de Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>

<!--HONumber=Oct16_HO2-->


