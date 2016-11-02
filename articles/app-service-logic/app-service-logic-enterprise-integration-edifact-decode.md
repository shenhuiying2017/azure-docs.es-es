<properties 
	pageTitle="Información acerca del conector Decode EDIFACT Message de Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Sepa cómo usar partners con las Aplicaciones lógicas y Enterprise Integration Pack" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# Introducción a Decode EDIFACT Message

Valida las propiedades de EDI y las específicas de los partners, genera un documento XML para cada conjunto de transacciones, así como la confirmación de transacción procesada.

## Creación de la conexión

### Requisitos previos

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)

* Para usar el conector Decode EDIFACT Message, se requiere una cuenta de integración. Vea los detalles sobre cómo crear una [cuenta de integración](./app-service-logic-enterprise-integration-create-integration-account.md), [partners](./app-service-logic-enterprise-integration-partners.md) y [un acuerdo de EDIFACT](./app-service-logic-enterprise-integration-edifact.md).

### Conexión con Encode EDIFACT Message mediante los siguientes pasos:

1. En [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](./app-service-logic-create-a-logic-app.md), encontrará un ejemplo.

2. Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica, como uno de solicitud. En el diseñador de aplicaciones lógicas, agregue un desencadenador y una acción. Seleccione Mostrar las API administradas por Microsoft en la lista desplegable y escriba "EDIFACT" en el cuadro de búsqueda. Seleccione Decode EDIFACT Message

	![buscar EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
	
3. Si no ha creado anteriormente ninguna conexión a la cuenta de integración, se le pedirán los detalles de conexión.

	![crear cuenta de integración](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)

4. Escriba los detalles de la cuenta de integración. Las propiedades con un asterisco son obligatorias.

	| Propiedad | Detalles |
	| -------- | ------- |
	| Connection Name (Nombre de la conexión)* | Escriba un nombre para la conexión. |
	| Integration Account (Cuenta de integración)* | Escriba el nombre de la cuenta de integración. Asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la misma ubicación de Azure. |

	Una vez completados, los detalles de la conexión presentan un aspecto similar al siguiente.

	![cuenta de integración creada](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)

5. Seleccione **Crear**.

6. Observe que se ha creado la conexión.

	![detalles de conexión de cuenta de integración](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)

7. Seleccione EDIFACT flat file message to decode (Mensaje de archivo plano de EDIFACT que se va a descodificar).

	![especificar campos obligatorios](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)

## Funciones de EDIFACT Decode

* Resuelve el acuerdo, para lo que hace coincidir el calificador e identificador del remitente con el calificador e identificador del receptor.
* Divide varios intercambios de un único mensaje en intercambios independientes.
* Valida el sobre con el acuerdo de socio comercial.
* Desensambla el intercambio.
* Valida las propiedades de EDI y específicas del partner, lo que incluye
	* Validación de la estructura del sobre de intercambio.
	* Validación del esquema del sobre con el esquema de control.
	* Validación del esquema de los elementos de datos del conjunto de transacciones con el esquema de mensaje
	* Validación de EDI realizada en los elementos de datos del conjunto de transacciones
* Comprueba que los números de control de intercambio, grupo y conjunto de transacciones no están duplicados (si está configurado).
	* Comprueba el número de control del intercambio con los intercambios recibidos anteriormente.
	* Comprueba el número de control del grupo con otros números de control de grupo del intercambio.
	* Comprueba el número de control del conjunto de transacciones con otros números de control del conjunto de transacciones de dicho grupo.
* Genera un documento XML para cada conjunto de transacciones.
* Convierte todo el intercambio a XML.
	* Dividir intercambio como conjuntos de transacciones (suspender conjuntos de transacciones en caso de error): analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, EDIFACT Decode suspende solo esos conjuntos de transacciones.
	* Dividir intercambio como conjuntos de transacciones (suspender intercambio en caso de error): analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, EDIFACT Decode suspende todo el intercambio.
	* Conservar intercambio (suspender conjuntos de transacciones en caso de error): crea un documento XML para todo el intercambio por lotes. EDIFACT Decode suspende solo los conjuntos de transacciones que no superan la validación, pero no deja de procesar los restantes conjuntos de transacciones.
	* Conservar intercambio (suspender intercambio en caso de error): crea un documento XML para todo el intercambio por lotes. Si uno o varios conjuntos de transacciones del intercambio no superan la validación, EDIFACT Decode suspende todo el intercambio.
* Genera una confirmación técnica (control) o funcional (si esta opción está configurada).
	* Una confirmación técnica o ACK CONTRL informa de los resultados de una comprobación sintáctica de todo el intercambio recibido.
	* Una confirmación funcional confirma la aceptación o el rechazo de un intercambio recibido o un grupo

## Pasos siguientes

[Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0824_2016-->