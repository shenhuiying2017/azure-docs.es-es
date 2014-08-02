<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="" solutions="" writer="mandia" manager="paulettm" editor="cgronlun" />

Servicios de BizTalk: Copia de seguridad y restauración
=======================================================

Los servicios de BizTalk de Azure incluyen las capacidades de copia de seguridad y restauración. Al crear una copia de seguridad, se realiza una instantánea de la configuración de los servicios de BizTalk de Azure.

Considere los siguientes escenarios:

-   Puede realizar la copia de seguridad de la configuración del servicio de BizTalk de dos formas mediante el Portal de administración de Azure: crear copias de seguridad a medida que se necesiten (copias de seguridad ad hoc) y realizar copias de seguridad programadas.

-   El contenido de las copias de seguridad se puede restaurar en el mismo servicio de BizTalk o en uno nuevo. Para restaurar el servicio de BizTalk con el mismo nombre, es preciso eliminar el servicio de BizTalk existente. De lo contrario, se producirá un error en la restauración.

-   Se pueden restaurar los servicios de BizTalk en la misma edición o en una posterior. No se puede realizar la restauración del servicio de BizTalk en una edición anterior con respecto a la usada para realizar la copia de seguridad.

    Por ejemplo, una copia de seguridad que usa la Basic Edition se puede restaurar a la Premium Edition. Sin embargo, una copia de seguridad que usa la Premium Edition no se puede restaurar a la Standard Edition.

-   A fin de mantener la continuidad de los números de control del intercambio electrónico de datos (EDI), estos se incluyen en una copia de seguridad. Si los mensajes se procesan después de la última copia de seguridad y se restaura el contenido de esta copia de seguridad, se pueden duplicar los números de control.

-   La copia de seguridad y la restauración no están disponibles como parte de la edición para programadores de los servicios de BizTalk.

En este tema se describe cómo realizar la copia de seguridad y la restauración de los servicios de BizTalk con el Portal de administración de Azure. También puede realizar la copia de seguridad de los servicios de BizTalk mediante las API de REST. Para obtener más información acerca al respecto, consulte [API de REST de los servicios de BizTalk](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx).

