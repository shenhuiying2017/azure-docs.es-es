<properties
	pageTitle="Documentación de Azure Government | Microsoft Azure"
	description="Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Administración y seguridad de Azure Government

##  Almacén de claves

La información siguiente identifica el límite de Azure Government para el Almacén de claves de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos cifrados con una clave del Almacén de claves de Azure pueden contener datos regulados o controlados. | Los metadatos del Almacén de claves de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener el Almacén de claves. No inserte datos regulados o controlados en los siguientes campos: nombres de grupos de recursos, nombres del Almacén de claves o nombres de suscripciones. |

El Almacén de claves está disponible en Azure Government con carácter general. Como es público, no hay ninguna extensión, por lo que solo está disponible mediante PowerShell y la CLI.

Para más información, consulte la [documentación pública del Almacén de claves de Azure](/key-vault-get-started).

## Log Analytics

Log Analytics se encuentra en su versión preliminar en Azure Government.

### Diferencias con Azure público

Las siguientes características y soluciones de Log Analytics no están disponibles actualmente en Azure Government. Esta lista se actualiza cuando cambia el estado de las características y soluciones.

+ Métricas casi en tiempo real
  - Cuando los gráficos de métricas se ven a intervalos de tiempo inferiores a 6 horas, el gráfico no se actualiza automáticamente con los valores de métricas nuevos.
+ Exportación de datos a PowerBI
+ Solución de supervisión de red
+ Solución de Office 365
+ Solución Upgrade Analytics de Windows 10
+ Application Dependency Monitoring
+ Evaluación de actualización
+ Integración de Azure Portal
  - La selección de las cuentas de Azure Storage que se van a supervisar se debe realizar con plantillas de Resource Manager o PowerShell
  - La selección de las máquinas virtuales para habilitar el agente de Log Analytics se debe realizar con plantillas de Resource Manager o PowerShell
+ Supervisión de Linux
+ Aplicaciones móviles de OMS
+ Extensión de VM de Microsoft Monitoring Agent
+ Extensión de VM de Agente de Linux de OMS
+ Datos de uso
+ Correos electrónicos de alerta y corrección con Azure Automation

Las características siguientes de Log Analytics se comportan de otra manera en Azure Government:

+ El agente de Windows se debe descargar en el portal de Log Analytics para Azure Government.
+ La solución Seguridad y auditoría no es compatible con la detección de direcciones IP malintencionadas.
+ Cargar datos con la API de Recopilador de datos requiere el uso de la dirección URL de Azure Government.

### Preguntas más frecuentes

+ ¿Se pueden migrar datos desde Log Analytics en Azure público a Azure Government?
  - No, no se pueden migrar datos ni su área de trabajo desde Azure público a Azure Government.
+ ¿Se puede cambiar entre las áreas de trabajo de Azure público y Azure Government desde el portal de Log Analytics de OMS?
  - No. Los portales de Azure público y Azure Government son independientes y no comparten información.

Para más información, consulte [Documentación pública de Log Analytics](../log-analytics/log-analytics-overview.md).

## Pasos siguientes

Para información complementaria y actualizaciones suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>

<!---HONumber=AcomDC_0928_2016-->