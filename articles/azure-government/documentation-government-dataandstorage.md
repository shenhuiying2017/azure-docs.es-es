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
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Datos y almacenamiento de Azure Government

##  Almacenamiento

La siguiente información identifica el límite de Azure Government para Almacenamiento de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Los datos especificados, almacenados y procesados dentro de un producto de Almacenamiento de Azure pueden contener datos controlados para exportación. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. | Los metadatos de Almacenamiento de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento. No inserte datos regulados o controlados en los siguientes campos: grupos de recursos, nombres de implementación, nombres de recursos o etiquetas de recursos.  

Para más información, consulte la documentación pública de Almacenamiento de Azure <a href=https://azure.microsoft.com/documentation/services/storage/> </a>.

Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>.

##  Base de datos SQL

La siguiente información identifica el límite de Azure Government para Almacenamiento de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos almacenados y procesados en Microsoft Azure SQL pueden contener datos regulados por Azure Government. Debe utilizar herramientas de base de datos para la transferencia de datos regulados por Azure Government. | Los metadatos de SQL de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento. No inserte datos regulados o controlados en los siguientes campos: nombres de base de datos, nombres de suscripciones, grupos de recursos, inicios de sesión de administrador, nombres de implementaciones, nombres de recursos o etiquetas de recursos.

Base de datos SQL v11 está disponible en Azure Government con carácter general.

Consulte <a href="https://msdn.microsoft.com/es-ES/library/bb510589.aspx"> Security Center para el motor de Base de datos SQL </a> y <a href="https://azure.microsoft.com/documentation/services/sql-database/"> Documentación pública de Base de datos SQL de Azure </a> para obtener instrucciones adicionales sobre la configuración de visibilidad de metadatos y los procedimientos recomendados de protección.

Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>.

<!---HONumber=AcomDC_0831_2016-->