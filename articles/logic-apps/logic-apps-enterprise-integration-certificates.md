---
title: "Protección de mensajes B2B con certificados en Azure Logic Apps | Microsoft Docs"
description: "Adición de certificados para proteger mensajes B2B con Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 708bdcddede71186c48ae7d4034cc9df0bd61391
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="secure-b2b-messages-with-certificates"></a>Protección de mensajes B2B con certificados

A veces se necesita garantizar la confidencialidad de las comunicaciones B2B. Para ayudarle a proteger las comunicaciones B2B para sus aplicaciones de integración empresarial (en particular, las aplicaciones lógicas), puede añadir certificados a su cuenta de integración. Los certificados son documentos digitales que comprueban la identidad de los participantes de las comunicaciones electrónicas.
Los certificados permiten proteger la comunicación de las siguientes maneras:

* Cifrado del contenido de los mensajes.
* Firma digital de los mensajes.  

Puede usar los siguientes certificados en las aplicaciones de integración empresarial:

* Certificados públicos, que deben comprarse a una entidad de certificación (CA).
* Certificados privados, que puede emitir usted mismo. Estos certificados se suelen denominar "certificados autofirmados".

## <a name="upload-a-public-certificate"></a>Carga de un certificado público

Para usar un *certificado público* en las aplicaciones lógicas con funcionalidades B2B, antes hay que cargarlo en la cuenta de integración. Después de definir las propiedades en los [contratos](logic-apps-enterprise-integration-agreements.md) que cree, el certificado estará disponible para que pueda proteger los mensajes B2B.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, escriba "integración" y, después, seleccione **Cuentas de integración**.

   ![Búsqueda de la cuenta de integración](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. En **Cuentas de integración**, seleccione la cuenta de integración en la que desea añadir el certificado.

   ![Selección de la cuenta integración en la que vaya a agregar el certificado](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Seleccione el icono **Certificados**.  

   ![Elección de "Certificados"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. En **Certificados**, elija **Agregar**.

   ![Elección de "Agregar"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. En **Agregar certificado**, proporcione los detalles de su certificado.
   
   1. Escriba el **nombre** de su certificado. Para el tipo de certificado, seleccione **Público**.

   2. Seleccione el icono de carpeta que se encuentra a la derecha del cuadro **Certificado**. 
   Busque y seleccione el archivo de certificado que desee cargar. 
   Cuando termine, elija **Aceptar**.

      ![Carga de un certificado público](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure carga el certificado después de validar la selección.

   ![Visualización del nuevo certificado](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Carga de un certificado privado

Para usar un *certificado privado* en las aplicaciones lógicas con funcionalidades B2B, antes hay que cargarlo en la cuenta de integración. También debe tener una clave privada que añadirá primero a [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Después de definir las propiedades en los [contratos](logic-apps-enterprise-integration-agreements.md) que cree, el certificado estará disponible para que pueda proteger los mensajes B2B.

> [!NOTE]
> En el caso de los certificados privados, asegúrese de añadir un certificado público correspondiente para que aparezca en la configuración de envío y recepción del [contrato AS2](logic-apps-enterprise-integration-as2.md) para la firma y el cifrado de mensajes.

1. [Añada la clave privada a Azure Key Vault](../key-vault/key-vault-get-started.md#add) y especifique un **nombre de clave**.
   
2. Autorice a Azure Logic Apps para que pueda realizar operaciones en Azure Key Vault. Para conceder acceso a la entidad de seguridad de servicio de Logic Apps, use el comando de PowerShell [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), como en el ejemplo siguiente:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Inicie sesión en el [Azure Portal](https://portal.azure.com).

4. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, escriba "integración" y, después, seleccione **Cuentas de integración**.

   ![Búsqueda de la cuenta de integración](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. En **Cuentas de integración**, seleccione la cuenta de integración en la que desea añadir el certificado.

6. Seleccione el icono **Certificados**.  

   ![Selección del icono Certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. En **Certificados**, elija **Agregar**.   

   ![Selección del botón Agregar](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. En **Agregar certificado**, proporcione los detalles de su certificado.
   
   1. Escriba el **nombre** de su certificado. Para el tipo de certificado, seleccione **Privado**.

   2. Seleccione el icono de carpeta que se encuentra a la derecha del cuadro **Certificado**. 
   Busque y seleccione el archivo de certificado que desee cargar. 
   Además, seleccione el **grupo de recursos**, el **almacén de claves** y el **nombre de clave**. 
   Cuando termine, elija **Aceptar**.

      ![Agregar certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure carga el certificado después de validar la selección.

   ![Visualización del nuevo certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>pasos siguientes

* [Creación de un contrato B2B](logic-apps-enterprise-integration-agreements.md)
