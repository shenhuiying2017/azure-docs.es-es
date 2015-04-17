<properties 
   pageTitle="Uso de reglas de BizTalk" 
   description="En este tema se tratan las características de las reglas de BizTalk y proporciona instrucciones sobre su uso" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="andalmia"/>

#Uso de reglas de BizTalk 

Las reglas de negocio encapsulan las directivas y decisiones que controlan los procesos de negocio. Estas directivas pueden estar definidas formalmente en manuales de procedimiento, contratos o acuerdos, o bien pueden existir como conocimiento o experiencia que tienen los empleados. Estas directivas son dinámicas y están sujetas a cambios a lo largo del tiempo debido a cambios en los planes de negocios, normativas u otras razones.

Implementar estas directivas en los lenguajes de programación tradicionales requiere mucho tiempo y coordinación y no permite a los no programadores participar en la creación y mantenimiento de directivas de negocio. Las reglas de negocio de BizTalk proporcionan una manera rápida de implementar estas directivas y desacoplar el resto del proceso de negocio. Esto permite realizar los cambios necesarios en las directivas de negocio sin afectar al resto del proceso de negocio.

##Por qué reglas

Hay 3 razones principales para utilizar las reglas de negocio de BizTalk en el proceso de negocio:

* Desacopla la lógica de negocio desde el código de aplicación
- Permite que los analistas de negocio tengan más control sobre la administración lógica del negocio
+ Los cambios en la lógica de negocios van a producción más rápido

###Conceptos de reglas

##Vocabulario

Los términos usados para definir acciones y condiciones de la regla se suelen expresar por dominio o nomenclatura específica del sector. Por ejemplo, un usuario de correo electrónico escribe reglas en términos de mensajes "recibido de" y mensajes "recibidos después de," mientras que un analista de negocio escribe reglas en términos de "factores de riesgo" e "importe de la cobertura".
Bajo esta terminología específica del dominio están los artefactos tecnológicos (objetos, tablas de base de datos y documentos XML) que implementan condiciones y acciones de regla. Los vocabularios están diseñados para cubrir el hueco entre la semántica del negocio y la implementación.

Por ejemplo, un enlace de datos para el estado de aprobación puede señalar a una determinada columna en una fila específica en una base de datos, representada como una consulta SQL. En lugar de insertar este tipo de representación compleja en una regla, puede crear una definición de vocabulario asociada a ese enlace de datos, con un nombre descriptivo de "Estado". Posteriormente puede incluir "Estado" en cualquier número de reglas y el motor de reglas puede recuperar los datos correspondientes de la tabla.
Por _vocabulary_ se entiende una colección de definiciones que consta de nombres descriptivos de los objetos informáticos utilizados en las acciones y condiciones de regla. Las definiciones de vocabulario hace que las personas de un dominio de negocio concreto lean, comprendan y compartan las reglas más fácilmente.

##Regla

Las reglas de negocio son instrucciones declarativas que rigen el comportamiento de los procesos de negocio. Una regla consta de una condición y acciones. La condición se evalúa y si se evalúa como true, el motor de reglas inicia una o más acciones.
Las reglas en el marco de reglas de negocio se definen mediante el siguiente formato:

_IF_ _condición_ _THEN_ _acción_

Suponga el ejemplo siguiente.

*IF el importe es inferior o igual a los fondos disponibles*  
*THEN realizar la transacción e imprimir el recibo*

Esta regla determina si una transacción se llevará a cabo mediante la aplicación de la lógica de negocio, en forma de una comparación de dos valores monetarios, en forma de un importe de transacción y los fondos disponibles.
Puede utilizar la regla de negocio para crear, modificar e implementar reglas de negocio. Como alternativa, puede realizar las tareas anteriores mediante programación.

###Condiciones

Una condición es una expresión (booleana) verdadero/falso que consta de uno o más predicados.
En nuestro ejemplo, el predicado menor o igual que se aplica al importe y los fondos disponibles. Esta condición siempre se evaluará como verdadero o falso.
Los predicados se pueden combinar con los operadores lógicos AND, OR y NOT para formar una expresión lógica que posiblemente es bastante grande, pero siempre se evaluarán como verdadero o falso.

###Acciones

Las acciones son las consecuencias funcionales de la evaluación de condición. Si se cumple una condición de regla, se inician las acciones correspondientes.
En nuestro ejemplo, "realizar la transacción" e "imprimir ek recibo" son acciones que se llevan a cabo cuando, y solo cuando se cumple la condición (en este caso, "IF el importe es inferior o igual a los fondos disponibles").
Las acciones se representan en el marco de reglas de negocio mediante la realización de operaciones establecidas en documentos XML.

