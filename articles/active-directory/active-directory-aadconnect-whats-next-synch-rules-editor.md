
<properties 
	pageTitle="Uso del Editor de reglas de sincronización de Azure AD Connect" 
	description="Aprenda a utilizar el Editor de reglas de sincronización de Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Editor de reglas de sincronización de Azure AD Connect


## Uso del editor de reglas de sincronización

En Azure AD Connect, puede configurar y ajustar el flujo de objetos y atributos entre Azure AD y sus directorios locales mediante la configuración de reglas de sincronización.

Una regla de sincronización es un objeto de configuración con un conjunto de atributos que se pasan cuando se cumple una condición. También se utiliza para describir cómo se relaciona un objeto en un espacio de conector con un objeto en el metaverso, proceso conocido como unión o coincidencia. Las reglas de sincronización tienen una prioridad que indica cómo se relacionan entre sí. Una regla de sincronización con un menor valor numérico en prioridad tiene una prioridad más alta y, en el caso de un conflicto en el flujo de atributos, la prioridad superior prevalecerá en su resolución. Las reglas de sincronización pueden configurarse mediante el Editor de reglas de sincronización.

Nos fijaremos a modo de ejemplo en la regla de sincronización "In from AD – User AccountEnabled". Marcaremos esta línea en el Editor de reglas de sincronización y seleccionaremos Editar. Una regla de sincronización tiene cuatro secciones de configuración: Descripción, Filtro de ámbito, Reglas de unión y Transformaciones.

### Descripción
La primera sección proporciona información básica como el nombre y la descripción.

<center>![Regla de unión](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync1.png) </center>

También encontramos información acerca del sistema conectado con el que se relaciona esta regla, el tipo de objeto del sistema conectado al que se aplica y el tipo de objeto del metaverso. El tipo de objeto del metaverso siempre es persona, independientemente de si el tipo de objeto de origen es usuario, iNetOrgPerson o contacto. El tipo de objeto del metaverso nunca debe cambiar, por lo que se crea como un tipo genérico. El tipo de vínculo se puede establecer en Unión, Unión permanente o Aprovisionamiento. Esta configuración funciona junto con la sección Reglas de unión, que abordaremos más adelante.

### Filtro de ámbito

La sección Filtro de ámbito se utiliza para configurar cuándo se debe aplicar una regla de sincronización. Como el nombre de la regla de sincronización que estamos examinando indica que solo debe aplicarse para los usuarios habilitados, el ámbito se configura de modo que el atributo userAccountControl de AD no deba tener establecido el bit 2. Cuando encontremos un usuario en AD aplicaremos esta regla si userAccountControl se ha establecido en el valor decimal 512 (usuario normal habilitado), pero no se aplicará si el usuario que encontremos tiene userAccountControl establecido en 514 (usuario normal deshabilitado).

