<properties
   pageTitle="Conector de SugarCRM"
   description="Uso del conector de SugarCRM"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#Uso del conector de SugarCRM en la aplicación lógica#

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. El conector de SugarCRM le permite crear y modificar diferentes entidades como cuentas, clientes potenciales, contactos etc. A continuación se muestran los escenarios de integración típicos relacionados con SugarCRM.

- Sincronización de cuentas entre sistemas ERP y SugarCRM como SAP

- Sincronización de cuentas, contactos y clientes potenciales entre Marketo y SugarCRM

- Solicitud de flujo de caja de SugarCRM a sistemas ERP


Como parte de la configuración del paquete del conector, el usuario puede especificar entidades que el conector puede administrar y las acciones y los parámetros de entrada y salida se completarán de forma dinámica.

##Acciones de conector de SugarCRM##
A continuación se muestran las distintas acciones disponibles en el conector de SugarCRM.

- Crear módulo: use esta acción para crear un nuevo registro para el módulo SugarCRM, como cuentas, clientes potenciales y contactos.

- Actualizar módulo: use esta acción para actualizar un registro existente para el módulo SugarCRM.

- Eliminar módulo: use esta acción para eliminar un registro existente para el módulo SugarCRM.

- Lista de módulo: use esta acción para mostrar una lista de los registros filtrados. Si no se especifica ninguna consulta se devuelven todos los registros.

- Obtener módulo: use esta acción para recuperar un único registro del módulo especificado.

- Obtener recuento de registros: use esta acción para obtener el número de registros en el módulo que coinciden con la consulta. Si no se especifica ninguna consulta, se devuelve el número total de registros del módulo.

- Comprobar módulo duplicado: use esta acción para buscar registros duplicados dentro de un módulo.

*Nota*: para obtener más detalles sobre los argumentos admitidos en las consultas, consulte la documentación de API de REST de SugarCRM.

##Creación de una aplicación de API del conector de SugarCRM##
1.	Vaya a portal.azure.com. Abra Azure Marketplace mediante la opción +NUEVO en la esquina superior izquierda del Portal de Azure.
2.	Diríjase a "Marketplace > Todo" y busque "SugarCRM".
3.	Para configurar el conector de SugarCRM, proporcione los detalles del plan del Servicio de aplicaciones, el grupo de recursos y seleccione el nombre de la aplicación de API.
4. Configure los ajustes del paquete del conector de SugarCRM. A continuación se proporciona la configuración del paquete que es necesario proporcionar para crear el conector:

	<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>Obligatorio</b></td>
    <td><b>Descripción</b></td>
  </tr>
  <tr>
    <td>Dirección URL del sitio</td>
    <td>Sí</td>
    <td>Especifique la dirección URL de la instancia de SugarCRM, por ejemplo: https://abcde1234.sugarcrm.com.</td>
  </tr>
  <tr>
    <td>Id. de cliente</td>
    <td>Sí</td>
    <td>Especifique la clave de consumidor de clave de oauth 2.0 en SugarCRM </td>
  </tr>
  <tr>
    <td>Secreto del cliente</td>
    <td>Sí</td>
    <td>Especifique el secreto del consumidor de la clave de oauth 2.0 en SugarCRM </td>
  </tr>
<tr>
    <td>Nombre de usuario</td>
    <td>Sí</td>
    <td>Especifique el nombre de usuario del usuario de SugarCRM</td>
  </tr>
	<tr>
    <td>Password</td>
    <td>Sí</td>
    <td>Especifique la contraseña del usuario de SugarCRM</td>
  </tr>
  <tr>
    <td>Nombres de módulo</td>
    <td>Sí</td>
    <td>Especifique los módulos de SugarCRM, como cuentas, contactos, productos, etc. en los que desea realizar la operación<br><br>Ejemplo: Cuentas, clientes potenciales, contactos</td>
  </tr>
</table>![][9]



##Crear una aplicación lógica##
Vamos a crear una aplicación lógica simple que cree una cuenta en SugarCRM y actualice la información de la dirección de facturación de la misma cuenta.

1.	Inicie sesión en el Portal de Azure y haga clic en "Nuevo -> Web+móvil -> Aplicación lógica"

	![][1]

2.	En la página "Crear aplicación lógica", especifique la información necesaria, como el nombre, el plan de servicio de la aplicación y la ubicación.

	![][2]

3.	Haga clic en "Desencadenadores y acciones" y aparecerá la pantalla del editor de aplicación lógica. Seleccione "Ejecutar esta lógica manualmente", lo que significa que esta aplicación lógica solo se puede invocar manualmente.


5.	Expanda ’Aplicaciones de API’ en este grupo de recursos en la Galería para ver todas las Aplicaciones de API disponibles. Seleccione «SugarCRM» en la galería y el «Conector de SugarCRM» se agregará al flujo.


	![][3]

6.	Seleccione la acción "Crear cuenta" y se mostrarán los parámetros de entrada.

	![][4]

12.	Proporcione un nombre como ’Cuenta Microsoft’ y haga clic en ✓.

	![][5]

13.	Seleccione ’Conector de SugarCRM’ en la sección ’Usados recientemente’ en la galería y se agregará una nueva acción de SugarCRM.

14.	Seleccione "Actualizar cuenta" (estará en Acciones avanzadas "...") de la lista de acciones y se mostrarán los parámetros de entrada de la acción "Actualizar cuenta".

	![][6]

15.	Haga clic en "..." junto a "Id. de registro" para elegir el valor del identificador de la salida de la acción "Crear cuenta".

	![][7]

16.	Proporcione los valores de la información de dirección de facturación y haga clic en ✓.

	![][8]

17. Haga clic en Aceptar en la pantalla del editor de Aplicación lógica y, a continuación, haga clic en ’Crear’. Se tardará aproximadamente 30 segundos en completar la creación.

18. Busque la aplicación lógica creada recientemente y haga clic en "Ejecutar ahora" para iniciar una ejecución.

19. Puede comprobar que se crea una nueva cuenta llamada ’Cuenta Microsoft’ en su cuenta de SugarCRM y la misma cuenta también se actualiza con la información de dirección de facturación.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=July15_HO4-->