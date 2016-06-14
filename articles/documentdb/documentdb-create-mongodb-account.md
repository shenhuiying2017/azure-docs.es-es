<properties 
	pageTitle="Creación de una cuenta de DocumentDB con soporte de protocolo para MongoDB | Microsoft Azure" 
	description="Obtenga información acerca de cómo crear una cuenta de DocumentDB con soporte de protocolo para MongoDB, ahora disponible en una versión preliminar." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Creación de una cuenta de DocumentDB con soporte de protocolo para MongoDB mediante el Portal de Azure.

Para crear una cuenta de DocumentDB con soporte de protocolo para MongoDB, debe:

- Tener una cuenta de Azure. Si no dispone de ella, puede obtener una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).

## Creación de la cuenta  

Para crear una cuenta de DocumentDB con soporte de protocolo para MongoDB, realice los siguientes pasos.

1. En una nueva ventana, inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, luego en **Datos y almacenamiento**, haga clic en **Ver todos** y, a continuación, busque la categoría **Datos y almacenamiento** para "Protocolo de DocumentDB". Haga clic en **DocumentDB: soporte de protocolo para MongoDB**.

	![Captura de pantalla de las hojas de búsqueda de Marketplace y Datos y almacenamiento, en las que se resalta DocumentDB: soporte de protocolo para MongoDB y base de datos de Mongo](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. O bien, en la categoría **Datos y almacenamiento**, en **Almacenamiento**, haga clic en **Más** y, a continuación, haga clic en **Cargar más** una o más veces para que aparezca **DocumentDB: soporte de protocolo para MongoDB**. Haga clic en **DocumentDB: soporte de protocolo para MongoDB**.

	![Captura de pantalla de las hojas de Marketplace y Datos y almacenamiento, en las que se resalta DocumentDB: soporte de protocolo para MongoDB y base de datos de Mongo](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. En la hoja **DocumentDB: soporte de protocolo para MongoDB (vista previa)**, haga clic en **Crear** para iniciar el proceso de suscripción de vista previa.

	![Hoja DocumentDB: soporte de protocolo para MongoDB en el Portal de Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. En la hoja **Cuenta de DocumentDB**, haga clic en **Registrar para la vista previa**. Lea la información y haga clic en **Aceptar**.

	![Hoja de registro inmediato para la vista previa de DocumentDB: soporte de protocolo para MongoDB en el Portal de Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  Después de aceptar los términos de la vista previa, se le devolverá a la hoja Crear. En la hoja **Cuenta de DocumentDB**, especifique la configuración que desee para la cuenta.

	![Captura de pantalla del nuevo DocumentDB con soporte de protocolo para la hoja de MongoDB.](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


	- En el cuadro **Id.**, escriba un nombre para identificar la cuenta. Cuando se valida el **identificador**, aparece una marca de verificación verde en el cuadro **Id.** El valor de **Id.** se convierte en el nombre de host dentro del URI. El **identificador** puede contener solo minúsculas, números y el carácter "-"; debe tener entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre del punto de conexión que elija; su resultado se convertirá en el punto de conexión de la cuenta.

	- En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la cuenta. Si la cuenta tiene una sola suscripción, dicha cuenta se selecciona de manera predeterminada.

	- En **Grupo de recursos**, seleccione o cree un grupo de recursos para la cuenta. De forma predeterminada, se elegirá un grupo de recursos existente en la suscripción de Azure. Sin embargo, puede elegir crear un nuevo grupo de recursos al que desea agregar la cuenta. Para más información, consulte [Uso del Portal de Azure para administrar los recursos de Azure](resource-group-portal.md).

	- Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará la cuenta.
   
	- Seleccione **Anclar al panel**

7.	Una vez que las nuevas opciones de la cuenta estén configuradas, haga clic en **Crear**. La cuenta puede tardar unos minutos en crearse. Para comprobar el estado, puede supervisar el progreso en el Panel de inicio. ![Captura de pantalla del icono de creación en el Panel de inicio - creador de bases de datos en línea](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)

	O bien, puede supervisar su progreso en el centro de notificaciones.

	![Creación rápida de bases de datos - Captura de pantalla del Centro de notificaciones, donde se indica que se está creando la cuenta de DocumentDB](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)

	![Captura de pantalla del Centro de notificaciones, que indica que se creó correctamente la cuenta de DocumentDB y se implementó en un grupo de recursos - Notificación del creador de bases de datos en línea](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.	Después de crear la cuenta, ya se puede usar con la configuración predeterminada.

	![Captura de pantalla de la hoja de cuenta predeterminada](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
	

## Pasos siguientes


- Obtenga información acerca de cómo [conectar](documentdb-connect-mongodb-account.md) una cuenta de DocumentDB con soporte de protocolo para MongoDB.

 

<!---HONumber=AcomDC_0601_2016-->