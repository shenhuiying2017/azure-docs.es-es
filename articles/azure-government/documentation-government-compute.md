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


#  Proceso de Azure Government

##  Máquinas virtuales


La siguiente información identifica el límite de Azure Government para Máquinas virtuales de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Los datos especificados, almacenados y procesados dentro de una máquina virtual pueden contener datos controlados para exportación. Archivos binarios que se ejecutan dentro de Máquinas virtuales de Azure. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Cadenas de conexión SQL. Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. | Los metadatos no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su instancia de Máquinas virtuales de Azure. No inserte datos regulados o controlados en los siguientes campos: nombres de roles de inquilino, grupos de recursos, nombres de implementación, nombres de recursos o etiquetas de recursos.  

Para más información, consulte la documentación pública de Máquinas virtuales de Azure <a href=https://azure.microsoft.com/documentation/services/virtual-machines/windows/> </a>.

Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>.

<!---HONumber=AcomDC_0831_2016-->