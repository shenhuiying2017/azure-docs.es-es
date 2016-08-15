<properties 
	pageTitle="Enterprise Integration Pack con EDIFACT | Microsoft Azure" 
	description="Aprenda a utilizar los acuerdos EDIFACT para crear aplicaciones lógicas." 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2016" 
	ms.author="jonfan"/>

# Enterprise Integration Pack con EDIFACT 

> [AZURE.NOTE] Esta página cubre las características de EDIFACT de Logic Apps. Para más información sobre X12, haga clic [aquí](app-service-logic-enterprise-integration-x12.md).

## Creación de un acuerdo EDIFACT 
Para poder intercambiar mensajes EDIFACT, hay que crear un acuerdo EDIFACT y almacenarlo en la cuenta de integración. Los siguientes pasos lo guían a lo largo del proceso de creación de un acuerdo EDIFACT.

### Requisitos para poder comenzar
- Debe tener una [cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) definida en su suscripción de Azure.
- Debe tener al menos dos [asociados](./app-service-logic-enterprise-integration-partners.md) ya definidos en su cuenta de integración.

>[AZURE.NOTE]Al crear un acuerdo, el contenido de los mensajes que envíe o reciba a y desde el asociado debe coincidir con el tipo de acuerdo.


Cuando haya [creado una cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) y [agregado los asociados](./app-service-logic-enterprise-integration-partners.md), podrá generar un acuerdo EDIFACT siguiendo estos pasos:

### En la página principal del Portal de Azure

Después de iniciar sesión en el [Portal de Azure](http://portal.azure.com "Portal de Azure"):
1. En el menú de la izquierda, seleccione **Examinar**.

>[AZURE.TIP]Si no ve el vínculo **Examinar**, puede que primero tenga que expandir el menú. Para ello, haga clic en el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)
2. Escriba *integration* en el cuadro de búsqueda del filtro; después, seleccione **Cuentas de integración** en la lista de resultados. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)
3. En la hoja **Cuentas de integración** que se abre, seleccione la cuenta de integración en la que va a crear el acuerdo. Si no ve ninguna lista con cuentas de integración, [cree una antes](./app-service-logic-enterprise-integration-accounts.md "Todo sobre las cuentas de integración"). ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)
4.  Seleccione el icono **Acuerdos**. Si no lo ve, agréguelo antes. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)
5. Seleccione el botón **Agregar** en la hoja Acuerdos que se abre. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)
6. Escriba un **nombre** para el acuerdo; después, seleccione **Tipo de contrato** para EDIFACT, **Asociado del host**, **Identidad del host**, **Asociado invitado**, **Identidad del invitado** en la hoja Acuerdos que se abre. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)
7. Después de haber establecido las propiedades del acuerdo, seleccione **Configuración de recepción** para configurar cómo se administran los mensajes que se reciben través de este acuerdo.
8. El control Configuración de recepción se divide en las siguientes secciones: Identificadores, Acknowledgement (Confirmación), Esquemas, Números de control, Validación, Configuración interna y Procesamiento por lotes. Configure estas propiedades en función del contrato con el asociado con el que intercambiará mensajes. A continuación se ofrece una vista de estos controles; configúrelos en función de cómo desee que este acuerdo identifique y controle los mensajes entrantes: ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)
9. Seleccione el botón **Aceptar** para guardar la configuración.

### Identifiers (Identificadores)

|Propiedad|Descripción |
|---|---|
|UNB6.1 (contraseña de referencia del destinatario)|Escriba un valor alfanumérico de entre 1 y 14 caracteres.|
|UNB6.2 (calificador de referencia del destinatario)|Escriba un valor alfanumérico con un carácter como mínimo y dos como máximo.|

### Agradecimientos 

|Propiedad|Descripción |
|----|----|
|Receipt of Message (CONTRL) (Recepción del mensaje [CONTRL])|Active esta casilla para devolver una confirmación técnica (CONTRL) al remitente del intercambio. Estas confirmaciones se envían al remitente del intercambio según la configuración de envío del acuerdo.|
|Acknowledgement (CONTRL) (Confirmación [CONTRL])|Active esta casilla para devolver una confirmación funcional (CONTRL) al remitente del intercambio. La confirmación se envía al remitente del intercambio basándose en la configuración de envío del acuerdo.|