<center>![Regla de unión](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

El filtro de ámbito tiene grupos y las cláusulas que se pueden anidar. Deben cumplirse todas las cláusulas dentro de un grupo para que se aplique una regla de sincronización. Cuando se definen varios grupos, se debe cumplir al menos uno para que la regla se aplique. Por ejemplo, un OR lógico se evalúa entre grupos y un AND lógico se evalúa dentro de un grupo. Un ejemplo de esto se puede encontrar en la regla de sincronización saliente Out to AAD – Group Join, que se muestra a continuación. Hay dos grupos de filtros de sincronización, uno para los grupos de seguridad (securityEnabled EQUAL True) y otro para los grupos de distribución (securityEnabled EQUAL False).

<center>![Regla de unión](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

Esta regla se usa para definir qué grupos se deben aprovisionar a ADD. Los grupos de distribución deben tener el correo habilitado para sincronizarse con ADD, pero esto no es necesario para los grupos de seguridad. Como se puede ver además, también se evalúan bastantes atributos más.

###Reglas de unión
La tercera sección se usa para configurar cómo se relacionan los objetos del espacio conector con los objetos del metaverso. La regla que hemos examinado antes no tiene ninguna configuración para Reglas de unión, por lo que vamos a examinar la regla In from AD – User Join en su lugar.

<center>![Regla de unión](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

El contenido de las reglas de unión dependerá de la opción de coincidencia seleccionada en el asistente para instalación. Para una regla entrante, la evaluación se inicia con un objeto en el espacio conector de origen y cada grupo de las reglas de unión se evalúa en secuencia. Si se evalúa si un objeto de origen coincide exactamente con un objeto del metaverso usando una de las reglas de unión, los objetos se unen. Si se han evaluado todas las reglas y no hay coincidencia, se usa el tipo de vínculo de la página de descripción. Si este valor se establece en Provision, se crea un nuevo objeto en el destino, el metaverso. Aprovisionar un nuevo objeto en el metaverso también se denomina proyectar un objeto en el metaverso. Las reglas de unión solo se evalúan una vez. Cuando un objeto del espacio conector y un objeto del metaverso se unen, permanecerán unidos mientras el ámbito de la regla de sincronización se siga cumpliendo. Cuando se evalúan reglas de sincronización, solo debe haber en el ámbito una regla de sincronización con reglas de unión definidas. Si se encuentran varias reglas de sincronización con reglas de unión para un objeto, se emite un error. Por este motivo, lo mejor es tener solo una regla de sincronización con una unión definida cuando haya varias reglas de sincronización en el ámbito para un objeto. En la configuración de origen para Azure AD Connect, estas reglas se pueden encontrar consultando el nombre y buscando las que tienen la palabra Join al final del mismo. Una regla de sincronización sin ninguna regla de unión definida aplicará los flujos de atributo si otra regla de sincronización ha unido los objetos o ha aprovisionado un nuevo objeto en el destino.

###Transformaciones
La sección sobre las transformaciones define todos los flujos de atributo que se aplicarán al objeto de destino cuando se unan los objetos y se cumpla el filtro de ámbito. Volviendo a nuestra regla de sincronización In from AD – User AccountEnabled, encontraremos las transformaciones siguientes:

<center>![Regla de unión](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

Para ponerlo en contexto, en una implementación de bosque cuenta-recurso esperamos encontrar una cuenta habilitada en el bosque de cuenta y una cuenta deshabilitada en el bosque de recurso con la configuración de Exchange y Lync. La regla de sincronización que estamos examinando contiene los atributos necesarios para el inicio de sesión y queremos que fluyan desde el bosque en el que hemos encontrado una cuenta habilitada. Todos estos flujos de atributo se combinan en una regla de sincronización. Una transformación puede tener tipos diferentes: constante, directo y expresión. En un flujo de tipo constante siempre fluirá un valor en particular, en el caso anterior estableceremos siempre el valor True en el atributo del metaverso denominado accountEnabled. En un flujo de tipo directo fluirá el valor del atributo del origen hacia el atributo de destino. El tercer flujo es de tipo expresión y permite configuraciones más avanzadas. El lenguaje de la expresión es VBA (Visual Basic para Aplicaciones), por lo que un usuario con experiencia en Microsoft Office o VBScript reconocerá el formato Los atributos se especifican entre corchetes, [nombreAtributo]. Los nombres de atributo y los nombres de función distinguen entre mayúsculas y minúsculas, pero el Editor de reglas de sincronización evaluará las expresiones y proporcionará una advertencia si la expresión no es válida. Todas las expresiones se expresan en una única línea con funciones anidadas. Para mostrar la eficacia del lenguaje de configuración, encontrará a continuación el flujo para pwdLastSet, pero con comentarios adicionales insertados:

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

El tema de transformación es amplio y proporciona una gran parte de la configuración personalizada posible con Azure AD Connect. La configuración personalizada no se explicará en este documento de información general, pero examinaremos algunos de los flujos de atributo adicionales más adelante en este documento.
 

<!---HONumber=July15_HO3-->