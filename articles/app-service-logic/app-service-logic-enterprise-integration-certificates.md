
<properties
    pageTitle="Uso de certificados con Enterprise Integration Pack | Microsoft Azure"
    description="Sepa cómo usar certificados con las Aplicaciones lógicas y Enterprise Integration Pack."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="deonhe"/>


# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Información sobre los certificados y Enterprise Integration Pack

## <a name="overview"></a>Información general
Enterprise Integration Pack utiliza certificados para proteger las comunicaciones B2B. Puede emplear dos tipos de certificados en las aplicaciones de integración de empresas:

- Certificados públicos, que deben comprarse a una entidad de certificación (CA).
- Certificados privados, que puede emitir usted mismo. Estos certificados se suelen denominar "certificados autofirmados".


## <a name="what-are-certificates?"></a>¿Qué son los certificados?
Los certificados son documentos digitales que comprueban la identidad de los participantes de las comunicaciones electrónicas, además de para proteger estas actividades.

## <a name="why-use-certificates?"></a>¿Por qué se utilizan los certificados?
A veces, hay que conservar la confidencialidad de las comunicaciones B2B. Enterprise Integration Pack usa certificados para proteger estas comunicaciones de dos maneras:

- Cifrando el contenido de los mensajes
- Firmando digitalmente los mensajes  

## <a name="how-do-you-upload-certificates?"></a>¿Cómo se cargan certificados?

### <a name="public-certificates"></a>Certificados públicos
Para usar un *certificado público* en las Aplicaciones lógicas con funcionalidades B2B, antes hay que cargarlo en la cuenta de integración. Para usar un *certificado autofirmado*, primero debe cargarlo en [Azure Key Vault](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves").

Después de cargar un certificado, estará disponible para que pueda proteger los mensajes B2B al definir sus propiedades en los [contratos](./app-service-logic-enterprise-integration-agreements.md) que cree.  

A continuación, figuran los pasos detallados para cargar certificados públicos en una cuenta de integración después de haber iniciado sesión en Azure Portal:

1. Seleccione **Examinar**.  
    ![Seleccionar Examinar](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  

2. Escriba **integración** en el cuadro de búsqueda de filtros y seleccione **Cuentas de integración** en la lista de resultados.     
    ![Seleccionar Cuentas de integración](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Seleccione la cuenta integración en la que vaya a agregar el certificado.  
    ![Selección de la cuenta integración en la que vaya a agregar el certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4.  Seleccione el icono de **Certificados** .  
    ![Selección del icono de Certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. En la hoja **Certificados** que se abre, haga clic en el botón **Agregar**.
    ![Seleccionar el botón Agregar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Escriba el **nombre** del certificado y, luego, seleccione el tipo de certificado (en este ejemplo, se usa el tipo de certificado público). Después, seleccione el icono de la carpeta que se encuentra a la derecha del cuadro de texto **Certificado**.

7. Cuando se abre el selector de archivos, busque y seleccione el archivo de certificado que quiera cargar en la cuenta de integración.

8. Seleccione el certificado y, luego, haga clic en el botón **Aceptar** del selector de archivos. Con esto, se valida y carga el certificado en la cuenta de integración.

8. Por último, en la hoja **Agregar certificado**, haga clic en el botón **Aceptar**.  
    ![Seleccionar el botón Aceptar](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  

9. Dentro de un minuto aproximadamente verá una notificación que indica que ha finalizado la carga del certificado.

10. Seleccione el icono de **Certificados** . Debería ver el certificado que acaba de agregar.  
    ![Consulta del nuevo certificado](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificados privados
Puede cargar certificados privados en la cuenta de integración siguiendo estos pasos:  

1. [Cargue la clave privada en Key Vault](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves").  

    > [AZURE.TIP] Debe autorizar el servicio Logic Apps de Azure App Service para realizar operaciones en Key Vault. Puede conceder acceso a la entidad de servicio Logic Apps mediante este comando de PowerShell: `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  

2. Cree un certificado privado.  

3. Cargue el certificado privado en su cuenta de integración.

Cuando haya realizado los pasos anteriores, puede usar el certificado privado para crear contratos.

A continuación, figuran los pasos detallados para cargar certificados privados en una cuenta de integración después de haber iniciado sesión en Azure Portal:  

1. Seleccione **Examinar**.  
    ![Cargar certificados privados en una cuenta de integración](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

2. Escriba **integración** en el cuadro de búsqueda de filtros y seleccione **Cuentas de integración** en la lista de resultados.     
    ![Seleccionar Cuentas de integración](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  

3. Seleccione la cuenta integración en la que vaya a agregar el certificado.  
    ![Selección de la cuenta integración en la que vaya a agregar el certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4. Seleccione el icono de **Certificados** .  
    ![Seleccionar el icono de Certificados.](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  

5. En la hoja **Certificados** que se abre, haga clic en el botón **Agregar**.
    ![Seleccionar el botón Agregar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Escriba el **nombre** del certificado y, luego, seleccione el tipo de certificado (en este ejemplo, se usa el tipo de certificado público). Después, seleccione el icono de la carpeta que se encuentra a la derecha del cuadro de texto **Certificado**.

7. Cuando se abre el selector de archivos, busque y seleccione el archivo de certificado que quiera cargar en la cuenta de integración.

8. Cuando haya seleccionado el certificado, haga clic en el botón **Aceptar** del selector de archivos. Esta acción valida y carga el certificado en la cuenta de integración.

9. Por último, en la hoja **Agregar certificado**, haga clic en el botón **Aceptar**.  
    ![Agregar certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  

10. Dentro de un minuto aproximadamente verá una notificación que indica que ha finalizado la carga del certificado.

11. Seleccione el icono de **Certificados** . Debería ver el certificado que acaba de agregar.
    ![Consulta del nuevo certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Después de cargar un certificado, estará disponible para que pueda proteger los mensajes B2B al definir sus propiedades en los [contratos](./app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Pasos siguientes
- [Creación de una aplicación lógica que utilice características de B2B](./app-service-logic-enterprise-integration-b2b.md)  
- [Creación de un contrato B2B](./app-service-logic-enterprise-integration-agreements.md)  
- [Más información sobre Key Vault](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves")  



<!--HONumber=Oct16_HO2-->


