<properties 
	pageTitle="Aplicaciones de empresa multicanal" 
	description="Información general sobre cómo una aplicación multicanal abarca los recursos locales y los servicios de software basados en la nube." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# Creación de aplicaciones multicanal para la empresa

## Información general

Las aplicaciones empresariales multicanal presentan los datos a los clientes mediante varias tecnologías. Los consumidores de API, móviles y de web empresarial recuperan y procesan la información de diversos orígenes. Estos orígenes abarcan sistemas internos que se ejecutan en local así como servicios basados en la nube.

Las aplicaciones empresariales también requieren el acceso seguro, con empleados y partners de negocio conectándose a los datos mediante identidades seguras bajo el control directo de la empresa.

Las aplicaciones empresariales que se ejecutan en el Servicio de aplicaciones de Azure (AAS) pueden proporcionar todas estas capacidades.

La aplicación de viajes de empleados de ejemplo que se muestra a continuación presenta una aplicación empresarial multicanal protegida a través de Azure Active Directory (AAD), que se integra tanto con recursos locales como con servicios de Software-as-a-Service (SaaS), como Office 365 y Salesforce.

## <a name="acceptablefiles"></a>Control del acceso con Azure Active Directory

Los usuarios de la aplicación de viajes primero deben autenticarse en un Active Directory corporativo. Se han realizado algunos pasos para configurar la aplicación para que utilice Azure Active Directory (AAD):

* Se ha creado un directorio de Azure Active Directory para la empresa.
* El directorio de Active Directory local de la empresa se ha federado con Azure Active Directory.
* Por último, la aplicación se ha registrado con AAD mediante la característica de integración sencilla de AAD del Servicio de aplicaciones de Azure. 

El resultado final es que la aplicación solicita a los usuarios que inicien sesión en AAD (y por extensión, en el AD corporativo).
	
![Inicio de sesión de AAD][AADLogin]

Para obtener más información acerca de cómo configurar la integración de AAS con AAD, consulte [Integración de AAS con AAD][AASIntegrationwithAAD].

## <a name="acceptablefiles"></a>Obtención de acceso a recursos locales

Cuando el usuario inicia sesión en AAD, verá una lista de los viajes corporativos planeados. Como la aplicación web se ejecuta en Microsoft Azure, necesita una manera de tener acceso al servidor SQL Server local que contiene esta información.

![Datos de SQL Server local][DatafromOnpremisesSqlServer]

La aplicación está configurada con la característica de integración de la red virtual de punto a sitio. Esto permite a la aplicación usar la lógica de acceso a datos estándar (en este caso, Entity Framework) para tener acceso d manera transparente a un servidor SQL Server remoto que se ejecuta dentro de la red corporativa.

Para obtener más información sobre la integración de la red virtual punto a sitio, consulte [Integración de red virtual][VNETIntegration].

## <a name="acceptablefiles"></a>Integración con Office 365

Cada vez que un empleado crea un registro de viaje, la aplicación web crea una solicitud de viaje en una lista de SharePoint Online. La aplicación muestra un vínculo que pueden usar los empleados para obtener acceso fácilmente a la lista de Sharepoint:

![Vínculo de la lista de SharePoint][SharepointListLink]

Al hacer clic en el vínculo se desplazará automáticamente a la lista de SharePoint. Como los empleados inician sesión en su AAD corporativa, se autentican de manera transparente en Office 365 con el token de seguridad emitido por AAD.

![Lista de SharePoint][SharepointList]

La aplicación web también crea un documento de viaje en una biblioteca de documentos de Sharepoint Online.

![Biblioteca de documentos de SharePoint][SharepointDocumentLibrary]

Los recursos creados en SharePoint Online permiten que la aplicación web aproveche las capacidades de Office 365. Por ejemplo un flujo de trabajo de aprobación/decisión podría desencadenarse cada vez que se crea un elemento en la lista de Sharepoint.

Para obtener más información sobre la integración con Office 365, consulte [Integración con Office 365][Office365Integration]

## <a name="acceptablefiles"></a>Integración con servicios SaaS

Hoy en día, las empresas utilizan una variedad de servicios SaaS y necesitan interactuar con datos SaaS desde otras aplicaciones. La aplicación de viajes muestra un ejemplo de este escenario. Actualiza la información de la cuenta del cliente en Salesforce cada vez que un empleado planea viajar en una cuenta de cliente.

![Integración de Salesforce][SalesforceIntegration]

La cuenta de Salesforce de la corporación se configura con AAD para permitir la autenticación transparente con Salesforce mediante el uso de credenciales de AAD. Como resultado, cuando los empleados inician sesión en la aplicación de viajes con AAD, la aplicación puede leer y escribir los datos almacenados en Salesforce.

Para obtener más información sobre la integración con SaaS, consulte [Integración con SaaS][SaaSIntegration]

## <a name="NextSteps"></a>Pasos siguientes

Para obtener más información, consulte [Servicios de aplicaciones de Azure][AzureApplicationServices]
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: ../app-service-cloud-app-platform.md
[SaaSIntegration]: ../app-service-cloud-app-platform.md
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png
<!--HONumber=54--> 