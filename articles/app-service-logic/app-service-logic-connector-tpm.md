<properties 
   pageTitle="Administración de socios comerciales de BizTalk" 
   description="Administración de socios comerciales de BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/19/2015"
   ms.author="rajram"/>

#Administración de socios comerciales de BizTalk
El servicio Administración de socios comerciales (TPM) de Microsoft Azure permite definir y mantener relaciones de negocio a negocio, como socios y contratos, junto con artefactos asociados como esquemas y certificados. Estas relaciones se pueden aplicar mediante servicios de API relacionados como X 12, EDIFACT y AS2.

La aplicación de API TPM es el requisito básico del conector AS2 y de las aplicaciones de API X12 o EDIFACT.

##Requisitos previos
- Base de datos de SQL Azure en blanco: debe crear primero una base de datos de SQL Azure en blanco antes de crear una nueva aplicación de API de TPM.

##Descripción de socios, contratos y perfiles
Para obtener más información sobre los contratos de socios comerciales, haga clic en [aquí][1]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=August15_HO8-->