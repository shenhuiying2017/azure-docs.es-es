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


#  Administración y seguridad de Azure Government

##  Almacén de claves

La información siguiente identifica el límite de Azure Government para el Almacén de claves de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos cifrados con una clave del Almacén de claves de Azure pueden contener datos regulados o controlados. | Los metadatos del Almacén de claves de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener el Almacén de claves. No inserte datos regulados o controlados en los siguientes campos: nombres de grupos de recursos, nombres del Almacén de claves o nombres de suscripciones. |

El Almacén de claves está disponible en Azure Government con carácter general. Como es público, no hay ninguna extensión, por lo que solo está disponible mediante PowerShell y la CLI.

Para más información, consulte la [documentación pública del Almacén de claves de Azure](/key-vault-get-started).

Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>.

<!---HONumber=AcomDC_0831_2016-->