### Esquemas

|Propiedad|Descripción |
|----|----|
|UNH2.1 (TYPE) (UNH2.1 [TIPO])|Seleccione un tipo de conjunto de transacciones.|
|UNH2.2 (VERSION) (UNH2.2 [VERSIÓN])|Escriba el número de versión de mensaje. (Como mínimo un carácter; tres caracteres como máximo).|
|UNH2.3 (RELEASE) (UNH2.3 [LANZAMIENTO])|Escriba el número de versión de mensaje. (Como mínimo un carácter; tres caracteres como máximo).|
|UNH2.5 (ASSOCIATED ASSIGNED CODE) (UNH2.5 [CÓDIGO ASIGNADO ASOCIADO])|Escriba el código asignado. (Seis caracteres como máximo. Debe ser alfanumérico).|
|UNG2.1 (APP SENDER ID) (UNG2.1 [ID. DE REMITENTE DE APLICACIÓN])|Escriba un valor alfanumérico con un carácter como mínimo y 35 como máximo.|
|UNG2.2 (APP SENDER CODE QUALIFIER) (UNG2.2 [CALIFICADOR DE CÓDIGO DE REMITENTE DE APLICACIÓN])|Escriba un valor alfanumérico con un máximo de cuatro caracteres.|
|SCHEMA (ESQUEMA)|Seleccione el esquema cargado anteriormente que desea utilizar desde su cuenta de integración asociada.|

### Control Numbers (Números de control)

|Propiedad|Descripción |
|----|----|
|Disallow Interchange Control Number duplicates (No permitir duplicados del número de control de intercambio)|Active esta casilla para bloquear los intercambios duplicados. Si la selecciona, la acción de descodificación de EDIFACT comprueba que el número de control de intercambio (UNB5) del intercambio recibido no coincide con el número de control de intercambio. Si se detecta una coincidencia, el intercambio no se procesa.
|Check for duplicate UNB5 every (days) (Comprobar UNB5 duplicados cada [días])|Si optó por no permitir números de control de intercambio duplicados, puede especificar el número de días en que se realizará la comprobación indicando el valor pertinente en la opción **Check for duplicate UNB5 every (days)** (Comprobar UNB5 duplicados cada [días]).|
|Disallow Group control number duplicates (No permitir duplicados del número de control de grupo)|Active esta casilla para bloquear los intercambios con números de control de grupo duplicados (UNG5).|
|Disallow Transaction set control number duplicates (No permitir duplicados del número de control del conjunto de transacciones)|Active esta casilla para bloquear los intercambios con números de control de conjuntos de transacciones duplicados (UNH1).|
|EDIFACT Acknowledgement Control Number (Número de control de confirmación EDIFACT)|Para designar los números de referencia del conjunto de transacciones que se utilizará en una confirmación, escriba un valor para el prefijo, un intervalo de números de referencia y un sufijo.|

### Validations (Validaciones)

|Propiedad|Descripción |
|----|----|
|Message Type (Tipo de mensaje)|Especifique el tipo de mensaje. Cuando se completa cada fila de la validación, se agregará otra automáticamente. Si no se especifica ninguna regla, la fila marcada como predeterminada se utiliza para la validación.|
|EDI Validation (Validación de EDI)|Active esta casilla para realizar una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales.|
|Extended Validation (Validación extendida)|Active esta casilla para habilitar la validación extendida (XSD) de los intercambios recibidos del remitente del intercambio. Esto incluye la validación de la longitud del campo, la opcionalidad y el número de repeticiones, además de la validación del tipo de datos XSD.|
|Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales)|Seleccione **Permitir** para permitir ceros iniciales y finales; **No permitidos** para no permitir ceros iniciales y finales, o **Recortar** para recortar los ceros iniciales y finales.|
|Recortar ceros iniciales y finales|Active esta casilla para recortar los ceros iniciales o finales|
|Trailing Separator Policy (Directiva de separador final)|Seleccione **No permitido** si no desea permitir delimitadores y separadores finales en un intercambio recibido del remitente de intercambio. Si el intercambio contiene delimitadores y separadores finales, se declarará no válido. Seleccione **Opcional** para aceptar intercambios con o sin delimitadores y separadores finales. Seleccione **Obligatorios** si el intercambio recibido debe contener delimitadores y separadores finales.|

