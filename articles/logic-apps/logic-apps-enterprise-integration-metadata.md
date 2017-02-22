---
title: "Metadatos de la cuenta de integración de Azure Logic Apps | Microsoft Docs"
description: "Información general sobre los metadatos de la cuenta de integración"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Metadatos de la cuenta de integración de Azure Logic Apps

## <a name="overview"></a>Información general

Los asociados, los contratos, los esquemas, las asignaciones agregadas a una cuenta de integración, almacenan los metadatos con pares clave y valor. Puede definir metadatos personalizados, que se pueden recuperar en tiempo de ejecución.  En estos momentos los artefactos no tienen la funcionalidad para crear metadatos en la interfaz de usuario; puede usar las API de REST para crearlos.  Los asociados, los contratos y el esquema pueden **editar como JSON**, que permite la entrada de información de metadatos.  En una aplicación de lógica, **Integration Account Artifact LookUp** (Búsqueda de artefactos de la cuenta de integración ) ayuda a recuperar la información de metadatos.

## <a name="how-to-store-metadata"></a>Cómo almacenar los metadatos

1. Cree una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md).   

2. Agregue un [asociado](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner) o un [contrato](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) o un [esquema](logic-apps-enterprise-integration-schemas.md) en la cuenta de integración.

3. Seleccione un asociado o un contrato o un esquema. Seleccione **Edit as JSON** (Editar como JSON) y escriba los detalles de los metadatos.    
![Entrada de los metadatos](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Llamada a **Integration Account Artifact LookUp** desde una aplicación lógica

1. Cree una [aplicación lógica](logic-apps-create-a-logic-app.md).

2. [Vincule](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app) la aplicación lógica con una cuenta de integración.    

3. Cree un desencadenador, por ejemplo, mediante una *solicitud* o *HTTP* antes de buscar **Integration Account Artifact LookUp**.  Busque **integration** (integración) para hallar **Integration Account Artifact LookUp**  
![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image2.png)

3. Seleccione **Integration Account Artifact LookUp**.  

4. Seleccione **Tipo de artefacto** y proporcione el **nombre del artefacto**.  
![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>Ejemplo para recuperar los metadatos de asociados

1. Los metadatos de asociados tiene detalles de la dirección url de enrutamiento    
![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image6.png)

2. En una aplicación lógica, configure **Integration Account Artifact LookUp** y **HTTP**   
![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image4.png)

3. Para recuperar el identificador URI, la vista de código debería ser similar a    
![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los contratos](logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  



<!--HONumber=Feb17_HO1-->