##Directiva

Una directiva es una agrupación lógica de reglas. El usuario redacta una directiva, la guarda, la prueba y, cuando está satisfecho con los resultados, puede utilizarla en un entorno de producción.

###Composición de directivas

Puede componer directivas en el portal de reglas. Una directiva puede contener un conjunto de reglas arbitrariamente grande, pero normalmente se redacta una directiva de reglas que pertenecen a un dominio de negocio específico dentro del contexto de la aplicación que va a utilizar la directiva.

###Prueba de la directiva
Puede realizar de forma eficaz una serie de pruebas de la directiva antes de utilizarla en un entorno de producción. El Portal de reglas permite proporcionar entradas a una directiva, ejecutar la directiva y ver la salida. La salida incluye registros, ejecución de la regla, evaluación de la condición y salidas resultantes.

##Escenario de ejemplo - reclamaciones de seguros
Veamos un escenario de ejemplo a mediad que se compone su lógica de negocio.

![Alt text][1]

En un escenario realmente sencillo de reclamaciones de seguros, el solicitante envía su reclamación de seguro (a través de cualquier cliente como sitio web, aplicación de teléfono, etc.). Esta solicitud de reclamación se envía a la unidad de procesamiento de reclamaciones de la empresa y en función del resultado del procesamiento, la notificación puede aprobarse, rechazarse o enviarse para su procesamiento manual.
En nuestro escenario, la unidad de procesamiento de reclamaciones será la que abarque la lógica de negocio para el sistema. Al echar un vistazo a esta unidad, podemos ver lo siguiente:

![Alt text][2]
 
Ahora, utilizaremos reglas de negocio para implementar esta lógica de negocio.


##Creación de Aplicaciones de API de reglas
1.	Inicie sesión en el Portal de Azure y vaya a la página principal. 
2.	Haga clic en Nuevo->Marketplace->Aplicaciones de API ->Reglas de Biz Talk->Crear
 
![Alt text][3]

3.	En la nueva hoja que se abre, escriba la siguiente información:  
	1. Nombre: asigne un nombre la aplicación de API de reglas
	1. Plan de hospedaje de aplicaciones: seleccione o cree un plan de hospedaje web
	1. Nivel de precios: elija el nivel de precios en el que quiere que resida esta aplicación:
	1. Grupo de recursos: seleccione o cree el grupo de recursos donde residirá la aplicación
	1. Ubicación: elija la ubicación geográfica donde desea implementar la aplicación.
4.	Haga clic en Crear. Dentro de unos minutos, se creará la aplicación de API de reglas de BizTalk.

##Creación de vocabulario
Después de crear una aplicación de API de reglas de BizTalk, el siguiente paso sería crear vocabularios. La expectativa es que normalmente sea el programador el rol que realice este ejercicio. Para ello, realice los pasos siguientes:
1.	Vaya a la aplicación de API creada mediante Examinar->Aplicaciones de API -><Sus reglas de Aplicaciones de API>. Esto debe llevarle a un panel de aplicación de API de reglas similar al siguiente:
 
![Alt text][4]

2.	A continuación, haga clic en "Definiciones de vocabulario". Esto mostrará la pantalla Creación de vocabulario. Haga clic en "Agregar" para empezar a agregar nuevas definiciones de vocabulario.
Hay dos tipos de definiciones de vocabulario que se admiten actualmente: Literal y XML.

##Definición literal
1.	Después de hacer clic en "Agregar", se abre una nueva hoja "Agregar definición". Escriba los siguientes valores
  1.	Nombre: solo se esperan caracteres alfanuméricos, sin ningún carácter especial. También debe ser único en su lista de definición de vocabulario existente.
  2.	Descripción: campo opcional.
  3.	Tipo: hay dos tipos compatibles. Elija Literal en este ejemplo
  4.	Tipo de entrada: permite a los usuarios seleccionar el tipo de datos de la definición. Actualmente, hay 4 tipos de datos seleccionables:
    i.	Cadena: estos valores deben escribirse entre comillas dobles ("cadena de ejemplo")  
    ii.	Booleano: puede ser verdadero o falso  
    iii.	Número: puede ser cualquier número decimal  
    iv.	Fecha y hora: significa que el tipo de la definición de fecha. Se deben especificar datos con este formato: dd/mm/aaaa hh AM\PM  
    v.	Entrada: aquí se escribe el valor de su definición. Los valores especificados aquí deben estar de acuerdo con el tipo de datos elegido. El usuario puede escribir un solo valor, un conjunto de valores separados por comas o un intervalo de valores mediante la palabra clave "to". Por ejemplo, el usuario puede especificar solo el valor 1; un conjunto de 1, 2, 3; o un intervalo de 1 a 5. Tenga en cuenta que el intervalo solo se admite para números.