### Internal Settings (Configuración interna)

|Propiedad|Descripción |
|----|----|
|Create empty XML tags if trailing separators are allowed (Crear etiquetas XML vacías si se permiten separadores finales)|Seleccione esta casilla para que el remitente del intercambio incluya etiquetas XML vacías para los separadores finales.|
|Inbound batching processing (Procesamiento por lotes entrantes)|Las opciones incluyen:</br></br>**Dividir intercambio como conjuntos de transacciones: suspender conjuntos de transacciones en caso de error**: analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado al conjunto de transacciones. Con esta opción, si uno o más conjuntos de transacciones del intercambio no superan la validación, solo se suspenden esos conjuntos de transacciones. Dividir intercambio como conjuntos de transacciones: suspender intercambio en caso de error: analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado. Con esta opción, si uno o varios conjuntos de transacciones del intercambio no superan la validación, todo el intercambio se suspende.</br></br>**Conservar intercambio: suspender conjuntos de transacciones en caso de error**: deja el intercambio intacto, ya que crea un documento XML para todo el intercambio por lotes. Con esta opción, si uno o más conjuntos de transacciones del intercambio no superan la validación, Servicios de BizTalk suspende el intercambio por completo.</br></br>**Conservar intercambio: suspender intercambio en caso de error**: deja el intercambio intacto, ya que crea un documento XML para todo el intercambio por lotes. Con esta opción, si uno o varios conjuntos de transacciones del intercambio no superan la validación, todo el intercambio se suspende.|

El acuerdo está preparado para controlar los mensajes entrantes que cumplen la configuración seleccionada.

Para configurar las opciones que controlan los mensajes que se envían a los asociados, siga estos pasos:
10. Seleccione **Send Settings** (Configuración de envío) para configurar cómo se controlarán los mensajes enviados mediante este acuerdo.

El control de la configuración de envío se divide en las siguientes secciones: Identificadores, Acknowledgement (Confirmación), Esquemas, Sobres, Juegos de caracteres y separadores, Números de control y Validación.

A continuación, se muestra una vista de estos controles. Realice las selecciones en función de cómo desee controlar los mensajes que envía a los asociados por medio de este acuerdo: ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)
11. Seleccione el botón **Aceptar** para guardar la configuración.

### Identifiers (Identificadores)
|Propiedad|Descripción |
|----|----|
|UNB1.2 (Syntax version) (UNB1.2 [versión de sintaxis])|Seleccione un valor entre **1** y **4**.|
|UNB2.3 (Sender Reverse Routing Address) (UNB2.3 [dirección de enrutamiento inverso del remitente])|Escriba un valor alfanumérico con un carácter como mínimo y 14 como máximo.|
|UNB3.3 (Recipient Reverse Routing Address) (UNB3.3 [dirección de enrutamiento inverso del destinatario])|Escriba un valor alfanumérico con un carácter como mínimo y 14 como máximo.|
|UNB6.1 (contraseña de referencia del destinatario)|Escriba un valor alfanumérico con un carácter como mínimo y 14 caracteres como máximo.|
|UNB6.2 (calificador de referencia del destinatario)|Escriba un valor alfanumérico con un carácter como mínimo y dos como máximo.|
|UNB7 (Application Reference ID) (UNB7 [Identificador de referencia de solicitud])|Escriba un valor alfanumérico con un carácter como mínimo y 14 como máximo.|

### Acknowledgement (Confirmación)
|Propiedad|Descripción |
|----|----|
|Receipt of Message (CONTRL) (Recepción del mensaje [CONTRL])|Active esta casilla si el asociado hospedado espera recibir una confirmación técnica (CONTRL). Esta configuración especifica que el asociado hospedado que envía el mensaje solicita una confirmación del asociado invitado.|
|Acknowledgement (CONTRL) (Confirmación [CONTRL])|Active esta casilla si el asociado hospedado espera recibir una confirmación funcional (CONTRL). Esta configuración especifica que el asociado hospedado que envía el mensaje solicita una confirmación del asociado invitado.|
|Generate SG1/SG4 loop for accepted transaction sets (Generar bucle SG1/SG4 en los conjuntos de transacciones aceptadas)|Si elige solicitar una confirmación funcional, active esta casilla para forzar la generación de bucles SG1/SG4 en confirmaciones CONTRL funcionales para conjuntos de transacciones aceptados.|

