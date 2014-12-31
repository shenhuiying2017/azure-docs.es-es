<properties urlDisplayName="Azure Import/Export Service" pageTitle="Uso de la importación y exportación para transferir datos al almacenamiento en blobs | Microsoft Azure" metaKeywords="" description="Learn how to create import and export jobs in the Azure Management Portal to transfer data to blob storage." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/25/2014" ms.author="tamram" />


# Uso del servicio de importación y exportación de Microsoft Azure para transferir datos al almacenamiento en blobs

Puede utilizar el servicio de importación y exportación de Microsoft Azure para transferir grandes cantidades de datos de archivo al almacenamiento en blobs de Azure en situaciones en las que el proceso de carga a la red es prohibitivamente caro o no es viable. También puede utilizar el servicio de importación y exportación para transferir grandes cantidades de datos almacenados en blobs a su instalación local de una forma rápida y rentable.

Para transferir un gran conjunto de datos de archivo al almacenamiento en blobs, puede enviar uno o varios discos duros que contengan los datos a un centro de datos de Azure, en el que se cargarán sus datos a la cuenta de almacenamiento. De forma similar, para exportar datos desde el almacenamiento en blobs, puede enviar discos duros vacíos a un centro de datos de Azure, en el que los datos almacenados en blobs de su cuenta de almacenamiento se copiarán en sus discos duros (que, a continuación, se le devolverán). Antes de enviar una unidad que contiene datos, deberá cifrar los datos de la unidad; cuando el servicio de importación y exportación exporte sus datos para enviárselos, los datos se cifrarán igualmente antes del envío.

Puede crear y administrar los trabajos de importación y exportación de las dos siguientes maneras:

- Mediante el Portal de administración de Azure.
- Mediante la interfaz REST del servicio.

Este artículo proporciona un resumen del servicio de importación y exportación y describe la forma en que se debe utilizar el Portal de administración para hacer uso del servicio de importación/exportación. Para obtener más información acerca de la API de REST, consulte [Referencia de la API de REST del servicio de importación y exportación](http://go.microsoft.com/fwlink/?LinkID=329099).

## Descripción general del servicio de importación y exportación ##

Para comenzar el proceso de importación o exportación desde el almacenamiento en blobs, cree primero un *trabajo*. Puede tratarse de un *trabajo de importación* o un *trabajo de exportación*:

- Cree un trabajo de importación si desea transferir datos que tiene en su instalación local a blobs en su cuenta de almacenamiento de Azure.
- Cree un trabajo de exportación si desea transferir datos que actualmente están almacenados en blobs de su cuenta de almacenamiento a discos duros que se le enviarán posteriormente.

Cuando crea un trabajo, notifica al servicio de importación y exportación que enviará uno o varios discos duros a un centro de datos de Azure. Para un trabajo de importación, deberá enviar discos duros que contengan datos de archivo. Para un trabajo de exportación, deberá enviar discos duros vacíos.

Para preparar la unidad que va a enviar para realizar un trabajo de importación, deberá ejecutar la **herramienta de importación y exportación de Microsoft Azure**, que facilita la copia de sus datos a la unidad mediante el cifrado de dichos datos en la unidad con BitLocker y genera los archivos de diario de la unidad, lo cual se describe a continuación.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Los datos de la unidad deben estar cifrados con BitLocker Drive Encryption. Así estarán protegidos mientras se encuentren en tránsito. Para un trabajo de exportación, el servicio de importación y exportación cifrará sus datos antes de efectuar el envío de vuelta de la unidad.</p>
</div>

Cuando cree un trabajo de importación o de exportación, deberá tener también el *identificador de la unidad*, que es el número de serie que asigna el fabricante de la unidad a un disco duro específico. El identificador de la unidad se muestra en el exterior de la misma. 

<h3>Requisitos y alcance</h3>

1.	**Cuentas de suscripción y almacenamiento:** Debe disponer de una suscripción de Azure existente y una o varias cuentas de almacenamiento para utilizar el servicio de importación y exportación. Puede utilizar cada trabajo para transferir datos desde o hacia una sola cuenta de almacenamiento. Dicho de otra forma, un trabajo no puede expandirse por varias cuentas de almacenamiento. Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage-create-storage-account/).
2.	**Discos duros:** El servicio de importación y exportación solo admite unidades de disco duro SATA II/III de 3,5 pulgadas. No se admiten unidades de disco duro con un tamaño superior a 4 TB. Para los trabajos de importación, solo se procesará el primer volumen de datos de la unidad. El volumen de datos debe tener formato NTFS. Es posible conectar un disco SATA II/III externo a la mayoría de los equipos con un adaptador USB SATA II/III.
3.	**Cifrado BitLocker:** Todos los datos almacenados en discos duros deben estar cifrados mediante BitLocker con claves de cifrado protegidas mediante contraseñas numéricas.
4.	**Objetivos de almacenamiento en blobs:** Los datos se pueden cargar a blobs en bloques y blobs en páginas o descargarlos de estos. 
5.	**Número de trabajos:** Un cliente puede tener hasta 20 trabajos activos por cuenta de almacenamiento.
6.	**Tamaño máximo de un trabajo:** El tamaño de un trabajo se determina mediante la capacidad de los discos duros utilizados y la cantidad máxima de datos que se pueden almacenar en una cuenta de almacenamiento. Cada trabajo puede incluir hasta 10 discos duros.

