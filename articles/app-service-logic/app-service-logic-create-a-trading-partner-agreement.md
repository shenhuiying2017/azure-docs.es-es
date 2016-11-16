---
title: "Creación de un contrato entre socios comerciales en Azure App Service | Microsoft Docs"
description: "Creación de acuerdos entre socios comerciales"
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajram
manager: erikre
editor: 
ms.assetid: 319e46fa-fd81-4730-a742-768bf1676972
ms.service: logic-apps
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/23/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e41ac0e91bd66fbc7df08b4397e78377021fcbca


---
# <a name="creating-a-trading-partner-agreement"></a>Creación de un acuerdo entre socios comerciales
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Los socios comerciales son las entidades implicadas en comunicaciones B2B (negocio a negocio). Cuando dos asociados establecen una relación, esto se conoce como un *acuerdo*. El contrato definido se basa en la comunicación que los dos socios desean lograr y es específico del protocolo o el transporte. Los distintos protocolos y transportes B2B compatibles con el Servicio de aplicaciones de Azure incluyen:

* AS2 (Applicability Statement 2)
* EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
* X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>Aplicaciones de API de BizTalk que admiten escenarios B2B
Las siguientes aplicaciones de API permiten estas funcionalidades gracias a la experiencia enriquecida e intuitiva que proporciona el Portal de Azure:

## <a name="biztalk-trading-partner-management-tpm"></a>Administración de socios comerciales (TPM) de BizTalk
* Creación y administración de socios, perfiles e identidades
* Almacenamiento y administración de esquemas EDI
* Almacenamiento y administración de certificados (usados en el protocolo AS2)
* Creación y administración de acuerdos de AS2
* Creación y administración de acuerdos de EDIFACT (incluye procesamiento por lotes en el lado de envío)
* Creación y administración de acuerdos de X12 (incluye procesamiento por lotes en el lado de envío)

![][1]

## <a name="as2-connector"></a>Conector AS2
* Ejecuta acuerdos de AS2 como se define en la instancia de la aplicación de API de TPM relacionada
* Presenta información de procesamiento y seguimiento de AS2 para solucionar problemas

## <a name="biztalk-edifact"></a>BizTalk EDIFACT
* Ejecuta acuerdos de ASEDIFACT como se define en la instancia de la aplicación de API de TPM relacionada
* Presenta información de procesamiento y seguimiento de EDIFACT para solucionar problemas
* Proporciona administración de estado de lotes (iniciar y detener) tal y como se define en los acuerdos de EDIFACT en la instancia de aplicación de API de TPM relacionada

## <a name="biztalk-x12"></a>BizTalk X12
* Ejecuta acuerdos de X12 como se define en la instancia de la aplicación de API de TPM relacionada 
* Presenta información de procesamiento y seguimiento de X12 para solucionar problemas
* Proporciona administración de estado de lotes (iniciar y detener) tal y como se define en los acuerdos de X12 en la instancia de aplicación de API de TPM relacionada

Como se indicó anteriormente, las aplicaciones de API AS2, X12 y EDIFACT requieren una aplicación de API de TPM para que funcionen como se espera.

## <a name="getting-started"></a>Introducción
Para crear acuerdos entre socios comerciales:

1. Cree una instancia del conector **Administración de socios comerciales de BizTalk** . Para que esto funcione, se necesita una base de datos SQL en blanco. Antes de iniciar asegúrese de tener una base de datos en blanco disponible y lista para su uso.
2. Cargue esquemas y certificados según lo requieran los acuerdos. Esto se hace examinando la instancia de TPM creada y entrando en la parte "Esquemas" o "Certificados".
3. Vaya a la instancia de TPM creada y entre en la parte **Socios** .
4. Cree socios como desee. Edite también los perfiles según corresponda y agregue las identidades necesarias.
5. Ahora use la parte **Acuerdos** para crear acuerdos. Cuando se crea un contrato, debe seleccionar el protocolo que se utilizará. Las opciones de configuración restantes se basan en el protocolo seleccionado.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png




<!--HONumber=Nov16_HO2-->