### Esquemas
|Propiedad|Descripción |
|----|----|
|UNH2.1 (TYPE) (UNH2.1 [TIPO])|Seleccione un tipo de conjunto de transacciones.|
|UNH2.2 (VERSION) (UNH2.2 [VERSIÓN])|Escriba el número de versión de mensaje.|
|UNH2.3 (RELEASE) (UNH2.3 [LANZAMIENTO])|Escriba el número de versión de mensaje.|
|SCHEMA (ESQUEMA)|Seleccione el esquema que se utilizará. Los esquemas se encuentran en la cuenta de integración. Para obtener acceso a los esquemas, antes debe vincular su cuenta de integración con su aplicación lógica.|

### Envelopes (Sobres)
|Propiedad|Descripción |
|----|----|
|UNB8 (Processing Priority Code) (UNB8 [Código de prioridad de procesamiento])|Escriba un valor alfabético con un solo carácter.|
|UNB10 (Communication Agreement) (UNB10 [Acuerdo de comunicaciones])|Escriba un valor alfanumérico con un carácter como mínimo y 40 como máximo.|
|UNB11 (Test Indicator) (UNB11 [Indicador de prueba])|Active esta casilla para indicar que el intercambio generado son datos de prueba|
|Aplicar segmento UNA (notificación del servicio)|Active esta casilla para generar un segmento UNA para el intercambio que se va a enviar.|
|Aplicar segmentos UNG (encabezado de grupo funcional)|Active esta casilla para crear segmentos de agrupación en el encabezado de grupo funcional en los mensajes enviados al asociado invitado. Los siguientes valores se utilizan para crear los segmentos UNG:</br></br>para **UNG1**, escriba un valor alfanumérico con un carácter como mínimo y seis caracteres como máximo.</br></br>Para **UNG2.1**, escriba un valor alfanumérico con un carácter como mínimo y 35 caracteres como máximo.</br></br>Para **UNG2.2**, escriba un valor alfanumérico con un máximo de cuatro caracteres.</br></br>Para **UNG3.1**, escriba un valor alfanumérico con un carácter como mínimo y 35 caracteres como máximo.</br></br>Para **UNG3.2**, escriba un valor alfanumérico con cuatro caracteres como máximo.</br></br>Para **UNG6**, escriba un valor alfanumérico con un carácter como mínimo y tres caracteres como máximo.</br></br>Para **UNG7.1**, escriba un valor alfanumérico con un carácter como mínimo y tres caracteres como máximo.</br></br>Para **UNG7.2**, escriba un valor alfanumérico con un carácter como mínimo y tres caracteres como máximo.</br></br>Para **UNG7.3**, escriba un valor alfanumérico con un carácter como mínimo y seis caracteres como máximo.</br></br>Para **UNG8**, escriba un valor alfanumérico con un carácter como mínimo y 14 caracteres como máximo.|

### Character Sets and Separators (Juegos de caracteres y separadores)
Además del juego de caracteres, puede especificar un conjunto de delimitadores diferente para cada tipo de mensaje. Si no se especifica un juego de caracteres en un esquema de mensaje concreto, se usará el juego de caracteres predeterminado.