[¿Qué se incluye en la copia de seguridad?](#budata)

[Creación de una copia de seguridad](#createbu)

[Restauración](#restore)

¿Qué se incluye en la copia de seguridad?
-----------------------------------------

Se incluyen los elementos siguientes al crear una copia de seguridad:

<table data-morhtml="true" border="1"> 
<tr data-morhtml="true" bgcolor="FAF9F9">
<th data-morhtml="true"> </th>
<th data-morhtml="true">Elementos incluidos en la copia de seguridad</th> 
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true" colspan="2">
 <strong data-morhtml="true">Portal de los servicios de BizTalk de Azure</strong></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Configuraci&oacute;n y tiempo de ejecuci&oacute;n</td> 
<td data-morhtml="true"><bl data-morhtml="true">
<li data-morhtml="true">Detalles del asociado y perfil</li>
<li data-morhtml="true">Acuerdos de socios</li>
<li data-morhtml="true">Ensamblados personalizados implementados</li>
<li data-morhtml="true">Puentes implementados</li>
<li data-morhtml="true">Certificados</li>
<li data-morhtml="true">Transformaciones implementadas</li>
<li data-morhtml="true">Procesos</li>
<li data-morhtml="true">Plantillas creadas y guardadas en el portal de servicios de BizTalk</li>
<li data-morhtml="true">Asignaciones X12 ST01 y GS01</li>
<li data-morhtml="true">N&uacute;meros de control (EDI)</li>
<li data-morhtml="true">Valores MIC de mensaje AS2</li>
</bl></td>
</tr> 
 
<tr data-morhtml="true">
<td data-morhtml="true" colspan="2">
 <strong data-morhtml="true">Servicio de BizTalk de Azure</strong></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Certificado SSL</td> 
<td data-morhtml="true">
<bl data-morhtml="true">
<li data-morhtml="true">Datos del certificado SSL</li>
<li data-morhtml="true">Contrase&ntilde;a del certificado SSL</li>
</bl>
</td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Configuraci&oacute;n del servicio de BizTalk</td> 
<td data-morhtml="true">
<bl data-morhtml="true">
<li data-morhtml="true">Recuento de unidades de escalado</li>
<li data-morhtml="true">Edici&oacute;n</li>
<li data-morhtml="true">Versi&oacute;n del producto</li>
<li data-morhtml="true">Regi&oacute;n/Centro de datos</li>
<li data-morhtml="true">Clave y espacio de nombres del servicio de control de acceso (ACS)</li>
<li data-morhtml="true">Cadena de conexi&oacute;n de la base de datos de seguimiento</li>
<li data-morhtml="true">Cadena de conexi&oacute;n de la cuenta de almacenamiento de archivado</li>
<li data-morhtml="true">Cadena de conexi&oacute;n de la cuenta de almacenamiento de supervisi&oacute;n</li>
</bl></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true" colspan="2">
 <strong data-morhtml="true">Elementos adicionales</strong></td>
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">Base de datos de seguimiento</td> 
<td data-morhtml="true">Cuando se aprovisiona el servicio de BizTalk, se definen los detalles de la base de datos de seguimiento, entre los que se incluyen el servidor de Base de datos SQL de Azure y el nombre de la base de datos de seguimiento. No se realiza la copia de seguridad de la base de datos de seguimiento de forma autom&aacute;tica.<br data-morhtml="true" /><br data-morhtml="true" /> <strong data-morhtml="true">Importante</strong><br data-morhtml="true" /> Si la base de datos de seguimiento se elimina accidentalmente y hay que recuperarla, debe existir una copia de seguridad previa. Si no la hay, no se podr&aacute;n recuperar la base de datos de seguimiento ni sus datos. En esta situaci&oacute;n, cree una nueva base de datos de seguimiento con el mismo nombre. Adem&aacute;s, se recomienda la replicaci&oacute;n geogr&aacute;fica.</td>
</tr> 
</table>

Creación de una copia de seguridad
----------------------------------

Puede realizar una copia de seguridad en cualquier momento y controlarla por completo. Puede realizar copias de seguridad desde el Portal de administración de Azure o desde la API de REST del servicio de BizTalk. Para crear una copia de seguridad con la API de REST de los servicios de BizTalk, consulte [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)

En esta sección se proporcionan instrucciones acerca de cómo realizar copias de seguridad con el Portal de administración. Puede usar el Portal de administración para crear copias de seguridad ad hoc o programadas en función de intervalos deseados.

Antes de crear una copia de seguridad
-------------------------------------

Asegúrese de tener en cuenta las consideraciones siguientes antes de crear una copia de seguridad:

1.  Antes de ejecutar una copia de seguridad ad hoc, procese el lote de mensajes activos. Esto le ayudará a impedir que se pierdan los mensajes *si* se restaura esta copia de seguridad. Los mensajes en lotes nunca se almacenan al realizar una copia de seguridad. Con las copias de seguridad programadas, quizá no pueda garantizar la ausencia de mensajes en el lote cuando se inicie la copia de seguridad.

    **Nota:**

    Si se realiza una copia de seguridad con mensajes activos en un lote, estos mensajes no se incluyen en la copia de seguridad y, por lo tanto, se pierden.

2.  Opcional: en el portal de servicios de BizTalk, detenga todas las operaciones de administración.
3.  Cree la copia de seguridad en la cuenta de almacenamiento mediante los comandos de la [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584) disponibles con la API de REST.

[Copias de seguridad ad hoc](#backupnow)

[Programación de una copia de seguridad](#backupschedule)

### Copias de seguridad ad hoc

1. En el Portal de administración de Azure, haga clic en Servicios de BizTalk y, a continuación, en el nombre del servicio de BizTalk del que desee realizar la copia de seguridad.
2. En la pestaña **Panel** del servicio de BizTalk, haga clic en **Back up** en la parte inferior de la página.
3. En el cuadro de diálogo **Copia de seguridad de servicio de BizTalk**, proporcione el nombre de la copia de seguridad.
4. Seleccione una cuenta de almacenamiento de blobs y haga clic en la marca de verificación para iniciar la copia de seguridad.

Una vez que termine la copia de seguridad, se crea un contenedor con el nombre de la copia de seguridad que ha especificado debajo de la cuenta de almacenamiento. Este contenedor contiene la configuración de la copia de seguridad del servicio de BizTalk.

### Programación de una copia de seguridad

1.  En el Portal de administración de Azure, haga clic en Servicios de BizTalk, a continuación, en el nombre del servicio de BizTalk para el que desea programar las copias de seguridad automatizadas y, por último, en la pestaña **Configurar**.
2.  En **Estado de copia de seguridad**, seleccione **None** si no desea programar copias de seguridad automatizadas. Para programar copias de seguridad automatizadas, haga clic en **Automatic**.
3.  En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento de Azure donde se crearán las copias de seguridad.
4.  En **Frecuencia**, especifique la fecha y la hora de inicio de la primera copia de seguridad y el intervalo (en días) para realizar las copias de seguridad sucesivas.
5.  En **Días de retención**, especifique una ventana de tiempo (en días) en la que se retienen las copias de seguridad. El período de retención debe ser superior a la frecuencia de la copia de seguridad.
6.  Seleccione la casilla **Conserve siempre al menos una copia de seguridad** para asegurarse de que haya al menos una copia de seguridad disponible incluso aunque haya pasado el período de días de retención.
7.  Haga clic en **Save**.

Cuando se ejecute un trabajo de copia de seguridad programada, se crea un contenedor (para almacenar los datos de la copia de seguridad) en la cuenta de almacenamiento que haya especificado. El nombre del contenedor presenta el formato *BizTalk Service name-date-time*.

Si se produce un error en la copia de seguridad, la página del panel Servicio de BizTalk muestra el estado de la copia de seguridad como **Failed**.

![Estado de la última copia de seguridad programada](./media/biztalk-backup-restore/status-last-backup.png)

Puede hacer clic en el vínculo para ir a la página de registros de operaciones de los servicios de administración para obtener más información acerca del error. Para obtener más información acerca de los registros de operaciones con respecto a los servicios de BizTalk, consulte [Servicios de BizTalk: Solución de problemas con la utilización de los registros de operaciones](http://go.microsoft.com/fwlink/?LinkId=391211).

Restauración
------------

Puede restaurar las copias de seguridad desde el Portal de administración de Azure o desde la API de REST de los servicios de BizTalk. En esta sección se ofrecen instrucciones acerca de cómo realizar la restauración mediante el Portal de administración. Para realizar la restauración con la API de REST, consulte [Restauración del servicio de BizTalk a partir de una copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582).

### Antes de restaurar una copia de seguridad

Al restaurar una copia de seguridad, tenga en cuenta lo siguiente:

-   Se pueden especificar los nuevos almacenes de seguimiento, archivado y supervisión al restaurar un servicio de BizTalk.

-   Para restaurar el servicio de BizTalk que use el mismo nombre, elimine el servicio de BizTalk existente antes de iniciar la restauración. De lo contrario, se producirá un error en la restauración.

-   Se restauran los mismos datos del tiempo de ejecución de EDI. La copia de seguridad del tiempo de ejecución de EDI almacena los números de control. Los números de control se restauran en secuencias desde la hora de la copia de seguridad. Si los mensajes se procesan después de la última copia de seguridad y se restaura el contenido de esta copia de seguridad, se pueden duplicar los números de control.

#### Para restaurar una copia de seguridad

1.  En el Portal de administración de Azure, haga clic en **New**, vaya a **Servicios de aplicaciones**, **Servicio de BizTalk** y, a continuación, haga clic en **Restore**.

    ![Restauración de una copia de seguridad](./media/biztalk-backup-restore/restore-ui.png)

2.  En el asistente Nuevo servicio de BizTalk - Restauración, en la página **Restaurar servicio de BizTalk**, haga clic en el icono de la carpeta del cuadro de texto **URL de copia de seguridad** para abrir el cuadro de diálogo **Buscar en almacenamiento en la nube**. Las cuentas de almacenamiento de Azure aparecerán en el cuadro de diálogo.

    Expanda la cuenta de almacenamiento que ha especificado al crear o programar la copia de seguridad y, a continuación, seleccione el nombre del contenedor desde donde tiene que restaurar la configuración del servicio de BizTalk.

    En el panel derecho, seleccione el archivo .txt correspondiente a la copia de seguridad a partir de la cual va a realizar la restauración y, a continuación, haga clic en **Open**.

3.  Proporcione la siguiente información en la página **Restaurar servicio de BizTalk**:
 - Proporcione el nombre de un servicio de BizTalk. De forma predeterminada, se usa el nombre del servicio de BizTalk incluido en la copia de seguridad.
 - Compruebe la URL, la edición y la región del dominio del servicio de BizTalk restaurado.
 - Elija crear una nueva instancia de Base de datos SQL para la base de datos de seguimiento, y a continuación, haga clic en la flecha derecha.

4.  En la página **Especificar configuración de base de datos**, compruebe el nombre de la base de datos SQL y especifique el servidor físico donde se creará la base de datos SQL, el nombre de usuario y la contraseña de dicho servidor.

    Si desea realizar la configuración avanzada de la base de datos SQL, seleccione la casilla **Configuración avanzada de base de datos** y, a continuación, haga clic en la flecha derecha.

    Si no desea realizar la configuración avanzada de la base de datos, haga clic en la flecha derecha y, a continuación, continúe con el paso 6 más abajo.

5.  En la página **Configuración avanzada de base de datos**, seleccione la edición de la base de datos que desee usar (**Web** o **Business**), especifique el tamaño máximo de la base de datos y las reglas de intercalación. Haga clic en la flecha derecha.

6.  En la página **Especifique la configuración de supervisión y archivado** cree una nueva cuenta de almacenamiento o especifique una que ya exista, donde se almacenará la información de supervisión del servicio de BizTalk.

7.  Haga clic en la marca de verificación para iniciar el proceso de restauración&lt;/b\>.

8.  Cuando se haya realizado correctamente la restauración, se incluye un nuevo servicio de BizTalk cuyo estado está suspendido en la página de servicios de BizTalk del Portal de administración de Azure.

### Después de restaurar una copia de seguridad

El servicio de BizTalk siempre está restaurado en un estado **Suspended**. Esto le permite realizar cambios en la configuración de las aplicaciones del servicio de BizTalk, según proceda, antes de que el nuevo entorno empiece a funcionar. A continuación encontrará las consideraciones que debe tener en cuenta antes de iniciar el servicio de BizTalk recién restaurado:

-   Si ha creado aplicaciones del servicio de BizTalk mediante el SDK de los servicios de BizTalk de Azure, puede que tenga que actualizar las credenciales de ACS en dichas aplicaciones para que puedan funcionar con el entorno restaurado.

-   Puede restaurar un servicio de BizTalk para replicar un entorno de servicio de BizTalk que ya funciona. En tal caso, si tiene contratos configurados en el portal de servicios de BizTalk original que usa recursos compartidos de FTP como orígenes, puede que quiera actualizar los contratos del entorno recién restaurado para que use otros orígenes o recursos compartidos FTP. Si no lo hace, podría tener dos contratos diferentes intentando extraer el mismo mensaje.

-   Si ha usado la operación de restauración para tener varios entornos del servicio de BizTalk, asegúrese de elegir el entorno adecuado usando las aplicaciones de Visual Studio, los cmdlets de PowerShell, las API de REST o las API OM de administración de socios comerciales.

-   También es recomendable configurar copias de seguridad automatizadas en el entorno de servicio de BizTalk recién restaurado.

Una vez que haya tenido en cuenta estas u otras consideraciones, vaya a la página de servicio de BizTalk del Portal de administración de Azure, seleccione el servicio de BizTalk recién restaurado con estado suspendido y, a continuación, haga clic en **Resume** en la parte inferior de la página para iniciar el servicio.

Pasos siguientes
----------------

Para aprovisionar los Servicios de BizTalk de Azure en el Portal de administración de Azure, vaya a [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para comenzar a crear aplicaciones, vaya a [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

Otras referencias
-----------------

-   [Copia de seguridad del servicio de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [Restauración del servicio de BizTalk a partir de una copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [Servicios de BizTalk: Gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servicios de BizTalk: Aprovisionamiento con el Portal de administración de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servicios de BizTalk: Gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servicios de BizTalk: Pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servicios de BizTalk: Limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servicios de BizTalk: Nombre de emisor y clave de emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