## Creación de un trabajo de importación en el Portal de administración##

Cree un trabajo de importación para notificar al servicio de importación y exportación que va a enviar al centro de datos una o varias unidades que contienen datos que desea importar a su cuenta de almacenamiento.

<h3>Preparación de las unidades</h3>

Antes de crear un trabajo de importación, prepare sus unidades con la herramienta de importación y exportación de Microsoft Azure. Para obtener más información acerca del uso de la herramienta de importación y exportación de Microsoft Azure, consulte [Referencia de la herramienta de importación y exportación de Azure](http://go.microsoft.com/fwlink/?LinkId=329032). Puede descargar la [la herramienta de importación y exportación de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) como un paquete independiente.
  
Para preparar sus unidades, siga los tres pasos siguientes: 

1.	Determine los datos que desea importar y el número de unidades que necesitará.
2.	Identifique los blobs de destino de sus datos en el servicio BLOB de Azure.
3.	Utilice la herramienta de importación y exportación de Microsoft Azure para copiar sus datos a uno o varios discos duros.

La herramienta de importación y exportación de Microsoft Azure genera un archivo de *diario de unidad* para cada unidad que se haya preparado. El archivo de diario de la unidad se almacena en su equipo local, no en la unidad en sí. Cargará el archivo de diario cuando haya creado el trabajo de importación. Un archivo de diario de unidad incluye el identificador de la unidad y la clave de BitLocker, así como otras informaciones acerca de la unidad.  

<h3>Creación del trabajo de importación</h3>

1.	Una vez preparada la unidad, diríjase a su cuenta de almacenamiento en el Portal de administración y visualice 	el Panel. En <strong>Vista rápida</strong>, haga clic en <strong>Crear un trabajo de importación.</strong>. 
 
2.	En el paso 1 del asistente, indique que ha preparado su unidad y que tiene el archivo de diario de la unidad 	disponible.
 
3.	En el paso 2, proporcione la información de contacto de la persona responsable de este trabajo de importación. Si desea guardar 	datos de registro detallados para el trabajo de importación, marque la opción para <strong>guardar el registro detallado en mi contenedor de blobs 	"waimportexport"</strong>.

4.	En el paso 3, cargue los archivos de diario de unidad que haya obtenido durante el paso de preparación de la unidad. Tendrá que 	cargar un archivo por cada unidad que haya preparado.

	![Create import job - Step 3][import-job-03]

5.	En el paso 4, escriba un nombre descriptivo para el trabajo de importación. Tenga en cuenta que el nombre que escriba solo puede contener 	letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios. 	El nombre elegido le servirá para realizar un seguimiento de sus trabajos mientras que estén en progreso y una vez se hayan completado.

	A continuación, seleccione en la lista la región de su centro de datos. La región del centro de datos indicará el centro de datos y la dirección donde debe enviar el paquete. Para obtener más información, consulte las P+F que se muestran a continuación.

6. 	En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación.

	Si tiene un número de seguimiento, seleccione en la lista el transportista para la entrega y, a continuación, escriba el número de seguimiento. 

	Si todavía no tiene un número de seguimiento, elija **Facilitaré mi información de envío para este trabajo de importación cuando haya enviado mi paquete** y, a continuación, finalice el proceso de importación.

7. Para escribir el número de seguimiento después de haber enviado el paquete, vuelva a la página **Importación/Exportación** de su cuenta de almacenamiento en el Portal de administración, seleccione el trabajo en la lista y elija **Información de envío**. Navegue por el asistente y escriba el número de seguimiento en el paso 2.

	Si el estado se encuentra en estado de creación, envío o transferencia, también puede actualizar el número de cuenta del transportista en el paso 2 del asistente. Una vez que el trabajo se encuentre en estado de empaquetado, no podrá actualizar el número de cuenta del transportista de ese trabajo. 

## Creación de un trabajo de exportación en el Portal de administración##

Cree un trabajo de exportación para notificar al servicio de importación y exportación que va a enviar al centro de datos una o varias unidades vacías, para que se exporten los datos de su cuenta de almacenamiento a las unidades y recibirlas posteriormente.

1. 	Para crear un trabajo de exportación, diríjase a su cuenta de almacenamiento en el Portal de administración y visualice el Panel. 	En <strong>Vista rápida</strong>, haga clic en <strong>Crear un trabajo de exportación</strong>y siga las indicaciones del 	asistente.

2. 	En el paso 2, proporcione la información de contacto de la persona responsable de este trabajo de exportación. Si desea guardar 	datos de registro detallados para el trabajo de exportación, marque la opción para <strong>guardar el registro detallado en mi contenedor de blobs 	"waimportexport"</strong>.

3.	En el paso 3, especifique los datos de blobs que desea exportar desde su cuenta de almacenamiento a una o varias unidades 	vacías. Puede elegir exportar todos los datos de blobs de la cuenta de almacenamiento o especificar los blobs 	o 	conjuntos de blobs que se van a exportar.

	![Create export job - Step 3][export-job-03]

	- Para especificar un blob que desea exportar, utilice el selector **Equal To** y especifique la ruta relativa al blob, empezando por el nombre del contenedor. Utilice *$root* para especificar el contenedor raíz.
	- Para especificar todos los blobs que empiecen por un prefijo, utilice el selector **Starts With** y especifique el prefijo, empezando por una barra diagonal "/". El prefijo puede ser el prefijo del nombre del contenedor, el nombre del contenedor completo o el nombre del contendor completo seguido del prefijo del nombre del blob.

	La tabla muestra ejemplos de rutas de blob válidas:

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tbody>
			<tr>
				<td><strong>Selector</strong></td>
				<td><strong>Ruta del blob</strong></td>
				<td><strong>Descripción</strong></td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/</td>
				<td>Exporta todos los blobs de la cuenta de almacenamiento.</td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/$root/</td>
				<td>Exporta todos los blobs del contenedor raíz.</td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/book</td>
				<td>Exporta todos los blobs de cualquier contenedor que empiecen por el prefijo <strong>book</strong></td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/music/</td>
				<td>Exporta todos los blobs del contenedor <strong>music</strong></td>
			</tr>
			<tr>
				<td>Starts With</td>
				<td>/music/love</td>
				<td>Exporta todos los blobs del contenedor <strong>music</strong> que empiecen por el prefijo <strong>love</strong></td>
			</tr>
			<tr>
				<td>Equal To</td>
				<td>$root/logo.bmp</td>
				<td>Exporta los blobs <strong>logo.bmp</strong> en el contenedor raíz</td>
			</tr>
			<tr>
				<td>Equal To</td>
				<td>videos/story.mp4</td>
				<td>Exporta los blobs <strong>story.mp4</strong> en el contenedor <strong>videos</strong></td>
			</tr>
		</tbody>
	</table>


4.	En el paso 4, escriba un nombre descriptivo para el trabajo de exportación. El nombre que escriba solo puede contener 	letras minúsculas, números, guiones y caracteres de subrayado, debe empezar por una letra y no puede contener espacios.

	La región del centro de datos indicará el centro de datos al que debe enviar su paquete. Para obtener más información, consulte las P+F que se muestran a continuación.

5. 	En el paso 5, seleccione en la lista el transportista para la devolución y, a continuación, escriba el número de cuenta del transportista. Microsoft utilizará esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de exportación.

	Si tiene un número de seguimiento, seleccione en la lista el transportista para la entrega y, a continuación, escriba el número de seguimiento. 

	Si todavía no tiene un número de seguimiento, elija <strong>Facilitaré mi información de envío para este trabajo de exportación cuando haya enviado mi paquete</strong>y, a continuación, finalice el proceso de exportación.

6. Para escribir el número de seguimiento después de haber enviado el paquete, vuelva a la página **Importación/Exportación** de su cuenta de almacenamiento en el Portal de administración, seleccione el trabajo en la lista y elija **Información de envío**. Navegue por el asistente y escriba el número de seguimiento en el paso 2.

	Si el estado se encuentra en estado de creación, envío o transferencia, también puede actualizar el número de cuenta del transportista en el paso 2 del asistente. Una vez que el trabajo se encuentre en estado de empaquetado, no podrá actualizar el número de cuenta del transportista de ese trabajo. 


## Seguimiento del estado de los trabajos en el Portal de administración##

Puede realizar el seguimiento del estado de sus trabajos de importación y exportación desde el Portal de administración. Diríjase a su cuenta de almacenamiento en el Portal de administración y haga clic en la pestaña **Importación/Exportación**. En la página aparecerá una lista con sus trabajos. Puede filtrar la lista en función del estado del trabajo, el nombre del trabajo, el tipo de trabajo o el número de seguimiento.

La tabla describe lo que significa cada designación de estado del trabajo.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td><strong>Estado del trabajo</strong></td>
			<td><strong>Descripción</strong></td>
		</tr>
		<tr>
			<td>Creating</td>
			<td>El trabajo se ha creado pero todavía no se ha proporcionado la información de envío.</td>
		</tr>
		<tr>
			<td>Envío</td>
			<td>El trabajo se ha creado y ya se ha proporcionado la información de envío.</td>
		</tr>
		<tr>
			<td>Transferring</td>
			<td>Los datos se están transfiriendo desde su disco duro (para un trabajo de importación) o a su disco duro (para un trabajo de exportación).</td>
		</tr>
		<tr>
			<td>Packaging</td>
			<td>Se ha completado la transferencia de datos y se está preparando su disco duro para el envío de vuelta.</td>
		</tr>
		<tr>
			<td>Complete</td>
			<td>El disco duro ya se le ha enviado.</td>
		</tr>
	</tbody>
</table>

## Visualización de claves de BitLocker de un trabajo de exportación ##

Para los trabajos de exportación, puede visualizar y copiar las claves de BitLocker que ha generado este servicio para su unidad, de modo que pueda descifrar los datos exportados cuando haya recibido las unidades del centro de datos de Azure. Diríjase a su cuenta de almacenamiento en el Portal de administración y haga clic en la pestaña **Importación/Exportación**. Seleccione su trabajo de exportación de la lista y haga clic en el botón **Ver claves**. Las claves de BitLocker se ven como se muestra a continuación:

![View BitLocker keys for export job][export-job-bitlocker-keys]

## Preguntas más frecuentes ##

<h3>General</h3>

**¿Cuál es el precio del servicio de importación y exportación?**

- Consulte la [página de precios](http://go.microsoft.com/fwlink/?LinkId=329033) para obtener información al respecto.

**¿Cuánto dura el proceso de importación o exportación de datos?**

- Durará el tiempo necesario para enviar los discos y varias horas por cada TB de datos que se desee copiar.
 
**¿Qué tipos de interfaz son compatibles?**

- El servicio de importación y exportación admite unidades de disco duro (HDD) internas SATA II/III de 3,5 pulgadas. Puede utilizar los siguientes adaptadores de USB a SATA para transferir los datos de los dispositivos antes del envío:
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE 

> [WACOM.NOTE] Si su convertidor no aparece en la lista anterior, puede probar a ejecutar la herramienta de importación y exportación de Microsoft Azure para preparar la unidad y comprobar si funciona antes de adquirir un convertidor compatible.

**Si deseo importar o exportar más de 10 unidades, ¿qué debo hacer?**

- Un trabajo de importación o exportación solo puede hacer referencia a 10 unidades en un único trabajo para el servicio de Importación/Exportación. Si desea enviar más de 10 unidades, puede crear varios trabajos.

**¿Qué sucede si envío por error un disco duro que no cumple con los requisitos de compatibilidad?**

- El centro de datos de Azure le devolverá la unidad que no cumple con los requisitos de compatibilidad. Si solo algunos de los dispositivos del paquete cumplen con los requisitos de compatibilidad, esas unidades se procesarán, y las unidades que no cumplan los requisitos se le devolverán.

<h3>Administración de trabajos de importación y exportación</h3>

**¿Qué sucede con mis trabajos de importación y exportación si elimino mi cuenta de almacenamiento de Azure?**

- Si elimina su cuenta de almacenamiento, todos los trabajos de importación y exportación de Azure se eliminarán junto con su cuenta.  

**¿Puedo cancelar un trabajo?**

- Puede cancelar un trabajo si el estado es Creating o Shipping.

**¿Durante cuánto tiempo puedo visualizar el estado de los trabajos completados en el Portal de administración?**

- Puede ver el estado de los trabajos completados durante 90 días. Todos los trabajos completados se eliminarán una vez transcurrido ese plazo.

**¿El cifrado de BitLocker es un requisito obligatorio?**

- Sí. Todas las unidades deben cifrarse con una clave de BitLocker.

**¿Aplica formatos en destino a las unidades antes de devolverlas?**

- No. Todas las unidades deben estar preparadas para BitLocker.

<h3>Envío</h3>

**¿Qué servicios de paquetería se pueden utilizar?**

- Para las regiones en EE. UU. y Europa, solo se admite [Federal Express](http://www.fedex.com/us/oadr/) (FedEx). Todos los paquetes se devolverán a través de FedEx Ground o FedEx International Economy.

- Para las regiones de Asia, solo se admite [DHL](http://www.dhl-welcome.com/Tutorial/) . Todos los paquetes se devolverán a través de DHL Express Worldwide.

	<div class="dev-callout">
	<strong>Important</strong>
	<p>You must provide your tracking number to the Azure Import/Export service; otherwise your job cannot be processed.</p>
	</div>

**¿Existe algún coste asociado al envío de devolución?**

- Microsoft utilizará el número de cuenta del transportista que facilite en el momento de creación del trabajo para enviar las unidades a la dirección de devolución desde el centro de datos. Asegúrese de proporcionar un número de cuenta del transportista para el transportista admitido en la región del centro de datos. Puede crear una cuenta de paquetería para [FedEx](http://www.fedex.com/us/oadr/) (en EE. UU. y Europa) o [DHL](http://www.dhl-welcome.com/Tutorial/) (Asia) si aún no tiene ninguna.

- Los gastos de la devolución se cargan en su cuenta de transportista, y dependen de este último.

**¿Desde y hasta dónde puedo enviar mis datos?**

- El servicio Importación/Exportación admite la importación a cuentas de almacenamiento y la exportación desde las mismas en las regiones siguientes:
	- Este de EE. UU. 
	- Oeste de EE. UU. 
	- Centro-Norte de EE. UU 
	- Centro-Sur de EE. UU 
	- Europa del Norte
	- Europa occidental
	- Asia oriental
	- Sudeste asiático

- Se le proporcionará una dirección de envío del lugar de residencia de su cuenta de almacenamiento. Por ejemplo, si vive en EE. UU. y la cuenta de almacenamiento se encuentra en el centro de datos de Europa occidental, se le proporcionará una dirección de envío en Europa para enviar las unidades.

	<div class="dev-callout">
	<strong>Importante</strong>
	<p>Tenga en cuenta que es posible que los medios físicos que está enviando deban cruzar alguna frontera internacional. Usted es el responsable de asegurar que los medios y datos físicos se importan o exportan de acuerdo con todas las normativas aplicables. Antes de enviar los medios físicos, pida asesoramiento para comprobar que los medios y datos se pueden enviar legalmente al centro de datos identificado. De este modo, se asegurará de que llegan a Microsoft de manera puntual.</p>
	</div>

- Al enviar los paquetes, debe seguir los términos establecidos en los [Términos de servicio de Microsoft Azure](http://azure.microsoft.com/en-us/support/legal/services-terms/). 

**¿Puedo adquirir unidades de Microsoft para los trabajos de importación y exportación?**

- 	No. Debe enviar sus propias unidades tanto para los trabajos de importación como para los de exportación.

**¿Qué debo incluir en el paquete?**

- Envíe solo las unidades de disco duro. No incluya elementos como cables de alimentación o cables USB.



[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png

<!--HONumber=35_1-->