##Definición de XML
1.	Si se selecciona el tipo de vocabulario como XML, es necesario especificar las entradas siguientes  
  a.	Esquema: al hacer clic aquí se abrirá una nueva hoja que permite al usuario elegir en una lista de esquemas ya cargados o bien cargar uno nuevo.   
  b.	XPATH: esta entrada solo se desbloquea después de elegir un esquema en el paso anterior. Al hacer clic aquí se mostrará el esquema que se ha seleccionado y permite al usuario seleccionar el nodo para el que se debe crear una definición de vocabulario.  
  c.	FACT: esta entrada identifica el objeto de datos que se pasaría al motor de reglas para su procesamiento. Es una propiedad avanzada y de forma predeterminada se establece en el elemento primario de la expresión XPATH seleccionada. FACT resulta especialmente importante para los escenarios de encadenamiento y colección.

### Incorporación masiva
Los pasos anteriores han capturado la experiencia para crear definiciones de vocabulario. Una vez creadas, las definiciones de vocabulario creadas aparecerán en forma de lista. Existen requisitos para poder generar varias definiciones desde el mismo esquema en lugar de repetir los pasos anteriores cada vez. Aquí es donde la capacidad de agregar en masa es muy útil. 
Hacer clic en "Agregar en masa" le llevará a una nueva hoja. El primer paso es seleccionar el esquema para el que se van a crear varias definiciones. Al hacer clic aquí se abrirá una nueva hoja que permite al usuario elegir en una lista de esquemas ya cargados o bien cargar uno nuevo.
Ahora la propiedad XPATHS se desbloquea. Al hacer clic aquí se abrirá el Visor de esquemas, donde se pueden seleccionar varios nodos.
Los nombres de las diversas definiciones creadas tomarán como valor predeterminado el nombre del nodo seleccionado. Siempre se pueden modificar después de su creación.

##Creación de directivas
Una vez que el desarrollador ha creado los vocabularios necesarios, la expectativa es que el analista de negocio sería el que crearía las directivas de negocio mediante el Portal de Azure.  
	1.	En la aplicación de reglas creada, hay una lente de directiva con la que, al hacer clic en ella, el usuario pasa a la página de creación de directivas.  
	2.	Esta página mostrará la lista de directivas, que tiene esta aplicación de reglas específica. El usuario puede agregar una nueva directiva simplemente al escribir un nombre de directiva y presionar la tecla de tabulación. Varias directivas pueden residir en una sola aplicación de API de reglas.  
	3.	Al hacer clic en la directiva creada se guiará al usuario a la página de detalles de directiva, donde se pueden ver las reglas que se encuentran en la directiva.  
	4.	Haga clic en "Agregar nuevo" para agregar una nueva regla. Esto le llevará a una nueva hoja.

##Creación de reglas
Una regla es una colección de instrucciones de condición y acción. Si la condición se evalúa como verdadera, se ejecutan las acciones. En la hoja Crear regla, asigne un nombre de regla único (para la directiva) y una descripción (opcional).
El cuadro Condición puede utilizarse para crear instrucciones condicionales complejas. Las palabras clave admitidas son las siguientes:  
1. 	And - operador condicional  
2. 	Or - operador condicional  
3. 	does\_not\_exist  
4. 	exists  
5. 	false  
6. 	is\_equal\_to  
7. 	is\_greater\_than  
8. 	is\_greater\_than\_equal\_to  
9. 	is\_in  
10. is\_less\_than  
11. is\_less\_than\_equal\_to  
12. is\_not\_in  
13. is\_not\_equal\_to  
14. mod  
15. true  

El cuadro Acción(Then) puede contener varias instrucciones, uno por línea, para crear acciones que se ejecutarán. Las palabras clave admitidas son las siguientes:  
1.	equals  
2.	false  
3.	true  
4.	halt  
5.	mod  
6.	null  
7.	update  

