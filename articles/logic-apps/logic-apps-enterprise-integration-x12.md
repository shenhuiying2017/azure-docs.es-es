---
title: "Creación de un contrato X12 en Azure Logic Apps | Microsoft Docs"
description: "Información acerca de la creación de un contrato de X12 para Enterprise Integration Pack| Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 1a982309fe771c0c244c691ae648bd5f4844a825
ms.openlocfilehash: f7b61b85cadfabde6637e46f0e65108a29901198


---
# <a name="enterprise-integration-with-x12"></a>Integración de empresas con X12

## <a name="prereqs"></a>Requisitos previos
Para poder intercambiar mensajes X12, hay que crear un contrato X12 y almacenarlo en la cuenta de integración. Los siguientes pasos lo guían a lo largo del proceso de creación de un contrato X12.

* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) definida en su suscripción de Azure  
* Al menos dos [asociados](../logic-apps/logic-apps-enterprise-integration-partners.md) configurados con el calificador EDI X12 en identidades de negocio.   
* Se necesita un [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) para cargarlo en la [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="create-an-x12-agreement"></a>Creación de un contrato X12
Después de iniciar sesión en el [Portal de Azure](http://portal.azure.com "Portal de Azure"):  

1. Inicie sesión en [Azure Portal](http://portal.azure.com "Azure Portal").
2. Seleccione **Más servicios** y escriba **integración** en el cuadro de búsqueda de filtro. Seleccione **Cuentas de integración** en la lista de resultados:    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
3. Seleccione la cuenta integración en la que quiera agregar el certificado:    
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)   
4. Seleccione el icono **Acuerdos** . Si no lo ve, agréguelo:     
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
5. Seleccione el botón **Agregar** en la hoja Acuerdos que se abre.   
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)     
6. Escriba el **nombre** del contrato y seleccione los valores de **Tipo de contrato**, **Asociado del host**, **Identidad del host**, **Asociado invitado** e **Identidad del invitado** en la hoja Contratos que se abre.    
![](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

| Propiedad | Descripción |
| --- | --- |
| Nombre |Nombre del contrato |
| Tipo de contrato | Debe ser X12 |
| Host Partner (Partner anfitrión) |Un contrato tiene asociado un partner anfitrión e invitado. El asociado del host representa la organización que configura el contrato. |
| Host Identity (Identidad anfitriona) |Un identificador del asociado del host. |
| Guest Partner (Partner invitado) |Un contrato tiene asociado un partner anfitrión e invitado. El asociado invitado representa la organización que está haciendo negocios con el asociado del host. |
| Guest Identity |Un identificador del asociado invitado. |
| Receive Settings (Configuración de recepción) |Estas propiedades se aplican a todos los mensajes que recibe un contrato |
| Send Settings (Configuración de envío) |Estas propiedades se aplican a todos los mensajes que envía un contrato |  

> [!NOTE]
> La resolución del contrato de X12 depende de la coincidencia entre el calificador e identificador del remitente y el calificador y el identificador del receptor definidos en el asociado y en el mensaje entrante.  Si hay cambios en estos valores en el asociado, edite también el contrato.
> 
> 

## <a name="receive-settings"></a>Receive Settings (Configuración de recepción)

1. Seleccione **Receive Settings** (Configuración de recepción) para configurar cómo se administran los mensajes que se reciben través de este contrato.  
2. El control de la configuración de recepción se divide en las siguientes secciones: Identifiers (Identificadores), Acknowledgement (Confirmación), Schemas (Esquemas), Envelopes (Sobres), Control Numbers (Números de control), Validations (Validaciones) e Internal Settings (Configuración interna). Configure estas propiedades en función del contrato con el asociado con el que intercambiará mensajes. A continuación se ofrece una vista de estos controles; configúrelos en función de cómo desee que este acuerdo identifique y controle los mensajes entrantes  

### <a name="identifiers"></a>Identifiers (Identificadores)

![](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propiedad | Descripción |
| --- | --- |
| ISA1 (Authorization Qualifier) (ISA1 (calificador de autorización)) |Seleccione el valor del calificador de autorización en la lista desplegable. |
| ISA2 |Opcional. Escriba el valor de la información de autorización. Si el valor especificado en ISA1 es distinto de 00, escriba como mínimo un carácter alfanumérico y, como máximo, diez. |
| ISA3 (Security Qualifier) (ISA3 (calificador de seguridad)) |Seleccione el valor del calificador de seguridad en la lista desplegable. |
| ISA4 |Opcional. Escriba el valor de la información de seguridad. Si el valor especificado en ISA3 es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez. |

### <a name="acknowledgments"></a>Agradecimientos

![](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propiedad | Descripción |
| --- | --- |
| TA1 expected (TA1 esperada) |Devuelve una confirmación técnica al remitente del intercambio. |
| FA expected (FA esperada) |Devuelve una confirmación funcional al remitente del intercambio. A continuación, seleccione si desea la confirmación 997 o la 999, en función de las versiones del esquema. |
| Include AK2/IK2 Loop (Incluir bucle AK2/IK2) |Habilita la generación de bucles AK2 en confirmaciones funcionales para conjuntos de transacciones aceptadas |


### <a name="schemas"></a>Esquemas
Elija un esquema para cada tipo de transacción (ST1) y aplicación remitente (GS2). La canalización de recepción desensambla el mensaje entrante haciendo coincidir los valores de ST1 y GS2 del mensaje entrante con los valores que establezca aquí y el esquema del mensaje entrante con el esquema que configure aquí.

![](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propiedad | Descripción |
| --- | --- |
| Versión |Seleccione la versión de X12. |
| Transaction Type (ST01) (Tipo de transacción (ST01)) |Seleccione el tipo de transacción. |
| Sender Application (GS02) (Aplicación remitente (GS02)) |Seleccione la aplicación remitente. |
| Esquema |Seleccione el archivo de esquema que desee enviarnos. Los esquemas se agregan a la cuenta de integración. |

> [!NOTE]
> Configure el [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necesario que se carga en la [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md)
> 
> 

### <a name="envelopes"></a>Envelopes (Sobres)

![](./media/logic-apps-enterprise-integration-x12/x12-34.png) 

| Propiedad | Descripción |
| --- | --- |
| ISA11 Usage (Uso de ISA11) |Utilice este campo para especificar el separador de un conjunto de transacciones:</br></br>Seleccione el identificador Estándar para usar la notación decimal de ".", en lugar de la notación decimal del documento entrante en la canalización de recepción de EDI.</br></br>Seleccione el separador Repetición para especificar el separador de las ocurrencias repetitivas de un elemento de datos simple o una estructura de datos repetida. Por ejemplo, se suele utilizar (^) como separador de repeticiones. En los esquemas HIPAA, solo se puede usar (^). |

### <a name="control-numbers"></a>Control Numbers (Números de control)

![](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propiedad | Descripción |
| --- | --- |
| Disallow Interchange Control Number duplicates (No permitir duplicados del número de control de intercambio) |Bloquea los intercambios de duplicados. Compara el número de control de intercambio (ISA13) con el número de control de intercambio recibido. Si se detecta una coincidencia, la canalización de recepción no procesa el intercambio. Puede especificar el número de días en que se realizará la comprobación indicando el valor pertinente en el *cuadro de comprobación de duplicados de ISA13 cada x días*. |
| Disallow Group control number duplicates (No permitir duplicados del número de control de grupo) |Bloquea los intercambios con números de control de grupo duplicados. |
| Disallow Transaction set control number duplicates (No permitir duplicados del número de control del conjunto de transacciones) |Bloquea los intercambios con números de control de grupo de transacción duplicados. |

### <a name="validations"></a>Validations (Validaciones)

![](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

| Propiedad | Descripción |
| --- | --- |
| Message Type (Tipo de mensaje) |Tipos de mensajes de EDI como, por ejemplo, 890-Purchase Order (850: Pedido de compra) o 999-Implementation Acknowledgement (999: Confirmación de la implementación). |
| EDI Validation (Validación de EDI) |Realiza una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales. |
| Extended Validation (Validación extendida) |Si el tipo de datos no es EDI, lo que se valida es el requisito de elemento de datos, así como las repeticiones permitidas, las enumeraciones y la validación de la longitud del elemento de datos (mín./máx.). |
| Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales) |Se conservan todos los espacios y ceros adicionales que se encuentren a la izquierda o a la derecha. No se eliminan. |
| Trailing Separator Policy (Directiva de separador final) |Genera separadores finales en el intercambio recibido. Entre las opciones se encuentran NotAllowed (No permitidos), Optional (Opcionales) y Mandatory (Obligatorios). |

### <a name="internal-settings"></a>Internal Settings (Configuración interna)

![](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propiedad | Descripción |
| --- | --- |
| Convert implied decimal format Nn to base 10 numeric value (Convertir formato decimal implícito Nn a valor numérico de base 10) |Convierte un número EDI especificado con el formato Nn en un valor numérico de base&10; |
| Create empty XML tags if trailing separators are allowed (Crear etiquetas XML vacías si se permiten separadores finales) |Seleccione esta casilla para que el remitente del intercambio incluya etiquetas XML vacías para los separadores finales. |
| Dividir intercambio como conjuntos de transacciones: suspender conjuntos de transacciones en caso de error|Analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado al conjunto de transacciones. Solo suspende aquellas transacciones en las que se produce un error en la validación |
| Dividir intercambio como conjuntos de transacciones: suspender intercambio en caso de error|Analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado. Suspende todo el intercambio si uno o varios conjuntos de transacciones del intercambio no superan la validación. | 
| Conservar intercambio: suspender conjuntos de transacciones en caso de error |Deja intacto el intercambio y crea un documento XML para todo el intercambio por lotes. Suspende solo los conjuntos de transacciones que no superan la validación, sin dejar de procesar el resto de conjuntos de transacciones. |
| Conservar intercambio: suspender intercambio en caso de error |Deja intacto el intercambio y crea un documento XML para todo el intercambio por lotes.  Suspende todo el intercambio si uno o varios conjuntos de transacciones del intercambio no superan la validación. |

Cuando haya establecido las propiedades de configuración de recepción, seleccione el botón **Aceptar**  
   
El contrato está preparado para controlar los mensajes entrantes que cumplen el esquema seleccionado.

## <a name="send-settings"></a>Send Settings (Configuración de envío)

1. Seleccione **Send Settings** (Configuración de envío) para configurar cómo se controlarán los mensajes enviados mediante este acuerdo.  

2. El control de la configuración de envío se divide en las siguientes secciones: Identifiers (Identificadores), Acknowledgement (Confirmación), Schemas (Esquemas), Envelopes (Sobres), Control Numbers (Números de control), Character Sets and Separators (Juegos de caracteres y separadores) y Validation (Validación).  Configure estas propiedades en función del contrato con el asociado con el que intercambiará mensajes. A continuación se ofrece una vista de estos controles; configúrelos en función de cómo desee que este acuerdo identifique y controle los mensajes salientes.


### <a name="identifiers"></a>Identifiers (Identificadores)

![](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propiedad | Descripción |
| --- | --- |
| ISA1 (Authorization Qualifier) (ISA1 (calificador de autorización)) |Seleccione el valor del calificador de autorización en la lista desplegable. |
| ISA2 |Escriba el valor de la información de autorización. Si el valor especificado es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez. |
| ISA3 (Security Qualifier) (ISA3 (calificador de seguridad)) |Seleccione el valor del calificador de seguridad en la lista desplegable. |
| ISA4 |Escriba el valor de la información de seguridad. Si el valor especificado es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez en el cuadro de texto del valor (ISA4). |

### <a name="acknowledgment"></a>Acknowledgement (Confirmación)

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propiedad | Descripción |
| --- | --- |
| TA1 expected (TA1 esperada) |Seleccione esta casilla para devolver una confirmación técnica (TA1) al remitente del intercambio. Esta configuración especifica que el asociado del host que envía el mensaje solicita una confirmación del asociado invitado en el contrato. El asociado del host espera estas confirmaciones en función de la configuración de recepción del contrato. |
| FA expected (FA esperada) |Seleccione esta casilla para devolver una confirmación funcional (FA) al remitente del intercambio y seleccione si desea la confirmación 997 o la 999, en función de las versiones del esquema con el que esté trabajando. El asociado del host espera estas confirmaciones en función de la configuración de recepción del contrato. |
| FA Version (Versión de FA) |Seleccione la versión de FA |

### <a name="schemas"></a>Esquemas

![](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propiedad | Descripción |
| --- | --- |
| Versión |Seleccione la versión de X12. |
| Transaction Type (ST01) (Tipo de transacción (ST01)) |Seleccione el tipo de transacción. |
| Esquema |Seleccione el esquema que se utilizará. Los esquemas se encuentran en la cuenta de integración. Si selecciona primero un esquema, este configura automáticamente la versión y el tipo de transacción  |

> [!NOTE]
> Configure el [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necesario que se carga en la [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md)
> 
> 

### <a name="envelopes"></a>Envelopes (Sobres)

![](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propiedad | Descripción |
| --- | --- |
| ISA11 Usage (Uso de ISA11) |Utilice este campo para especificar el separador de una transacción |
| Identificador Estándar |Seleccione el identificador Estándar para usar la notación decimal de ".", |
| Separador de repeticiones |Seleccione el separador Repetición para especificar el separador de las ocurrencias repetitivas de un elemento de datos simple o una estructura de datos repetida. Por ejemplo, se suele utilizar (^) como separador de repeticiones. En los esquemas HIPAA, solo se puede usar (^). |

### <a name="control-numbers"></a>Control Numbers (Números de control)

![](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propiedad | Descripción |
| --- | --- |
| Control version number (ISA12) (Número de versión de control (ISA12)) |Seleccione la versión del estándar X12 |
| Usage Indicator (ISA15) (Indicador de uso (ISA15)) |Seleccione el contexto de un intercambio.  Los valores son información, datos de producción o datos de prueba |
| SCHEMA (ESQUEMA) |Genera los segmentos GS y ST para un intercambio X12 codificado que envía a la canalización de envío. |
| GS1 |Opcional, seleccione un valor para el código funcional en la lista desplegable. |
| GS2 |Opcional, remitente de la aplicación |
| GS3 |Opcional, receptor de la aplicación |
| GS4 |Opcional, seleccione CCYYMMDD (SSAAMMDD) o YYMMDD (AAMMDD). |
| GS5 |Opcional, seleccione HHMM, HHMMSS o HHMMSSdd. |
| GS7 |Opcional, seleccione un valor para la agencia responsable de la lista desplegable |
| GS8 |Opcional, versión del documento |
| Interchange Control Number (ISA13) (Número de control de intercambio (ISA13)) |Obligatorio, escriba un intervalo de valores para el número de control de intercambio. Escriba un valor numérico: 1 como mínimo y 999999999 como máximo |
| Group Control Number (GS06) (Número de control de grupo (GS06)) |Obligatorio, escriba un intervalo de números para el número de control de grupo. Escriba un valor numérico: 1 como mínimo y 999999999 como máximo |
| Transaction Set Control Number (ST02) (Número de control de conjunto de transacciones (ST02)) |Obligatorio, escriba un intervalo de números para el número de control del grupo de transacciones. Escriba un intervalo de valores numéricos: 1 como mínimo y 999999999 como máximo. |
| Prefijo |Opcional, designado para el intervalo de números de control del conjunto de transacciones utilizado en la confirmación. Escriba un valor numérico en los dos campos centrales y un valor alfanumérico (si lo desea) en los campos Prefijo y Sufijo. Los campos centrales son obligatorios y contienen los valores mínimo y máximo del número de control |
| Sufijo |Opcional, designado para el intervalo de números de control del conjunto de transacciones utilizado en la confirmación. Escriba un valor numérico en los dos campos centrales y un valor alfanumérico (si lo desea) en los campos Prefijo y Sufijo. Los campos centrales son obligatorios y contienen los valores mínimo y máximo del número de control |

### <a name="character-sets-and-separators"></a>Character Sets and Separators (Juegos de caracteres y separadores)

Además del juego de caracteres, puede especificar un conjunto de delimitadores diferente para cada tipo de mensaje. Si no se especifica un juego de caracteres en un esquema de mensaje concreto, se usará el juego de caracteres predeterminado.

![](./media/logic-apps-enterprise-integration-x12/x12-9.png) 


| Propiedad | Descripción |
| --- | --- |
| Character Set to be used (Juego de caracteres que se va a usar) |Seleccione el juego de caracteres de X12 para validar las propiedades.  Las opciones son básico, ampliado y UTF8 |
| SCHEMA (ESQUEMA) |Seleccione un esquema en la lista desplegable. En el esquema seleccionado, seleccione el conjunto de separadores que se debe usar |
| Tipo de entrada |Seleccione un tipo de entrada en la lista desplegable |
| Separador de elementos de componente |Escriba un carácter simple para separar los elementos de datos compuestos |
| Separador de elementos de datos |Escriba un carácter simple para separar los elementos de datos simples dentro de los elementos de datos compuestos |
| Carácter de reemplazo |Escriba un carácter de reemplazo. Al generar el mensaje X12 saliente, todas las instancias de caracteres separadores de los datos de carga útil se reemplazan por el carácter especificado |
| Terminador de segmento |Escriba un carácter simple para indicar el final de un segmento EDI |
| Sufijo |Seleccione el carácter que se usa con el identificador de segmento. Si designa un sufijo, el elemento de datos del terminador de segmento podrá estar vacío. Si se deja vacío el terminador de segmento, deberá designar un sufijo. |

### <a name="validation"></a>Validación

![](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

| Propiedad | Descripción |
| --- | --- |
| Message Type (Tipo de mensaje) |Seleccione el tipo de mensaje de la lista |
| EDI Validation (Validación de EDI) |Al seleccionar esta opción, se habilita la validación en el receptor del intercambio. Esta validación realiza la validación de EDI en los elementos de datos del conjunto de transacciones, lo que valida los tipos de datos, las restricciones de longitud, los elementos de datos vacíos y los separadores finales. |
| Extended Validation (Validación extendida) |Al seleccionar esta opción, se habilita la validación extendida de los intercambios recibidos del remitente del intercambio. Esto incluye la validación de la longitud del campo, la opcionalidad y el número de repeticiones, además de la validación del tipo de datos XSD. Puede habilitar la validación extendida sin habilitar la validación de EDI o viceversa. |
| Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales) |Al seleccionar esta opción, se especifica que un intercambio EDI recibido de la entidad superará la validación si un elemento de datos de un intercambio EDI no cumple el requisito de longitud debido a los espacios finales, pero sí lo cumple si estos se eliminan. |
| Trailing Separator (Separador final) |Al seleccionar esta opción, se especifica que un intercambio EDI recibido de la entidad superará la validación si un elemento de datos de un intercambio EDI no cumple el requisito de longitud debido a los ceros a la izquierda (o finales), pero sí lo cumple si estos se eliminan.</br></br>Seleccione Not Allowed (No permitidos) si no desea permitir delimitadores y separadores finales en un intercambio recibido del remitente del intercambio. Si el intercambio contiene delimitadores y separadores finales, se declarará no válido.</br></br>Seleccione Opcional para aceptar intercambios con o sin delimitadores y separadores finales.</br></br>Seleccione Mandatory (Obligatorios) si el intercambio recibido debe contener delimitadores y separadores finales. |

Cuando haya establecido las propiedades de configuración de recepción, seleccione el botón **Aceptar**.  El contrato está preparado para controlar los mensajes salientes que cumplen el esquema seleccionado.

Seleccione Aceptar para crear el contrato.

Seleccione el icono de **Acuerdos** de la hoja Cuenta de integración; verá en la lista el acuerdo que acaba de agregar.  
![](./media/logic-apps-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Más información
* [Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  




<!--HONumber=Feb17_HO1-->


