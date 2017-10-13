---
title: Uso de certificados con Enterprise Integration Pack | Microsoft Docs
description: Uso de certificados con Enterprise Integration Pack | Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0570aab14283b38f9efcc50636f0c0c1c8e3ed13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Información sobre los certificados y Enterprise Integration Pack
## <a name="overview"></a>Información general
Enterprise Integration Pack utiliza certificados para proteger las comunicaciones B2B. Puede emplear dos tipos de certificados en las aplicaciones de integración de empresas:

* Certificados públicos, que deben comprarse a una entidad de certificación (CA).
* Certificados privados, que puede emitir usted mismo. Estos certificados se suelen denominar "certificados autofirmados".

## <a name="what-are-certificates"></a>¿Qué son los certificados?
Los certificados son documentos digitales que comprueban la identidad de los participantes de las comunicaciones electrónicas, además de para proteger estas actividades.

## <a name="why-use-certificates"></a>¿Por qué se utilizan los certificados?
A veces, hay que conservar la confidencialidad de las comunicaciones B2B. Enterprise Integration Pack usa certificados para proteger estas comunicaciones de dos maneras:

* Cifrando el contenido de los mensajes
* Firmando digitalmente los mensajes  

## <a name="upload-a-public-certificate"></a>Carga de un certificado público

Para usar un *certificado público* en las Aplicaciones lógicas con funcionalidades B2B, antes hay que cargarlo en la cuenta de integración.  

Después de cargar un certificado, estará disponible para que pueda proteger los mensajes B2B al definir sus propiedades en los [contratos](logic-apps-enterprise-integration-agreements.md) que cree.  

A continuación, figuran los pasos detallados para cargar certificados públicos en una cuenta de integración después de haber iniciado sesión en Azure Portal:

1. Seleccione **Más servicios** y escriba **integración** en el cuadro de búsqueda de filtro. Seleccione **Cuentas de integración** en la lista de resultados.     
![Seleccionar Examinar](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Seleccione la cuenta integración en la que vaya a agregar el certificado.  
![Selección de la cuenta integración en la que vaya a agregar el certificado](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Seleccione el icono de **Certificados** .  
![Selección del icono de Certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. En la hoja **Certificados** que se abre, haga clic en el botón **Agregar**.   
![Seleccionar el botón Agregar](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Escriba el **nombre** del certificado y seleccione el tipo de certificado **público** en la lista desplegable.  
6. Seleccione el icono de carpeta que se encuentra a la derecha del cuadro de texto **Certificado**. Cuando se abre el selector de archivos, busque y seleccione el archivo de certificado que quiera cargar en la cuenta de integración.
7. Seleccione el certificado y, luego, haga clic en el botón **Aceptar** del selector de archivos. Con esto, se valida y carga el certificado en la cuenta de integración.
8. Por último, en la hoja **Agregar certificado**, haga clic en el botón **Aceptar**.  
![Seleccionar el botón Aceptar](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Seleccione el icono de **Certificados** . Debería ver el certificado que acaba de agregar.  
![Consulta del nuevo certificado](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>Carga de un certificado privado

Para usar un *certificado privado* en las aplicaciones lógicas con funcionalidades B2B, puede cargar un certificado privado en su cuenta de integración realizando los pasos siguientes

1. [Cargue la clave privada en Key Vault](../key-vault/key-vault-get-started.md "Información acerca de Key Vault") y especifique un **nombre de clave**. 
   
   > [!TIP]
   > Debe autorizar Logic Apps para poder realizar operaciones en Key Vault. Puede conceder acceso a la entidad de servicio Logic Apps mediante este comando de PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

Una vez realizado el paso anterior, agregue un certificado privado a la cuenta de integración.

A continuación, figuran los pasos detallados para cargar certificados privados en una cuenta de integración después de haber iniciado sesión en Azure Portal:  
 
1. Seleccione la cuenta de integración en la que desea agregar el certificado y haga clic en el icono **Certificados**.  
![Selección del icono de Certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. En la hoja **Certificados** que se abre, haga clic en el botón **Agregar**.   
![Seleccionar el botón Agregar](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Escriba el **nombre** del certificado y seleccione el tipo de certificado **privado** en la lista desplegable.   
4. Seleccione el icono de carpeta que se encuentra a la derecha del cuadro de texto **Certificado**. Cuando se abra el selector de archivos, busque el certificado público correspondiente que desee cargar en la cuenta de integración.   
   
   > [!Note]
   > Siempre que se agrega un certificado privado, es importante agregar el correspondiente certificado público para que aparezca en la configuración de envío y recepción del [contrato AS2](logic-apps-enterprise-integration-as2.md) y esté disponible para firmar y cifrar los mensajes.
   > 
   >   

5. Seleccione un valor para **Grupo de recursos**, **Key Vault** y **Nombre de clave**, y haga clic en el botón **Aceptar**.  
![Agregar certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Seleccione el icono de **Certificados** . Debería ver el certificado que acaba de agregar.
![Consulta del nuevo certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [Creación de un contrato B2B](logic-apps-enterprise-integration-agreements.md)  
* [Más información sobre Key Vault](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves")  