Los cuadros de condición y acción proporcionan IntelliSense para ayudar a los usuarios crear rápidamente una regla. Pueden activarse presionando la tecla ctrl + espacio o simplemente cuando se empieza a escribir. las palabras clave que coinciden con los caracteres escritos automáticamente se filtrarán y se mostrarán. La ventana de IntelliSense mostrará todas las palabras clave y definiciones de vocabulario.

##Encadenamiento de reenvío explícito
Las reglas de BizTalk admiten el encadenamiento de reenvío explícito. Esto significa que si los usuarios desean volver a evaluar las reglas en respuesta a determinadas acciones, pueden desencadenarlo mediante el uso de ciertas palabras clave. Las palabras clave admitidas son las siguientes:  
   1.	update <definición de vocabulario>: esta palabra clave vuelve a evaluar todas las reglas que utilizan la definición de vocabulario especificada en su condición.  
   2.	Halt: esta palabra clave detiene todas las ejecuciones de la regla

##Habilitación o deshabilitación de reglas
Cada regla de la directiva puede ser habilitada o deshabilitada. De forma predeterminada, todas las reglas están habilitadas. Las reglas deshabilitadas no se ejecutan durante la ejecución de la directiva. La habilitación o deshabilitación de reglas puede realizarse en la hoja de la regla directamente: los comandos están disponibles en la barra de comandos en la parte superior de la hoja, o bien en la directiva, donde el menú contextual (botón secundario en una regla) tiene la opción de habilitar o deshabilitar.

##Prioridad de las reglas
Todas las reglas de una directiva se ejecutan en orden. La prioridad de ejecución está determinada por el orden en que aparecen en la directiva. Esta prioridad puede cambiarse simplemente arrastrando y colocando la regla. 

##Probar directiva
Después de crear la directiva y antes de usarla en producción, se puede probar la directiva. Mediante el comando "Probar directiva", los usuarios pueden tener acceso a la hoja Probar directiva. En esta hoja puede ver una lista de las definiciones de vocabulario que se usan en la directiva y que requieren una entrada de usuario. Los usuarios pueden agregar manualmente los valores de estas entradas para su escenario de prueba. Como alternativa, los usuarios también pueden importar pruebas de XML para las entradas. Una vez incluidas todas las entradas, se puede ejecutar la prueba y las salidas de cada definición de vocabulario se mostrarán en la columna de salida para facilitar la comparación. Para ver registros descriptivos del analista de negocio, haga clic en "Ver registros" para ver los registros de la ejecución. Para guardar los registros, la opción "Guardar salida" está disponible para almacenar todos los datos relacionados para el análisis independiente.

## Uso de reglas de aplicaciones de lógica
Una vez creada y probada la directiva, ahora está lista para su uso. Los usuarios pueden crear una nueva aplicación lógica mediante Nuevo->Aplicación de lógica. En el diseñador, las reglas de BizTalk están disponibles en la galería de la derecha. Ahora se pueden arrastrar y colocar en la superficie del diseñador. Una vez hecho esto, habrá una opción para elegir qué aplicación de API de reglas se va a dirigir. Una vez hecho esto, habrá una lista de directivas disponibles que se van a configurar. Elija una directiva concreta; a continuación, es necesario especificar las entradas necesarias para la directiva. Los usuarios pueden utilizar la salida del canal de bajada de la aplicación de API de reglas de nivel inferior para tomar más decisiones.

## Uso de reglas a través de API
La aplicación de API de reglas también se puede invocar mediante un amplio conjunto de API disponibles. De esta forma, los usuarios no están restringidos a simplemente utilizar flujos y pueden utilizar reglas en cualquier aplicación mediante llamadas REST.

## Edición de vocabulario y directiva
Una de las principales ventajas de usar reglas de negocio es que los cambios realizados en la lógica de negocio pueden enviarse a producción mucho más rápido. Cualquier cambio realizado en el vocabulario y las directivas se aplica inmediatamente en producción. El usuario simplemente necesita ir a la directiva o la definición de vocabulario respectiva y realizar el cambio para que entre en vigor.

<!--Image references-->
[1]: ./media/biztalk-rules-deploy-create-manage/InsuranceScenario.PNG	
[2]: ./media/biztalk-rules-deploy-create-manage/InsuranceBusinessLogic.png
[3]: ./media/biztalk-rules-deploy-create-manage/RulesDocImg3.png
[4]: ./media/biztalk-rules-deploy-create-manage/RulesDocImg4.png


<!--HONumber=49-->