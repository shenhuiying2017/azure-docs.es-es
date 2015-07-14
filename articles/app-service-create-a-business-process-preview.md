<properties 
	pageTitle="Creación de un proceso de negocio" 
	description="Creación de un proceso de negocio" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service-logic" 
	documentationCenter=""/>



<tags
	ms.service="app-service-logic"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="stepsic"/>

# Desarrollo de un proceso de negocio mediante aplicaciones de flujo y de API

Ha creado un flujo básico pero ahora se lo quiere tomar en serio y quiere crear un proceso de negocio con aplicaciones de flujo. En este artículo trataré varios temas clave:

- Adición de lógica condicional (por ejemplo, ejecutar una acción solamente si se cumple una condición determinada) y de bucles
- Aplicaciones de API de BizTalk
- Uso de la vista Código para editar un flujo
- Los distintos tipos de desencadenadores

Para empezar, haga clic aquí para crear un elemento en la suscripción que ya tiene un flujo básico rellenado. Una vez creado este flujo, haga clic en desencadenadores y acciones para editarlo.

## Adición de lógica condicional y de bucles

Aunque el flujo básico funciona correctamente, es posible que haya observado algunos problemas. En primer lugar, solo le enviará por correo el tweet superior, no todos los tweets sobre la palabra clave. A fin de repetir una acción para obtener una lista de elementos, puede usar la propiedad "repetición".

### Repetición

La repetición toma una lista de elementos y ejecuta la acción para cada elemento de esa lista. Para usar la repetición, haga clic en el icono de flecha circular de la parte superior de la acción. Se abrirá un cuadro de texto, donde debe escribir:

    @triggers().outputs.body

Al igual que ocurre con el flujo básico, esto da como resultado una lista de tweets. A continuación, para usar realmente el tweet dentro de la acción, reemplace el mensaje por lo siguiente:

    @repeatItem().message

"repeatItem()" es una función que le proporcionará todos los elementos de la lista. Haga clic en la marca de verificación.

### Condicional

Ahora, este flujo puede seguir siendo molesto porque dará como resultado muchos correos electrónicos. Puede agregar más lógica para que solo pueda recibir un correo electrónico cuando la persona que realiza el tweet tiene un cierto número de seguidores. Para ello, haga clic en el icono de diamante que hay en la parte superior de la acción.

Escriba lo siguiente en el cuadro de texto:

    @greaterThan(repeatItem().person.followers, 1000)

Se mostrará la función repeatItem(), así como una nueva función llamada greaterThan(). Esta función compara dos valores y solo permite que se ejecute la acción si el primero es mayor que el segundo. Observe la sintaxis para obtener los seguidores: para cada propiedad que le interese, agregue un punto seguido del nombre de la propiedad. Haga clic en la marca de verificación para guardar la condición.

## Aplicaciones de API de BizTalk

Hay varias categorías diferentes de aplicaciones de API disponibles en Azure. Twitter y Office 365 son excelentes conectores para este escenario, pero también hay una serie de aplicaciones de API que proporciona BizTalk y que puede aprovechar en su flujo.

Haga clic en el servicio XXXXXX que se encuentra en el panel de la derecha. Esto agregará ese servicio, con tecnología de BizTalk, al diseñador.

### Paso de datos entre acciones

Para usar más de una acción en un flujo, debe pasar datos entre las acciones. Para ello, se usa la función actions().

Una vez agregada la acción XXXXXX, escriba:

    @actions(‘SendMail’).outputs.YYYYYY

Esto obtendrá la propiedad YYYYYY de los resultados de la acción SendMail. Siempre puede ver el nombre de una acción justo debajo del icono de esa acción. Del mismo modo, puede ver todos los resultados de esa acción en la parte inferior del cuadro.

## Uso de la vista Código para editar un flujo

Además de usar el diseñador, puede editar directamente la definición de un flujo de forma manual. Para ello, haga clic en el botón de la vista Código en la barra de comandos. Se abrirá un editor completo que muestra la definición del flujo que acaba de editar.

Un editor de texto puede facilitar ciertas operaciones; por ejemplo, puede copiar y pegar cualquier cantidad de acciones dentro del mismo flujo (o incluso entre flujos). También puede agregar o eliminar fácilmente secciones completas del flujo de una sola vez.

### Parámetros

También hay algunas capacidades que solo se muestran en la vista de código, por ejemplo, los parámetros. Los parámetros facilitan la reutilización de valores a través de un flujo. Por ejemplo, si tiene una dirección de correo electrónico que quiera incluir en varias acciones, puede crear un parámetro.

Para definir parámetros, vaya al objeto parameters {} y agregue lo siguiente en su interior: “emailAddress” : { “type” : “string”, “defaultValue” : “me@example.com” }

Ahora, desplácese a la acción de envío por correo electrónico. Verá la entrada para el correo electrónico. Reemplace el correo electrónico por lo siguiente:

    @parameters(‘emailAddress’)

Los parámetros son una buena forma de extraer valores que probablemente cambie mucho.

## Diferentes tipos de desencadenadores

Hay varias maneras de iniciar flujos y cada una de ellas tiene un comportamiento ligeramente distinto. Por lo tanto, asegúrese de seleccionar la correcta para sus escenarios.

### Desencadenadores de sondeo

Algunos desencadenadores comprueban una aplicación de API en función de un intervalo. Puede definir el intervalo pero también es necesario pasar un campo denominado "triggerState". Estado de desencadenador es información que se pasa a la invocación del desencadenador para saber lo que devolvió la vez anterior que se realizó la comprobación. Estado de desencadenador será normalmente la cadena:

    @triggers().outputs.body.triggerState

### Desencadenadores de devolución de llamada

Otros desencadenadores devuelven el flujo cuando tienen una nueva solicitud. En este caso, debe proporcionar al desencadenador el URI de devolución de llamada del flujo. Para obtenerlo, copie el extremo de acceso de la hoja Propiedades de la pantalla Configuración cuando vaya a un flujo.

### Invocación manual

En tercer lugar, puede tener un flujo que simplemente ejecuta de forma manual. En el portal, hay un botón Ejecutar en el que puede hacer clic para iniciar un flujo.

<!--HONumber=54-->