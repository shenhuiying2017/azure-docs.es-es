<properties 
	pageTitle="Uso de certificados con Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure" 
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
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Información sobre los certificados y Enterprise Integration Pack

## Información general
Enterprise Integration Pack utiliza certificados para proteger las comunicaciones B2B. Puede emplear dos tipos de certificados en las aplicaciones de integración de empresas:
- Certificados públicos, que deben comprarse a una entidad de certificación (CA).
- Certificados privados, que puede emitir usted mismo. A veces, se denominan "certificados autofirmados".

## ¿Qué son los certificados?
Los certificados son documentos digitales que se utilizan para comprobar la identidad de los participantes de las comunicaciones electrónicas, además de para proteger estas actividades.

## ¿Por qué se utilizan los certificados?
A veces, hay que conservar la confidencialidad de las comunicaciones B2B. Enterprise Integration Pack usa certificados para proteger estas comunicaciones de dos maneras:
- Cifrando el contenido de los mensajes
- Firmando digitalmente los mensajes

## ¿Cómo se cargan certificados?

### Certificado público
Para usar un **certificado público** en las Aplicaciones lógicas con funcionalidades B2B, antes hay que cargarlo en la cuenta de integración. Asimismo, para utilizar un **certificado autofirmado**, primero debe cargarlo en el [Almacén de claves](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves").

Después de cargar un certificado, estará disponible para que pueda proteger los mensajes B2B al definir sus propiedades en los [contratos](./app-service-logic-enterprise-integration-agreements.md).

A continuación, figuran los pasos detallados para cargar certificados públicos en una cuenta de integración después de haber iniciado sesión en el Portal de Azure:
1. Seleccione **Examinar**. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Seleccione la **cuenta integración** en la que vaya a agregar el certificado. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Seleccione el icono de **Certificados**. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Haga clic en el botón **Agregar** de la hoja Certificados que se abre. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Escriba un **nombre** para el certificado, seleccione el tipo de certificado (en este ejemplo, se usa el tipo de certificado público). Después, seleccione el icono de carpeta del lado derecho del cuadro de texto **Certificado**. Esta acción abre el selector de archivos que permite buscar y seleccionar el archivo de certificado que quiera cargar en la cuenta de integración. Cuando haya seleccionado el certificado, haga clic en el botón **Aceptar** del selector de archivos. Con esto, se valida y carga el certificado en la cuenta de integración. Finalmente, en la hoja **Agregar certificado**, haga clic en el botón **Aceptar**. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)
7. Dentro de un minuto verá una notificación que indica que ha finalizado la carga del certificado.
8. Seleccione el icono de **Certificados**. Tras esto, se actualiza la página y debería ver el certificado que acaba de agregar: ![](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### Certificado privado
En la cuenta de integración también puede cargar certificados privados. Para ello, tendrá que completar los siguientes pasos:
1. [Agregar la clave privada al Almacén de claves](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves")
2. Crear un certificado privado Cargar en certificado privado en la cuenta de integración

Cuando haya realizado los pasos anteriores, puede usar el certificado privado para crear contratos.

A continuación, figuran los pasos detallados para cargar certificados privados en una cuenta de integración después de haber iniciado sesión en el Portal de Azure:
1. Seleccione **Examinar**. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Seleccione la **cuenta integración** en la que vaya a agregar el certificado. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Seleccione el icono de **Certificados**. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Haga clic en el botón **Agregar** de la hoja Certificados que se abre. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Escriba un **nombre** para el certificado, seleccione el tipo de certificado (en este ejemplo, se usa el tipo de certificado público). Después, seleccione el icono de carpeta del lado derecho del cuadro de texto **Certificado**. Esta acción abre el selector de archivos que permite buscar y seleccionar el archivo de certificado que quiera cargar en la cuenta de integración. Cuando haya seleccionado el certificado, haga clic en el botón **Aceptar** del selector de archivos. Con esto, se valida y carga el certificado en la cuenta de integración. Finalmente, en la hoja **Agregar certificado**, haga clic en el botón **Aceptar**. ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)
7. Dentro de un minuto verá una notificación que indica que ha finalizado la carga del certificado.
8. Seleccione el icono de **Certificados**. Tras esto, se actualiza la página y debería ver el certificado que acaba de agregar: ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

Después de cargar un certificado, estará disponible para que pueda proteger los mensajes B2B al definir sus propiedades en los [contratos](./app-service-logic-enterprise-integration-agreements.md).


## Pasos siguientes
- [Creación de un contrato B2B](./app-service-logic-enterprise-integration-agreements.md)
- [Creación de una Aplicación lógica mediante características B2B](./app-service-logic-enterprise-integration-b2b.md)
- [Más información sobre el Almacén de claves](../key-vault/key-vault-get-started.md "Información sobre el Almacén de claves")

<!---HONumber=AcomDC_0803_2016-->