<properties 
	pageTitle="Conectores empresariales del Servicio de aplicaciones de Microsoft Azure | Azure" 
	description="Aprenda a crear y configurar un conector empresarial; arquitectura de microservicios" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Conectores empresariales del Servicio de aplicaciones de Microsoft Azure
Servicio de aplicaciones de Microsoft Azure (o Servicio de aplicaciones para abreviar) incluye varios conectores que pueden utilizarse con sistemas empresariales, como SAP y Marketo. Mediante estos conectores, puede conectarse fácilmente a un sistema empresarial y realizar diferentes tareas.

Estos conectores empresariales ofrecen las funcionalidades "Desencadenador" o "Acción". Un desencadenador inicia una nueva instancia en función de un evento específico, como la actualización de una entrada de MongoDB. Una acción es el resultado, como después de actualizar una entrada de MongoDB, obtener luego una entrada de MongoDB.


## ¿Qué es un conector empresarial?
Los conectores empresariales son "aplicaciones de API" existentes que pueden conectarse a sistemas empresariales locales y basados en la nube, como:

Conector | Descripción
--- | ---
Marketo | Conector **Acción**. Conéctese a Marketo y realice acciones diferentes como crear y actualizar clientes potenciales, obtener cambios de clientes potenciales, programar campañas, solicitar campañas, obtener clientes potenciales, obtener información de campañas y listas, agregar clientes potenciales a listas y quitar clientes potenciales de listas.
MongoDB | Conector **Desencadenador** y **Acción**. Conéctese a la base de datos del servidor Mongo o al servidor Mongo local. En una colección de MongoDB, realice diferentes acciones, como crear, actualizar, obtener y eliminar entradas. 
QuickBooks | Conector **Acción**. Conéctese a QuickBooks y use las aplicaciones para crear, actualizar, leer, eliminar y consultar las diferentes entidades de Intuit QuickBooks, como clientes, artículos, facturas, etc.
Salesforce |Conector **Acción**. Conéctese a Salesforce para administrar diferentes entidades como cuentas, clientes potenciales, oportunidades, casos, etc. en su cuenta de Salesforce. Puede realizar distintas acciones en las diferentes entidades, como crear, actualizar, upsert, consultar y eliminar. 
SugarCRM | Conector **Acción**. Conéctese a SugarCRM en línea y use las aplicaciones para crear, actualizar, leer y eliminar diferentes tipos de módulos, como cuentas, contactos, productos, etc.
SAP | Conector **Acción**. Conéctese a un servidor SAP local. Invoque RFC, BAPI y tRFC. También puede enviar IDOC.


Con estos conectores, puede completar diferentes tareas en el sistema de la empresa. Por ejemplo, mediante el conector QuickBooks, puede realizar tareas típicas dentro de QuickBooks, como agregar a un nuevo cliente, actualizar una factura o eliminar un artículo.

Puede crear fácilmente tantos conectores como desee. También puede reutilizar un único conector dentro de varios escenarios o flujos de trabajo.

Por ejemplo, supongamos que se encuentra en una conferencia y se encuentra con un cliente que desea comprar 10 artículos de su producto. En su dispositivo móvil, puede abrir la aplicación móvil que usa el conector QuickBooks creado, agregar los detalles del cliente y crear una factura.

Mientras está en la conferencia, un cliente de su ubicación empresarial desea pagar una factura existente. El empleado abre una aplicación que usa el mismo conector QuickBooks que creó usted, busca la información del cliente y actualiza la factura para reflejar el nuevo saldo.

Estos son flujos diferentes y usan el mismo conector QuickBooks. Puede crear y usar estos conectores sin escribir ningún código. Comencemos.


## Requisitos de inicio
Para crear un conector que usa una empresa, existen algunos recursos necesarios. Estos elementos los debe crear el usuario antes de que se puedan usar en el conector. Estos requisitos son:

