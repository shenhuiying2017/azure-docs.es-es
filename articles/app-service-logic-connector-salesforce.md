<properties 
   pageTitle="Conector de Salesforce" 
   description="Uso del conector de Salesforce" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Uso del conector de Salesforce en la aplicación lógica#

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. El conector de Salesforce le permite crear y modificar las distintas entidades como cuentas, clientes potenciales, etc. A continuación se muestran los escenarios de integración típicos relacionados con Salesforce.

- Sincronización de cuentas entre sistemas ERP y de Salesforce como SAP y QuickBooks	

- Solicitud de flujo de caja de sitemas de Salesforce a ERP


Como parte de la configuración del paquete del conector, el usuario puede especificar entidades que el conector puede administrar y las acciones y los parámetros de entrada y salida se completarán de forma dinámica. A continuación se muestran las distintas acciones disponibles en el conector de Salesforce:
 
- Crear entidad: use esta acción para crear una nueva entidad de Salesforce como Cuenta y Caso.

- Actualizar entidad: use esta acción para actualizar una entidad de Salesforce existente.

- Entidad Upsert: use esta acción para actualizar una entidad de Salesforce existente o crear una si no existe.

- Eliminar entidad: use esta acción para eliminar una entidad de Salesforce existente.

- Ejecutar consulta: use esta acción para ejecutar una consulta SELECT escrita en el lenguaje de consulta de objetos de Salesforce (SOQL).


## Creación de una aplicación lógica##
Para utilizar un conector de Salesforce en su aplicación  lógica, debe haberse creado ya la aplicación de la API del conector o puede crear uno al vuelo en la aplicación lógica. Vamos a crear una aplicación lógica simple que cree una cuenta en Salesforces y actualice la información de la dirección de facturación de la misma cuenta.

1.	Inicie sesión en el Portal de Azure y haga clic en "Nuevo -> Web+móvil -> Aplicación lógica"

	![][1]

2.	En la página "Crear aplicación lógica", especifique la información necesaria, como el nombre, el plan de servicio de la aplicación y la ubicación.

	![][2]

3.	Haga clic en "Desencadenadores y acciones" y aparecerá la pantalla del editor de aplicación lógica.

	![][3]

4.	Seleccione "Ejecutar esta lógica manualmente", lo que significa que esta aplicación lógica solo se puede invocar manualmente.

    ![][4]

5.	Expanda "Crear nuevo" en Galería para ver todas las aplicaciones de API disponibles. Seleccione "Salesforce" en la galería y "Conector de Salesforce" se agregará al flujo.


6.	El valor predeterminado de "Entidades de Salesforce" es "Cuenta, Cliente potencial, Oportunidad, Caso, Contacto". Puede agregar otras entidades, incluidas entidades personalizadas disponibles en la cuenta de Salesforce y hacer clic en ✓.

	![][5]

7.	Esto crea una nueva aplicación de la API de "Conector de Salesforce" en el mismo grupo de recursos que la aplicación lógica. Se tardará aproximadamente 30 segundos en crear la aplicación de la API.

8.	Una vez creada la aplicación, haga clic en "Autorizar" para proporcionar las credenciales de inicio de sesión de Salesforce.

	![][6]

9.	Se le redirigirá a la página de inicio de sesión de Salesforce y podrá autenticarse con las credenciales de cuenta de Salesforce.

	![][7]

	![][8]

10.	Una vez completada la autorización, se muestran todas las acciones.

	![][9]

11.	Seleccione la acción "Crear cuenta" y se mostrarán los parámetros de entrada.

	![][10]

12.	Proporcione el nombre de cuenta y haga clic en ✓. 

	![][11]

13.	Seleccione "Conector de Salesforce" en la sección "Usados recientemente" en la galería y se agregará una nueva acción de Salesforce.

14.	Seleccione "Actualizar cuenta" de la lista de acciones y se mostrarán los parámetros de entrada de la acción "Actualizar cuenta".

	![][12]

15.	Haga clic en "+" junto a "Id. de registro" para elegir el valor del identificador de la salida de la acción "Crear cuenta". 

	![][13]

16.	Proporcione valores para la calle de facturación, la ciudad de facturación, el estado de facturación y el código postal de facturación y haga clic en ✓.

	![][14]

17. Haga clic en Aceptar en la pantalla del editor de aplicación lógica y, a continuación, haga clic en 'Create'. Se tardará aproximadamente 30 segundos en completar la creación.

18. Busque la aplicación lógica creada recientemente y haga clic en 'Run' para iniciar una ejecución.

19. Puede comprobar que se crea una cuenta nueva con el nombre 'Contoso' en la cuenta de Salesforce.

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png



<!--HONumber=49-->