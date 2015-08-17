<properties
	pageTitle="Conector de SFTP"
	description="Introducción al conector de SFTP"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sameerch"/>

# Uso del conector de SFTP en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

El conector de SFTP permite mover datos a un servidor SFTP o desde él. Permite descargar o cargar archivos o mostrar una lista de archivos desde y hacia un servidor de SFTP.

## Creación de un conector de SFTP para la aplicación lógica ##
Para usar el conector de SFTP, deberá crear primero una instancia de la aplicación de API del conector de SFTP. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la esquina superior derecha del Portal de Azure.
2.	Vaya a "Web y móvil" y busque "Conector de SFTP".
3.	Configure el conector de SFTP de la siguiente forma:

	![][1] - **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector - **Suscripción**: elija una suscripción en la que desea crear este conector - **Grupo de recursos**: seleccione o cree un grupo de recursos donde debe residir el conector - **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web - **Nivel de precios**: elija un nivel de precios para el conector - **Nombre**: asigne un nombre al conector SFTP - **Configuración del paquete** - **Dirección del servidor**: especifique el nombre o la dirección IP del servidor SFTP - **Aceptar cualquier tecla del host del servidor SSH**: determina si se debe aceptar alguna huella digital de la clave pública SSH del servidor. Si se establece en false, la tecla del host se comparará con la clave especificada en la propiedad "Huella digital de la tecla del host del servidor SSH" -**Tecla del host del servidor SSH**: especifique la huella digital de la tecla de host pública para el servidor SSH -*opcional*-. **Carpeta raíz**: especifique una ruta de acceso a la carpeta raíz. Si esté valor está en blanco, se establecerá de manera predeterminada en la raíz. - **Cifrar cifrado**: especifique el código del cifrado - *opcional*. - **Puerto del servidor**: especifique el número del puerto del servidor SFTP
4. Haga clic en Crear. Se creará un nuevo conector de SFTP.

5. Acceda a la aplicación de API que acaba de crear mediante Examinar -> Aplicaciones de API -> <Name of the API App just created>; puede ver que el componente "Seguridad" no está configurado.

	![][2]
6. Haga clic en el componente "Seguridad" para configurar la seguridad (Nombre de usuario, Contraseña, Clave privada, Contraseña del archivo PPK) para el conector SFTP. Seleccione la pestaña de autorización "Contraseña", "Privatekey" o "MultiFactor" en Seguridad y proporcione las propiedades necesarias.

	![][3] ![][4] ![][5]
6. Una vez guardada la configuración de seguridad, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de SFTP.

## Uso del conector de SFTP en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de SFTP como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de SFTP.

	![][6]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

	![][7]
3.	El conector de SFTP aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.

	![][8]
4.	Puede quitar la aplicación de la API del conector de SFTP en el editor haciendo clic en "Conector de SFTP".


6.	Ahora puede usar el conector de SFTP en el flujo. Puede utilizar el archivo recuperado desde el desencadenador de SFTP ("TriggerOnFileAvailable") en otras acciones del flujo.

	**Nota:** el desencadenador de SFTP "TriggerOnFileAvailable" eliminará el archivo recuperado después de procesarlo.

8.	Configure las propiedades de entrada para el desencadenador de SFTP de la forma siguiente:

	- **Ruta de la carpeta:**: especifique la ruta de acceso a la carpeta desde la que es necesario recuperar los archivos.
	- **El tipo de archivo: texto o binario**: seleccione el tipo de archivo.
	- **Máscara de archivo**: especifique la máscara de archivo que se va a aplicar para recuperar los archivos. '*' recupera todos los archivos de la carpeta especificada.
- **Máscara para excluir archivo**: especifique la máscara de archivo que se aplicará para excluir archivos. Si también se ha establecido la propiedad "Máscara de archivo", primero se aplicará la máscara para excluir archivo.


	![][9] ![][10]

7.	De un modo similar, puede usar las acciones de SFTP en el flujo. Puede usar la acción "Cargar archivo" para cargar un archivo en el servidor de SFTP. Configure las propiedades de entrada para la acción "Cargar archivo" de la siguiente manera:

	- **Contenido**: especifique el contenido del archivo que se va a cargar.
	- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
	- **Ruta de archivo**: especifique la ruta de acceso del archivo que se va a cargar.
	- **Sobrescribir**: especifique "true" para sobrescribir el archivo si ya existe.
	- ****Anexar si existe **: especifique "true" o "false". Cuando se establece en "true", los datos se anexan al archivo (si existe). Cuando se establece en "false", se sobrescribe el archivo (si existe)
- **Carpeta temporal**: si se proporciona, el adaptador cargará el archivo en la ’Ruta a la carpeta temporal’ y una vez que se realiza la carga, el archivo se moverá a la ’Ruta de carpeta’. La ’Ruta a la carpeta temporal’ debe estar en el mismo disco físico que la ’Ruta de carpeta’ para asegurarse de que la operación de mover es atómica. La carpeta temporal solo puede usarse cuando la propiedad «Anexar si existe» está deshabilitada.

	![][11] ![][12]





<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!---HONumber=August15_HO6-->