Requisito | Descripción
--- | ---
Espacio de nombres de Bus de servicio y sus valores de clave | Cuando el uso se haga en SAP local o MongoDB, se necesita tanto un espacio de nombres de Bus de servicio como sus valores de clave. Si no se conecta a un sistema local, no es necesario un espacio de nombres de Bus de servicio.<br/><br/>[Creación de un espacio de nombres de Bus de servicio](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Administrador de conexiones híbridas local | Cuando se conecta a un sistema local, instala el Administrador de conexiones híbridas en dicho sistema. Cuando crea el conector local, la descarga está disponible en la configuración o las propiedades del conector. 
Propiedades específicas del sistema | Cuando se usan sistemas empresariales, existen propiedades específicas del sistema (como el nombre de usuario y la contraseña) que necesita. 


## Creación de un conector
Se puede crear un conector con el Portal de Azure.

### Creación de un conector en el Portal de Azure
En el Portal de Azure, puede crear un conector empresarial al crear aplicaciones lógicas, aplicaciones web o aplicaciones móviles. O bien, puede crear uno con su propia hoja. Ambas formas son sencillas, por lo que depende de sus necesidades o preferencias. Algunos usuarios prefieren crear primero todos los conectores con sus propiedades específicas. A continuación, cree las aplicaciones lógicas, web o móviles y agregue el conector creado.

Los pasos siguientes crean un conector empresarial mediante la hoja del conector:

1. En el Panel de inicio del Portal de Azure (la página de inicio), seleccione **Marketplace**. **Aplicaciones de API** enumera todos los conectores existentes. También puede **buscar ** un conector específico.
2. Seleccione el conector. En la nueva hoja, seleccione **Crear**. 
3. Especifique las propiedades: 

	Propiedad | Descripción
--- | ---
Nombre | Escriba cualquier nombre para el conector. Por ejemplo, denomínelo *SAPConnector*, *SalesForceGetAccounts* o  *QuickBooksGetItems*.
Configuración del paquete | Especifique la configuración del sistema empresarial, como *Nombre de usuario de SAP* o *Dirección URL del servidor de SugarCRM*. Vea [Propiedades específicas del sistema empresarial](#AddProperties) en este tema. 
Plan de servicio de aplicación | Muestra el plan de pagos. Puede cambiarlo si necesita más o menos recursos.
Nivel de precios | Propiedad de solo lectura que muestra la categoría de precio de su suscripción de Azure. 
El grupos de recursos | Cree uno nuevo o utilice un grupo ya existente. Todas las aplicaciones de API y conectores de las aplicaciones lógicas, aplicaciones web y aplicaciones móviles deben estar en el mismo grupo de recursos. En <br/><br/>[Uso de grupos de recursos](../resource-group-overview.md) se explica esta propiedad. 
La suscripción | Propiedad de solo lectura que muestra su suscripción actual.
Ubicación | La ubicación geográfica que hospeda el servicio de Azure. 
Agregar al Panel de inicio | Seleccione esto para agregar el conector a su panel de inicio (la página principal)


	**<a name="AddProperties"></a>Propiedades específicas del sistema empresarial**

> [AZURE.IMPORTANT]Cada conector tiene propiedades que son específicas de ese sistema empresarial. Al conectarse a SAP, escriba las propiedades específicas de SAP. Al conectarse a Salesforce, escriba las propiedades específicas de Salesforce, etc. En la tabla siguiente se enumeran las propiedades del sistema empresarial necesarias.
	
	Enterprise System | Required Properties
--- | ---
Marketo | <ul><li>Extremo</li><li>Nombre de proveedor</li></ul>
MongoDB| <ul><li>Cadena de conexión</li><li>Host</li><li>Puerto</li><li>Nombre de usuario</li><li>Contraseña</li><li>Base de datos</li><li>Utilizar cifrado SSL</li><li>Local: escriba False si está basado en la nube. Si el sistema de MongoDb es local, escriba True y especifique también las siguientes propiedades:<ul><li>Nombre de clave de acceso compartido</li><li>Espacio de nombres de Bus de servicio</li><li>Ruta de acceso de retransmisión</li><li>Enviar clave</li></ul></li></ul>
QuickBooks | <ul><li>Identificador de la compañía</li><li>Nombre de proveedor</li></ul>
SAP | <ul><li>Nombre de host</li><li>Idioma</li><li>Nombre de usuario</li><li>Contraseña</li><li>Número de sistema</li><li>Cadena de conexión de Bus de servicio</li><li>Nombres de RFC</li><li>Nombres de TRFC</li><li>Nombres de BAPI</li><li>Nombre de IDOC</li></ul>
Salesforce | <ul><li>Nombre de proveedor</li><li>Instancia</li><li>Versión</li><li>Entidades (valores separados por comas)</li></ul>
SugarCRM | <ul><li>Dirección URL del servidor</li><li>Nombre de proveedor</li><li>Nombres de módulo</li></ul>

4. Seleccione **Crear**.


## Instalación del Administrador de conexiones híbridas local
Después de crear el conector empresarial que se conecta a un sistema local, como SAP, instale el Administrador de conexiones híbridas:

1. En el sistema empresarial local, abra el Portal de administración de Azure y seleccione el conector empresarial. 
2. En la hoja, seleccione **Conexión híbrida**. 
3. Copie el valor **Cadena de configuración de puerta de enlace principal**. 
4. Seleccione **Descargar y configurar**. Durante la instalación, pegue la cadena de configuración de la puerta de enlace que copió y continúe con la instalación. 
5. Para confirmar la conectividad, abra la hoja del conector empresarial. El estado debe mostrar **Conectado**. 

[La integración con un servidor SAP local](app-service-logic-integrate-with-an-on-premise-SAP-server.md) proporciona un ejemplo.


## Supervisión de las aplicaciones de API
En el Portal de administración de Azure, abra su aplicación de API empresarial. En la sección **Operaciones**, puede ver distintas operaciones de administración. Por ejemplo, puede:

- Ver sucesos informativos y de error
- Ver el uso de memoria y el recuento de subprocesos del proceso de trabajo (w3wp)
- Ver los registros de la aplicación y del servidor web

Más información en [Supervisión de las aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md).


## Adición del conector a la aplicación 
Servicio de aplicaciones de Microsoft Azure expone diferentes tipos de aplicación que pueden usar estos conectores. Puede crear conectores nuevos o agregar conectores existentes a aplicaciones lógicas, aplicaciones móviles o aplicaciones web.

Dentro de la aplicación, con el simple hecho de seleccionar el conector desde la Galería, automáticamente se agrega a la aplicación.

Los siguientes pasos agregan un conector empresarial a aplicaciones lógicas, aplicaciones móviles o aplicaciones web:

1. En el Panel de inicio del Portal de Azure (página principal), vaya a  **Marketplace** y busque las aplicaciones lógicas, móviles o web.

	Si va a crear una nueva aplicación, busque aplicaciones lógicas, aplicaciones móviles o aplicaciones web. Seleccione la aplicación en la nueva hoja, seleccione **Crear**. En [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md) se muestran los pasos.

2. Abra su aplicación y seleccione **Desencadenadores y acciones**.

3. En la **Galería**, agregue los conectores creados, lo que automáticamente los agrega a la aplicación.
4. Seleccione **Aceptar** para guardar los cambios. 


## Seguridad
Algunos de los conectores empresariales tienen seguridad OAuth. Al agregar el conector a la aplicación, se  **autoriza** al conector mediante la conexión al sistema empresarial con su cuenta de inicio de sesión y acepta los términos. Al hacerlo, la aplicación y el conector usan la cuenta de inicio de sesión para autenticarse con el sistema.


### Acceso a un conector con API de REST
[Vea la documentación sobre cómo usar las API de REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Más recursos de conector empresarial
[Integración con un servidor SAP local](app-service-logic-integrate-with-an-on-premise-SAP-server.md)<br/> [¿Qué son los conectores y las aplicaciones de la API de BizTalk?](app-service-logic-what-are-biztalk-api-apps.md)


## Lea acerca de las aplicaciones lógicas y las aplicaciones web
[¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)<br/> [Sitios web y aplicaciones web en el Servicio de aplicaciones de Azure](../app-service-web/app-service-web-app-azure-portal.md)



## Más conectores
[Conectores de integración de BizTalk](app-service-logic-integration-connectors.md)<br/> [Conectores negocio a negocio](app-service-logic-b2b-connectors.md)<br/> [ Conectores sociales](app-service-logic-social-connectors.md)<br/> [Conectores de protocolo](app-service-logic-protocol-connectors.md)<br/> [Conectores de aplicaciones y servicios de datos](app-service-logic-data-connectors.md)<br/> [Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)<br/><br/> [¿Qué son los conectores y las aplicaciones de la API de BizTalk?](app-service-logic-what-are-biztalk-api-apps.md)
<!--HONumber=54--> 