|Propiedad|Descripción |
|----|----|
|UNB1.1 (System Identifier) (UNB1.1 [Identificador del sistema])|Seleccione el conjunto de caracteres EDIFACT que se aplicará al intercambio de salida.|
|Esquema|Seleccione un esquema en la lista desplegable. Cuando se completa cada fila se agregará una nueva. En el esquema seleccionado, seleccione el conjunto de separadores que se debe usar:</br></br>**Separador de elementos de componente**: especifique un único carácter para separar los elementos de datos compuestos.</br></br>**Separador de elementos de datos**: especifique un único carácter para separar los elementos de datos simples dentro de elementos de datos compuestos.</br></br></br></br>**Carácter de reemplazo**: seleccione esta casilla si los datos de carga útil contienen caracteres que también se utilizan como datos, segmentos o separadores de componentes. Seguidamente, podrá especificar un carácter de reemplazo. Al generar el mensaje EDIFACT saliente, todas las instancias de caracteres separadores de los datos de carga útil se reemplazan por el carácter especificado.</br></br>**Terminador de segmento**: especifique un único carácter para indicar el final de un segmento EDI.</br></br>**Sufijo**: seleccione el carácter que se usa con el identificador de segmento. Si designa un sufijo, el elemento de datos del terminador de segmento podrá estar vacío. Si se deja vacío el terminador de segmento, deberá designar un sufijo.|

### Control Numbers (Números de control)
|Propiedad|Descripción |
|----|----|
|UNB5 (Interchange Control Number) (UNB5 [Número de control de intercambio])|Escriba un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar un intercambio saliente. El prefijo y sufijo son opcionales; se requiere el número de control. El número de control se incrementa para cada nuevo mensaje; el prefijo y el sufijo siguen siendo los mismos.|
|UNG5 (Group Control Number) (UNG5 [Número de control de grupo])|Escriba un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar el número de control de grupo. El prefijo y sufijo son opcionales; se requiere el número de control. El número de control se incrementa para cada nuevo mensaje hasta que se alcanza el valor máximo; el prefijo y el sufijo siguen siendo los mismos.|
|UNH1 (Message Header Reference Number) (UNH1 [Número de referencia de encabezado de mensaje])|Escriba un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar el número de referencia de encabezado del mensaje. El prefijo y sufijo son opcionales; el número de referencia es obligatorio. El número de referencia se incrementa para cada nuevo mensaje; el prefijo y el sufijo siguen siendo los mismos.|

### Validations (Validaciones)
|Propiedad|Descripción |
|----|----|
|Message Type (Tipo de mensaje)|Al seleccionar esta opción, se habilita la validación en el receptor del intercambio. Esta validación realiza la validación de EDI en los elementos de datos del conjunto de transacciones, lo que valida los tipos de datos, las restricciones de longitud, los elementos de datos vacíos y los separadores finales.|
|EDI Validation (Validación de EDI)|Active esta casilla para realizar una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales.|
|Extended Validation (Validación extendida)|Al seleccionar esta opción, se habilita la validación extendida de los intercambios recibidos del remitente del intercambio. Esto incluye la validación de la longitud del campo, la opcionalidad y el número de repeticiones, además de la validación del tipo de datos XSD. Puede habilitar la validación extendida sin habilitar la validación de EDI o viceversa.|
|Permitir ceros delante/detrás|Al seleccionar esta opción, se especifica que un intercambio EDI recibido de la entidad superará la validación si un elemento de datos de un intercambio EDI no cumple el requisito de longitud debido a los espacios finales, pero sí lo cumple si estos se eliminan.|
|Recortar ceros iniciales y finales|Al seleccionar esta opción se recortan los ceros iniciales y finales.|
|Trailing Separator (Separador final)|Al seleccionar esta opción, se especifica que un intercambio EDI recibido de la entidad superará la validación si un elemento de datos de un intercambio EDI no cumple el requisito de longitud debido a los ceros iniciales (o finales) o espacios finales, pero sí lo cumple si estos se eliminan.</br></br>Seleccione **No permitido** si no desea permitir delimitadores y separadores finales en un intercambio recibido del remitente del intercambio. Si el intercambio contiene delimitadores y separadores finales, se declarará no válido.</br></br>Seleccione **Opcional** para aceptar intercambios con o sin delimitadores y separadores finales.</br></br>Seleccione **Obligatorios** si el intercambio recibido debe contener delimitadores y separadores finales.|

Después de seleccionar **Aceptar** en la hoja abierta:
12. Seleccione el icono de **Acuerdos** de la hoja Cuenta de integración; verá en la lista el acuerdo que acaba de agregar. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)

## Más información
- [Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0803_2016-->