---
title: "Mensajes X12 para la integración empresarial B2B: Azure Logic Apps | Microsoft Docs"
description: "Intercambie mensajes X12 en formato EDI para la integración empresarial B2B con Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bfad01d8c14cdd972ebe8e4038f226ffe0da93b1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Intercambio de mensajes X12 para la integración empresarial con aplicaciones lógicas

Para poder intercambiar mensajes X12 para Azure Logic Apps, debe crear un contrato X12 y almacenarlo en su cuenta de integración. Estos son los pasos para crear un contrato X12.

> [!NOTE]
> Esta página abarca las características de X12 para Azure Logic Apps. Para más información, consulte [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Antes de comenzar

Esto es lo que necesita:

* Una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md) que ya esté definida y asociada a su suscripción de Azure
* Al menos dos [asociados](../logic-apps/logic-apps-enterprise-integration-partners.md) que estén definidos en su cuenta de integración y configurados con el identificador X12 en **Identidades de negocio**    
* Un [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) obligatorio para cargarlo en su [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md)

Una vez que haya [creado una cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md), [agregado asociados](logic-apps-enterprise-integration-partners.md) y obtenido un [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que desee usar, puede crear un contrato X12 siguiendo estos pasos.

## <a name="create-an-x12-agreement"></a>Creación de un contrato X12

1.  Inicie sesión en [Azure Portal](http://portal.azure.com "Azure Portal"). En el menú izquierdo, seleccione **Más servicios**. 

    > [!TIP]
    > Si no ve **Más servicios**, expanda el menú. En la parte superior del menú contraído, seleccione **Mostrar menú**.

    ![En el menú izquierdo, seleccione "Más servicios"](./media/logic-apps-enterprise-integration-x12/account-1.png)

2.  En el cuadro de búsqueda, escriba "integración" como filtro. En la lista de resultados, seleccione **Cuentas de integración**.  

    ![Filtre por "integración", seleccione "Cuentas de integración"](./media/logic-apps-enterprise-integration-x12/account-2.png)

3. En la hoja **Cuentas de integración** que se abre, seleccione la cuenta de integración en la que va a agregar el contrato.
Si no ve ninguna, [créela](../logic-apps/logic-apps-enterprise-integration-accounts.md "Información completa sobre las cuentas de integración").

    ![Selección de la cuenta de integración donde desea crear el contrato](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Seleccione **Información general** y el icono **Acuerdos**. Si no tiene un icono Acuerdos, agréguelo. 

    ![Elección del icono "Acuerdos"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. En la hoja Acuerdos que se abre, seleccione **Agregar**.

    ![Elección de "Agregar"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. En **Agregar**, escriba un valor en **Nombre** para el contrato. Para el tipo de contrato, seleccione **X12**. Seleccione valores en **Asociado del host**, **Identidad del host**, **Asociado invitado** e **Identidad del invitado** para el contrato. Para más detalles sobre las propiedades, consulte la tabla en este paso.

    ![Especificación de los detalles del contrato](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propiedad | DESCRIPCIÓN |
    | --- | --- |
    | NOMBRE |Nombre del contrato |
    | Tipo de contrato | Debe ser X12 |
    | Host Partner (Partner anfitrión) |Un contrato tiene asociado un partner anfitrión e invitado. El asociado del host representa la organización que configura el contrato. |
    | Host Identity (Identidad anfitriona) |Un identificador del asociado del host. |
    | Guest Partner (Partner invitado) |Un contrato tiene asociado un partner anfitrión e invitado. El partner invitado representa la organización que está haciendo negocios con el partner anfitrión. |
    | Guest Identity |Un identificador del asociado invitado. |
    | Receive Settings (Configuración de recepción) |Estas propiedades se aplican a todos los mensajes que recibe un contrato. |
    | Send Settings (Configuración de envío) |Estas propiedades se aplican a todos los mensajes que envía un contrato. |  

  > [!NOTE]
  > La resolución del contrato de X12 depende de la coincidencia entre el calificador e identificador del remitente y el calificador y el identificador del receptor definidos en el asociado y en el mensaje entrante. Si estos valores cambian para el asociado, actualice también el contrato.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configuración de la forma en que su contrato controla los mensajes recibidos

Ahora que ha establecido las propiedades del contrato, puede configurar cómo este identifica y controla los mensajes entrantes recibidos del asociado a través de este contrato.

1.  En **Agregar**, seleccione **Configuración de recepción**.
Configure estas propiedades en función del contrato con el asociado con el que intercambia mensajes. Para las descripciones de las propiedades, consulte las tablas de esta sección.

    **Configuración de recepción** se divide en las siguientes secciones: Identificadores, Reconocimiento, Esquemas, Sobres, Números de control, Validaciones y Configuración interna.

2. Cuando haya terminado, asegúrese de guardar la configuración con **Aceptar**.

Ahora el contrato está preparado para controlar los mensajes entrantes que cumplan la configuración seleccionada.

### <a name="identifiers"></a>Identifiers (Identificadores)

![Establezca las propiedades de identificadores](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| ISA1 (Authorization Qualifier) (ISA1 (calificador de autorización)) |Seleccione el valor del calificador de autorización en la lista desplegable. |
| ISA2 |Opcional. Escriba el valor de la información de autorización. Si el valor especificado en ISA1 es distinto de 00, escriba como mínimo un carácter alfanumérico y, como máximo, diez. |
| ISA3 (Security Qualifier) (ISA3 (calificador de seguridad)) |Seleccione el valor del calificador de seguridad en la lista desplegable. |
| ISA4 |Opcional. Escriba el valor de la información de seguridad. Si el valor especificado en ISA3 es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez. |

### <a name="acknowledgment"></a>Acknowledgement (Confirmación)

![Establezca las propiedades de confirmación](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| TA1 expected (TA1 esperada) |Devuelve una confirmación técnica al remitente del intercambio. |
| FA expected (FA esperada) |Devuelve una confirmación funcional al remitente del intercambio. A continuación, seleccione si desea la confirmación 997 o la 999, en función de las versiones del esquema. |
| Include AK2/IK2 Loop (Incluir bucle AK2/IK2) |Habilita la generación de bucles AK2 en confirmaciones funcionales para conjuntos de transacciones aceptadas |

### <a name="schemas"></a>Esquemas

Seleccione un esquema para cada tipo de transacción (ST1) y aplicación de remitente (GS2). La canalización de recepción desensambla el mensaje entrante haciendo coincidir los valores de ST1 y GS2 del mensaje entrante con los valores que establezca aquí y el esquema del mensaje entrante con el esquema que configure aquí.

![Seleccione un esquema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Versión |Seleccione la versión de X12. |
| Transaction Type (ST01) (Tipo de transacción (ST01)) |Seleccione el tipo de transacción. |
| Sender Application (GS02) (Aplicación remitente (GS02)) |Seleccione la aplicación remitente. |
| Esquema |Seleccione el archivo de esquema que desee usar. Los esquemas se agregan a la cuenta de integración. |

> [!NOTE]
> Configure el [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necesario que se carga en la [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes (Sobres)

![Especifique el separador de un conjunto de transacciones: elija Identificador estándar o Separador de repeticiones](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| ISA11 Usage (Uso de ISA11) |Especifica el separador que se va a usar en un conjunto de transacciones: <p>Seleccione **Identificador estándar** para usar un punto (.) en notación decimal, en lugar de la notación decimal del documento entrante en la canalización de recepción EDI. <p>Seleccione **Separador de repeticiones** para especificar el separador de repeticiones de un elemento de datos simple o una estructura de datos repetida. Por ejemplo, normalmente se utiliza el acento circunflejo (^) como separador de repeticiones. En los esquemas HIPAA, solo se puede usar el acento circunflejo. |

### <a name="control-numbers"></a>Control Numbers (Números de control)

![Seleccione cómo desea controlar los duplicados en números de control](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Disallow Interchange Control Number duplicates (No permitir duplicados del número de control de intercambio) |Bloquee los intercambios duplicados. Comprueba el número de control de intercambio (ISA13) para el número de control de intercambio recibido. Si se detecta una coincidencia, la canalización de recepción no procesa el intercambio. Puede especificar el número de días para realizar la comprobación si especifica un valor para *Comprobar si hay ISA13 duplicados todos los (días)*. |
| Disallow Group control number duplicates (No permitir duplicados del número de control de grupo) |Bloquee los intercambios con números de control de grupo duplicados. |
| Disallow Transaction set control number duplicates (No permitir duplicados del número de control del conjunto de transacciones) |Bloquee los intercambios con números de control de conjunto de transacciones duplicados. |

### <a name="validations"></a>Validations (Validaciones)

![Establezca las propiedades de validación de mensajes recibidos](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Cuando haya completado cada fila de validación, se agrega otra automáticamente. Si no se especifica ninguna regla, la validación utiliza la fila "Predeterminado".

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Message Type (Tipo de mensaje) |Seleccione el tipo de mensaje EDI. |
| EDI Validation (Validación de EDI) |Realiza una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales. |
| Extended Validation (Validación extendida) |Si el tipo de datos no es EDI, lo que se valida es el requisito de elemento de datos, así como las repeticiones permitidas, las enumeraciones y la validación de la longitud del elemento de datos (mín./máx.). |
| Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales) |Conserve los caracteres de espacio y cero iniciales o finales adicionales. No los quite. |
| Recortar ceros iniciales y finales |Quite los caracteres de espacio y cero iniciales o finales. |
| Trailing Separator Policy (Directiva de separador final) |Genere separadores finales. <p>Seleccione **No permitido** para prohibir los delimitadores y separadores finales en el intercambio recibido. Si el intercambio contiene delimitadores y separadores finales, se declara no válido. <p>Seleccione **Opcional** para aceptar intercambios con o sin delimitadores y separadores finales. <p>Seleccione **Obligatorio** cuando el intercambio deba contener delimitadores y separadores finales. |

### <a name="internal-settings"></a>Internal Settings (Configuración interna)

![Seleccione la configuración interna](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Convertir formato decimal implícito Nn en valor numérico de base 10 |Convierte un número EDI especificado con el formato "Nn" en un valor numérico de base 10. |
| Create empty XML tags if trailing separators are allowed (Crear etiquetas XML vacías si se permiten separadores finales) |Seleccione esta casilla para que el remitente del intercambio incluya etiquetas XML vacías para los separadores finales. |
| Dividir intercambio como conjuntos de transacciones: suspender conjuntos de transacciones en caso de error|Analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado al conjunto de transacciones. Solo suspende aquellas transacciones en las que se produce un error en la validación. |
| Dividir intercambio como conjuntos de transacciones: suspender intercambio en caso de error|Analiza todos los conjuntos de transacciones de un intercambio en un documento XML independiente aplicando el sobre adecuado. Suspende todo el intercambio cuando uno o varios conjuntos de transacciones del intercambio no superan la validación. | 
| Conservar intercambio: suspender conjuntos de transacciones en caso de error |Deja intacto el intercambio y crea un documento XML para todo el intercambio por lotes. Suspende solo los conjuntos de transacciones que no superan la validación, sin dejar de procesar el resto. |
| Conservar intercambio: suspender intercambio en caso de error |Deja intacto el intercambio y crea un documento XML para todo el intercambio por lotes. Suspende todo el intercambio cuando uno o varios conjuntos de transacciones del intercambio no superan la validación. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configuración de la forma en que su contrato envía mensajes

Puede configurar cómo este contrato identifica y controla los mensajes salientes que envía a su asociado a través de este contrato.

1.  En **Agregar**, seleccione **Send Settings** (Configuración de envío).
Configure estas propiedades en función del contrato con el asociado con el que intercambia mensajes. Para las descripciones de las propiedades, consulte las tablas de esta sección.

    **Configuración de envío** se organiza en las siguientes secciones: Identificadores, Reconocimiento, Esquemas, Sobres, Juegos de caracteres y separadores, Números de control y Validación.

2. Cuando haya terminado, asegúrese de guardar la configuración con **Aceptar**.

Ahora el contrato está preparado para controlar los mensajes salientes que cumplan la configuración seleccionada.

### <a name="identifiers"></a>Identifiers (Identificadores)

![Establezca las propiedades de identificadores](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| ISA1 (Authorization Qualifier) (ISA1 (calificador de autorización)) |Seleccione el valor del calificador de autorización en la lista desplegable. |
| ISA2 |Escriba el valor de la información de autorización. Si el valor especificado es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez. |
| ISA3 (Security Qualifier) (ISA3 (calificador de seguridad)) |Seleccione el valor del calificador de seguridad en la lista desplegable. |
| ISA4 |Escriba el valor de la información de seguridad. Si el valor especificado es distinto de 00, especifique como mínimo un carácter alfanumérico y, como máximo, diez en el cuadro de texto del valor (ISA4). |

### <a name="acknowledgment"></a>Acknowledgement (Confirmación)

![Establezca las propiedades de confirmación](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| TA1 expected (TA1 esperada) |Devuelva una confirmación técnica (TA1) al remitente del intercambio. Esta configuración especifica que el asociado del host que envía el mensaje solicita una confirmación del asociado invitado en el contrato. El asociado del host espera estas confirmaciones en función de la configuración de recepción del contrato. |
| FA expected (FA esperada) |Devuelva una confirmación funcional (FA) al remitente del intercambio. Seleccione si desea la confirmación 997 o la 999, en función de las versiones del esquema con las que esté trabajando. El asociado del host espera estas confirmaciones en función de la configuración de recepción del contrato. |
| FA Version (Versión de FA) |Seleccione la versión de FA |

### <a name="schemas"></a>Esquemas

![Seleccione el esquema que se utilizará](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Versión |Seleccione la versión de X12. |
| Transaction Type (ST01) (Tipo de transacción (ST01)) |Seleccione el tipo de transacción. |
| Esquema |Seleccione el esquema que se utilizará. Los esquemas se encuentran en la cuenta de integración. Si selecciona primero un esquema, este configura automáticamente la versión y el tipo de transacción  |

> [!NOTE]
> Configure el [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necesario que se carga en la [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes (Sobres)

![Especifique el separador de un conjunto de transacciones: elija Identificador estándar o Separador de repeticiones](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| ISA11 Usage (Uso de ISA11) |Especifica el separador que se va a usar en un conjunto de transacciones: <p>Seleccione **Identificador estándar** para usar un punto (.) en notación decimal, en lugar de la notación decimal del documento entrante en la canalización de recepción EDI. <p>Seleccione **Separador de repeticiones** para especificar el separador de repeticiones de un elemento de datos simple o una estructura de datos repetida. Por ejemplo, normalmente se utiliza el acento circunflejo (^) como separador de repeticiones. En los esquemas HIPAA, solo se puede usar el acento circunflejo. |

### <a name="control-numbers"></a>Control Numbers (Números de control)

![Especifique las propiedades de número de control](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Control version number (ISA12) (Número de versión de control (ISA12)) |Seleccione la versión del estándar X12 |
| Usage Indicator (ISA15) (Indicador de uso (ISA15)) |Seleccione el contexto de un intercambio.  Los valores son información, datos de producción o datos de prueba |
| Esquema |Genera los segmentos GS y ST para un intercambio X12 codificado que envía a la canalización de envío. |
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

Además del juego de caracteres, puede especificar un conjunto de delimitadores diferente para cada tipo de mensaje. Si no se especifica ningún juego de caracteres en un esquema de mensaje concreto, se usa el predeterminado.

![Especifique los delimitadores para tipos de mensaje](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Character Set to be used (Juego de caracteres que se va a usar) |Para validar las propiedades, seleccione el juego de caracteres de X12. Las opciones son básico, ampliado y UTF8. |
| Esquema |Seleccione un esquema en la lista desplegable. Después de completar cada fila, se agrega automáticamente una nueva. Para el esquema seleccionado, elija el conjunto de separadores que desee usar, en función de las siguientes descripciones. |
| Tipo de entrada |Seleccione un tipo de entrada en la lista desplegable. |
| Separador de componentes |Para separar elementos de datos compuestos, escriba un solo carácter. |
| Separador de elementos de datos |Para separar elementos de datos simples dentro de elementos de datos compuestos, escriba un solo carácter. |
| Carácter de reemplazo |Escriba un carácter de reemplazo que se usa para reemplazar todos los caracteres separadores en los datos de carga al generar el mensaje X12 saliente. |
| Terminador de segmento |Para indicar el final de un segmento EDI, escriba un solo carácter. |
| Sufijo |Seleccione el carácter que se usa con el identificador de segmento. Si designa un sufijo, el elemento de datos del terminador de segmento podrá estar vacío. Si se deja vacío el terminador de segmento, deberá designar un sufijo. |

> [!TIP]
> Para proporcionar valores de caracteres especiales, edite el contrato como JSON y proporcione el valor ASCII para el carácter especial.

### <a name="validation"></a>Validación

![Establezca las propiedades de validación para enviar mensajes](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Cuando haya completado cada fila de validación, se agrega otra automáticamente. Si no se especifica ninguna regla, la validación utiliza la fila "Predeterminado".

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Message Type (Tipo de mensaje) |Seleccione el tipo de mensaje EDI. |
| EDI Validation (Validación de EDI) |Realiza una validación de EDI en los tipos de datos según lo definido en las propiedades de EDI del esquema, las restricciones de longitud, los elementos de datos vacíos y los separadores finales. |
| Extended Validation (Validación extendida) |Si el tipo de datos no es EDI, lo que se valida es el requisito de elemento de datos, así como las repeticiones permitidas, las enumeraciones y la validación de la longitud del elemento de datos (mín./máx.). |
| Allow Leading/Trailing Zeroes (Permitir ceros a la izquierda/finales) |Conserve los caracteres de espacio y cero iniciales o finales adicionales. No los quite. |
| Recortar ceros iniciales y finales |Quite los ceros iniciales o finales. |
| Trailing Separator Policy (Directiva de separador final) |Genere separadores finales. <p>Seleccione **No permitido** para prohibir los delimitadores y separadores finales en el intercambio enviado. Si el intercambio contiene delimitadores y separadores finales, se declara no válido. <p>Seleccione **Opcional** para enviar intercambios con o sin delimitadores y separadores finales. <p>Seleccione **Obligatorio** si el intercambio enviado debe contener delimitadores y separadores finales. |

## <a name="find-your-created-agreement"></a>Búsqueda del contrato creado

1.  Cuando termine de establecer las propiedades del contrato, en la hoja **Agregar**, elija **Aceptar** para terminar de crear el contrato y volver a la hoja de la cuenta de integración.

    Ahora el contrato recién agregado aparece en la lista **Acuerdos**.

2.  También puede ver los contratos en la información general de la cuenta de integración. En la hoja de la cuenta de integración, elija **Información general** y seleccione el icono **Acuerdos**.

    ![Elección del icono "Acuerdos" para ver todos los contratos](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/x12/). 

## <a name="learn-more"></a>Más información
* [Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  

