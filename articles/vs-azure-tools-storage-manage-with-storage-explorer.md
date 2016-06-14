<properties
	pageTitle="Introducción al Explorador de almacenamiento (versión preliminar) | Microsoft Azure"
	description="Administración de recursos de almacenamiento de Azure con el Explorador de almacenamiento (versión preliminar)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# Introducción al Explorador de almacenamiento (versión preliminar)

## Información general 

La versión preliminar del Explorador de almacenamiento de Microsoft Azure es una aplicación independiente que permite trabajar fácilmente con los datos de Almacenamiento de Azure en Windows, OSX y Linux. En este artículo aprenderá las diferentes maneras de conectarse a sus cuentas de Almacenamiento de Azure y de administrarlas.

## Requisitos previos

- [Descargue e instale el Explorador de almacenamiento (versión preliminar)](http://go.microsoft.com/fwlink/?LinkId=708343)

## Conexión a una cuenta de almacenamiento o servicio

El Explorador de almacenamiento (versión preliminar) proporciona innumerables formas de conectar con las cuentas de almacenamiento. Esto incluye la conexión a cuentas de almacenamiento asociadas a las suscripciones de Azure, la conexión a cuentas de almacenamiento y servicios compartidas desde otras suscripciones de Azure, e incluso la conexión y administración del almacenamiento local mediante el emulador de almacenamiento de Azure:

- [Conexión a una suscripción de Azure](#connect-to-an-azure-subscription): administre recursos de almacenamiento que pertenecen a su suscripción de Azure.
- [Conexión al almacenamiento local](#connect-to-local-storage): administre el almacenamiento local mediante el emulador de almacenamiento de Azure. 
- [Asociación y desasociación de una cuenta de almacenamiento externo](#attach-or-detach-an-external-storage-account): administre los recursos de almacenamiento que pertenezcan a otra suscripción de Azure utilizando el nombre de cuenta de la cuenta de almacenamiento y la clave.
- [Asociación de cuentas mediante SAS](#attach-account-using-sas): administre los recursos de almacenamiento que pertenezcan a otra suscripción de Azure usando una SAS.
- [Asociación de servicios mediante SAS](#attach-service-using-sas): administre un servicio de almacenamiento específico (contenedor de blob, cola o tabla) que pertenezca a otra suscripción de Azure usando una SAS.

## Conexión a una suscripción de Azure

> [AZURE.NOTE] Si aún no tiene ninguna cuenta de Azure, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Inicie el Explorador de almacenamiento (versión preliminar). 

1. Si ejecuta el Explorador de almacenamiento (versión preliminar) por primera vez o si lo ha ejecutado antes pero sin conectarse a una cuenta de Azure, verá una barra de información que le permite conectarse a una cuenta de Azure.

	![][0]
	
1. Seleccione **Conectar con Microsoft Azure** y siga los cuadros de diálogo para iniciar sesión con una cuenta de Microsoft que esté asociada con al menos una suscripción activa de Azure.

Una vez que haya iniciado sesión correctamente en una cuenta de Microsoft, el panel izquierdo del Explorador de almacenamiento (versión preliminar) se rellenará con todas las cuentas de almacenamiento asociadas a todas las suscripciones de Azure que están a su vez asociadas con la cuenta de Microsoft.

### Filtrado de las suscripciones de Azure

El Explorador de almacenamiento (versión preliminar) le permite filtrar cuáles de las suscripciones de Azure asociadas a las cuentas de Microsoft con las que ha iniciado sesión tendrán sus cuentas de almacenamiento enumeradas en el panel izquierdo.

1. Seleccione el icono de **configuración** (engranaje).

	![][1]

1. 	En la parte superior del panel izquierdo, verá una lista desplegable que contiene todas las cuentas de Microsoft en las que ha iniciado sesión.

	![][3]
	 
1.	Seleccione la flecha abajo junto a la lista desplegable para ver todas las cuentas de Microsoft en las que ya tiene una sesión abierta, así como un vínculo para agregar (iniciar sesión) en otras cuentas adicionales de Microsoft.

	![][4]

1.	En la lista desplegable, seleccione la cuenta de Microsoft deseada.

1. 	El panel izquierdo mostrará todas las suscripciones de Azure asociadas a la cuenta de Microsoft seleccionada. La casilla a la izquierda de cada suscripción de Azure le permite especificar si desea o no que el Explorador de almacenamiento (versión preliminar) enumere todas las cuentas de almacenamiento asociadas con dicha suscripción de Azure. La activación o desactivación de **All subscriptions** (Todas las suscripciones) alterna entre la selección de todas o de ninguna de las suscripciones de Azure que aparecen enumeradas.

	![][2]

1.	Cuando haya terminado de seleccionar las suscripciones de Azure que desea administrar, pulse **Aplicar**. Se actualizará el panel izquierdo con una lista de todas las cuentas de almacenamiento de cada suscripción de Azure seleccionada para la cuenta actual de Microsoft.

### Incorporación de cuentas de Microsoft adicionales

Los siguientes pasos le guiarán a través de la conexión con cuentas de Microsoft adicionales para ver las suscripciones de Azure y las cuentas de almacenamiento de cada cuenta.

1.	Seleccione el icono de **configuración** (engranaje).

	![][1]

1. 	En la parte superior del panel izquierdo, verá una lista desplegable que contiene todas las cuentas de Microsoft conectadas en ese momento.

	![][3]
	 
1.	Seleccione la flecha abajo junto a la lista desplegable para ver todas las cuentas de Microsoft en las que ya tiene una sesión abierta, así como un vínculo para agregar (iniciar sesión) en otras cuentas adicionales de Microsoft.

	![][4]

1.	Seleccione **Agregar una cuenta** y siga los cuadros de diálogo para iniciar sesión en una cuenta asociada con al menos una suscripción activa de Azure.

1.	Seleccione las casillas de las suscripciones de Azure que desea examinar.

	![][2]

1.	Seleccione **Aplicar**.

### Cambio entre cuentas de Microsoft

Aunque puede conectarse a varias cuentas de Microsoft, el panel izquierdo muestra solo las cuentas de almacenamiento asociadas con las suscripciones de una sola cuenta de Microsoft (la actual). Si se conecta a varias cuentas de Microsoft, puede cambiar entre ellas realizando los pasos siguientes:

1.	Seleccione el icono de **configuración** (engranaje).

	![][1]

1. 	En la parte superior del panel izquierdo, verá una lista desplegable que contiene todas las cuentas de Microsoft conectadas en ese momento.

	![][3]
	 
1.	Seleccione la flecha abajo junto a la lista desplegable para ver todas las cuentas de Microsoft en las que ya tiene una sesión abierta, así como un vínculo para agregar (iniciar sesión) en otras cuentas adicionales de Microsoft.

	![][4]

1.	Seleccione la cuenta de Microsoft deseada.

1.	Seleccione las casillas de las suscripciones de Azure que desea examinar.

	![][2]

1.	Seleccione **Aplicar**.
  
## Conexión al almacenamiento local

El Explorador de almacenamiento (versión preliminar) le permite trabajar con almacenamiento local mediante el emulador de almacenamiento de Azure. Esto le permite escribir código y realizar pruebas de almacenamiento sin necesidad de tener una cuenta de almacenamiento implementada en Azure (ya que el emulador de almacenamiento de Azure emula dicha cuenta).

>[AZURE.NOTE] El emulador de almacenamiento de Azure actualmente solo se admite para Windows.

1. Inicie el Explorador de almacenamiento (versión preliminar). 

1. En el panel izquierdo, amplíe el nodo **(Desarrollo)**.

	![][21]

1. Si aún no ha instalado el emulador de almacenamiento de Azure, se solicitará que lo haga a través de una barra de información. Si se muestra la barra de información, seleccione **Descargar la última versión**, e instale el emulador.

	![][22]

1. Una vez instalado el emulador,podrá crear y trabajar con tablas, colas y blobs locales. Para informarse sobre cómo trabajar con cada tipo de cuenta de almacenamiento, seleccione a continuación el vínculo apropiado:

	- [Administración de recursos de Almacenamiento de blobs de Azure](./vs-azure-tools-storage-explorer-blobs.md)
	- Administración de recursos de Almacenamiento en cola de Azure; *próximamente*.
	- Administración de recursos de Almacenamiento de tablas de Azure; *próximamente*.

## Asociación y desasociación de una cuenta de almacenamiento externo

El Explorador de almacenamiento (versión preliminar) permite asociar cuentas de almacenamiento externo, lo que proporciona la capacidad de compartir fácilmente las cuentas de almacenamiento. En esta sección se explica cómo asociar (y desasociar) cuentas de almacenamiento externo.

### Obtención de las credenciales de la cuenta de almacenamiento

Para compartir una cuenta de almacenamiento externo, el propietario de la misma tiene que obtener primero las credenciales (nombre de cuenta y clave) de la cuenta, y compartir esa información con la persona que desea asociar a esa cuenta (externa). La obtención de las credenciales de cuenta de almacenamiento puede realizarse a través del Portal de Azure siguiendo estos pasos:

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com).
1.	Seleccione **Examinar**.
1.	Seleccione **Cuentas de almacenamiento**.
1.	En la hoja **Cuentas de almacenamiento**, seleccione la cuenta de almacenamiento deseada.
1.	En el hoja **Configuración** de la cuenta de almacenamiento elegida, seleccione **Claves de acceso**.

	![][5]
	
1.	En la hoja **Claves de acceso**, copie los valores **NOMBRE DE LA CUENTA DE ALMACENAMIENTO** y **CLAVE 1** valores para su uso en la conexión con la cuenta de almacenamiento.

	![][6]

### Asociación de una cuenta de almacenamiento externo

1.	En el Explorador de almacenamiento (versión preliminar), haga clic en **Cuentas de almacenamiento**, y en el menú contextual seleccione **Asociar almacenamiento externo**.

	![][7]
	
1.	La sección *Obtención de las credenciales de la cuenta de almacenamiento*, explica cómo obtener los valores de nombre y clave 1 de la cuenta de almacenamiento. Estos valores se usarán en este paso. En el cuadro de diálogo **Asociar almacenamiento externo**, escriba el nombre de la cuenta de almacenamiento en el cuadro **Nombre de la cuenta** y el valor Clave 1 en el cuadro **Clave de cuenta**. Pulse **Aceptar** cuando haya terminado.

	![][8]

	Una vez se haya realizado la asociación, se mostrará la cuenta de almacenamiento externo con el texto **(Externa)** junto al nombre de la cuenta.

	![][9]

### Desasociación de una cuenta de almacenamiento externo

1. 	Haga clic con el botón derecho en la cuenta de almacenamiento externo que desea desasociar y en el menú contextual seleccione **Desconectar**.

	![][10]

1.	Cuando aparezca el cuadro de mensaje de confirmación, seleccione **Sí** para confirmar la desasociación de la cuenta de almacenamiento externo.

	![][12]

## Asociación de cuentas mediante SAS

Una SAS (firma de acceso compartido) proporciona al administrador de una suscripción de Azure la capacidad de conceder acceso a una cuenta de almacenamiento de forma temporal, sin tener que proporcionar sus credenciales de suscripción de Azure.

Para ilustrar este punto, supongamos que el Usuario A es el administrador de una suscripción de Azure que desea permitir al Usuario B el acceso a una cuenta de almacenamiento durante un tiempo limitado con determinados permisos:

1. El Usuario A genera una SAS (que consiste en una cadena de conexión para la cuenta de almacenamiento) durante un período de tiempo específico y con los permisos deseados.
1. El Usuario A comparte la SAS con la persona que desea tener acceso a la cuenta de almacenamiento (en nuestro ejemplo el Usuario B).  
1. El Usuario B utiliza el Explorador de almacenamiento (versión preliminar) para asociarse a la cuenta de la que es propietario el Usuario A, usando la SAS proporcionada. 

### Obtención de una SAS para la cuenta que se desea compartir

1.	Abra el Explorador de almacenamiento (versión preliminar).
1.	En el panel izquierdo, haga clic con el botón derecho en la cuenta de almacenamiento que desea compartir y en el menú contextual seleccione **Get Shared Access Signature** (Obtener firma de acceso compartido).

	![][13]

1. En el cuadro de diálogo **Firma de acceso compartido**, especifique el período de tiempo y los permisos que desee para la cuenta y seleccione **Crear**.

	![][14]
 
1. Aparecerá un segundo cuadro de diálogo **Firma de acceso compartido** mostrando la SAS. Seleccione **Copiar** junto a la **Cadena de conexión** para copiarla en el Portapapeles. Seleccione **Cerrar** para descartar el cuadro de diálogo.

### Asociación a la cuenta compartida con SAS

1.	Abra el Explorador de almacenamiento (versión preliminar).
1.	En el panel izquierdo, haga clic con el botón derecho en **Cuentas de almacenamiento**, y en el menú contextual seleccione **Attach account using SAS** (Asociar cuenta usando SAS). ![][15]

1. En el diálogo** Attach Account using SAS** (Asociar cuenta usando SAS):

	- **Nombre de la cuenta**: escriba el nombre que desea asociar con esta cuenta. **NOTA:** El nombre de cuenta no tiene que coincidir con el nombre de la cuenta de almacenamiento original para la que se generó la SAS. 
 	- **Cadena de conexión**: pegue la cadena de conexión que copió anteriormente.
 	- Pulse **Aceptar** cuando haya terminado.
	
	![][16]

Una vez se haya realizado la asociación, se mostrará la cuenta de almacenamiento externo con el texto (SAS) junto al nombre de cuenta que ha proporcionado.

![][17]

## Asociación de servicios mediante SAS

La sección [Asociación de cuentas mediante SAS](#attach-account-using-sas) muestra cómo un administrador de suscripción de Azure puede conceder acceso temporal a una cuenta de almacenamiento mediante la generación (y uso compartido) de una SAS para la cuenta de almacenamiento. De forma similar, se puede generar una SAS para un servicio específico (contenedor de blob, cola o tabla) dentro de una cuenta de almacenamiento.

### Generación de una SAS para el servicio que se desea compartir

En este contexto, un servicio puede ser un contenedor de blob, una cola o una tabla. Las siguientes secciones explican cómo generar la SAS para el servicio de lista:

- [Get the SAS for a blob container](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) (Obtención de la SAS para un contenedor de blob)
- Obtención de la SAS para una cola; *próximamente*.
- Obtención de la SAS para una tabla; *próximamente*.

### Asociación al servicio de cuenta compartida con SAS

1.	Abra el Explorador de almacenamiento (versión preliminar).
1.	En el panel izquierdo, haga clic con el botón derecho en **Cuentas de almacenamiento**, y en el menú contextual seleccione **Attach service using SAS** (Asociar servicio usando SAS). ![][18]

1. En el cuadro de diálogo **Attach service using SAS** (Asociar servicio usando SAS) pegue el URI de SAS que copió anteriormente y seleccione **Aceptar**.

	![][19]

Una vez asociado, el servicio aparecerá en el nodo **(Sevice SAS)** (SAS de servicio).

![][20]

## Búsqueda de cuentas de almacenamiento

Si tiene una larga lista de cuentas de almacenamiento, puede usar el cuadro de búsqueda en la parte superior del panel izquierdo para encontrar rápidamente una cuenta de almacenamiento concreta.

A medida que vaya escribiendo en el cuadro de búsqueda, el panel izquierdo mostrará solo las cuentas de almacenamiento que coincidan con el valor de búsqueda que haya escrito hasta ese momento. La captura de pantalla a continuación muestra un ejemplo en el que hemos buscado todas las cuentas de almacenamiento en las que el nombre de la cuenta de almacenamiento contiene el texto "tarcher".

![][11]
	
Para borrar la búsqueda, seleccione el botón **x** en el cuadro de búsqueda.

## Pasos siguientes
- [Administración de recursos de Almacenamiento de blobs de Azure con el Explorador de almacenamiento (versión preliminar)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0608_2016-->