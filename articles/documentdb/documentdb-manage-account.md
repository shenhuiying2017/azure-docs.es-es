<properties 
	pageTitle="Administrar una cuenta de DocumentDB | Azure" 
	description="Obtenga información acerca de cómo administrar la cuenta de DocumentDB." 
	services="documentdb" 
	documentationCenter="" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

#Administración de una cuenta de DocumentDB

Obtenga información sobre cómo trabajar con claves, la configuración de coherencia y la configuración de capacidad, y conozca cómo eliminar una cuenta.

## <a id="keys"></a>Visualización, copia y regeneración de las claves de acceso
Cuando se crea una cuenta de DocumentDB, el servicio genera dos claves de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de DocumentDB. Al proporcionar dos claves de acceso, DocumentDB permite regenerar las claves sin interrupción en la cuenta de esta base de datos.

En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso al elemento **Claves** de la hoja de **cuenta de DocumentDB** para ver, copiar y regenerar las claves de acceso que se usan para obtener acceso a la cuenta de DocumentDB.

![](media/documentdb-manage-account/keys.png)

### Ver y copiar una clave de acceso

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.

2.      En el modo **Resumen**, haga clic en **Claves**.

3.      En la hoja **Claves**, haga clic en el botón **Copiar**, a la derecha de la clave que quiere copiar.

  ![](./media/documentdb-manage-account/image004.jpg)

### Regenerar las claves de acceso

Cambie las claves de acceso de la cuenta de DocumentDB de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de DocumentDB, de modo que puede usar una clave de acceso mientras regenera la otra.

> [AZURE.WARNING] La regeneración de las claves de acceso afecta a las aplicaciones que dependen de una clave actual. Todos los clientes que usan la clave de acceso para obtener acceso a la cuenta de DocumentDB deben estar actualizados para usar la nueva clave.

Si cuenta con aplicaciones o servicios en la nube que usan la cuenta de DocumentDB y regenera las claves, perderá las conexiones. Para evitarlo, sustituya las claves. Los siguientes pasos describen el proceso de distribución de las claves.

1.      Actualice la clave de acceso en el código de aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de DocumentDB.

2.      Vuelva a generar la clave de acceso primaria para su cuenta de DocumentDB. En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.

3.      En el modo Resumen, haga clic en **Claves**.

4.      En la hoja **Claves**, haga clic en el comando **Regenerar principal** y haga clic en **Aceptar** para confirmar que quiere generar una clave nueva.

5.      Tras comprobar que la clave nueva está disponible para su uso (aproximadamente 5 minutos después de la regeneración), actualice la clave de acceso en el código de aplicación para hacer referencia a la nueva clave de acceso principal.

6.      Vuelva a generar la clave de acceso secundaria.

*Tenga en cuenta que pueden pasar varios minutos antes de poder obtener acceso a la cuenta de DocumentDB con la clave que acaba de crear.*

## <a id="consistency"></a>Administración de la configuración de coherencia de DocumentDB
La configuración de la administración de la coherencia de DocumentDB admite cuatro niveles diferenciados de coherencia de datos que el usuario puede configurar para que los desarrolladores puedan equilibrar la coherencia, la disponibilidad y la latencia de forma predecible.

- Una coherencia **fuerte** garantiza que las operaciones de lectura siempre devolverán el último valor que se escribió.

- La coherencia de **uso vinculado** garantiza que las lecturas no quedarán obsoletas. Garantiza de forma específica que les lecturas serán, como máximo, de K versiones anteriores a la última versión que se escribió. 

- La coherencia de **sesión** garantiza lecturas monotónicas (nunca se leen datos antiguos, nuevos y antiguos en ese orden), operaciones de escritura monotónicas (las operaciones se ordenan) y que usted leerá los elementos escritos más recientes desde un único punto de vista del cliente.

- La coherencia **ocasional** garantiza que las operaciones de lectura siempre procesarán un subconjunto válido de las operaciones de escritura y que pueden converger de forma ocasional.

*Tenga en cuenta que, de forma predeterminada, se realiza el aprovisionamiento de las cuentas de DocumentDB con coherencia de nivel de sesión. Para obtener información adicional sobre la configuración de coherencia de DocumentDB, consulte la sección [Nivel de coherencia](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Para especificar la coherencia predeterminada de una cuenta de la DocumentDB:

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.

2.      En el modo **Configuración**, haga clic en **Coherencia predeterminada**.

3.      En la hoja **Coherencia predeterminada**, seleccione el nivel de coherencia predeterminado que quiere para la cuenta de DocumentDB.

![](./media/documentdb-manage-account/image005.png)

![](./media/documentdb-manage-account/image006.png)

4.       Haga clic en **Save**.

5.      Puede supervisar el progreso de la operación a través del Centro de notificaciones del Portal de vista previa de Azure.

*Tenga en cuenta que pueden pasar varios minutos antes de que los cambios en la configuración de coherencia predeterminada se hagan efectivos en la cuenta de DocumentDB.*

## <a id="capacity"></a>Administrar la configuración de capacidad de la DocumentDB
La configuración de la administración de la capacidad de DocumentDB de Microsoft Azure permite la escalación elástica a medida que cambia la demanda de la aplicación a lo largo de su ciclo de vida. Para realizar el escalado de DocumentDB, se aumenta la capacidad de la cuenta de esta base de datos a través del Portal de vista previa de Azure.

Cuando se crea una cuenta de base de datos, esta dispone de rendimiento reservado y almacenamiento de base de datos. Puede cambiar el rendimiento y el almacenamiento de base de datos disponible para la cuenta en cualquier momento. Para ello, agregue o quite unidades de capacidad en el Portal de vista previa de Azure.

### Para agregar o quitar unidades de capacidad:

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.

2.      En el modo **Uso**, haga clic en **Escala**.

3.      En la hoja **Escala**, especifique el número de unidades de capacidad para la cuenta de DocumentDB.


![](./media/documentdb-manage-account/image007.png)

4.      Haga clic en **Guardar** (tenga en cuenta que pueden pasar varios minutos antes de que la operación de escalado se complete. Puede supervisar su progreso a través del Centro de notificaciones del Portal de vista previa de Azure).

 *Tenga en cuenta que la vista previa de DocumentDB admite un máximo de 5 unidades de capacidad
por cada cuenta de esta base de datos.*


## <a id="delete"></a> Instrucciones acerca de cómo: Eliminación de cuentas de DocumentDB
Para quitar una cuenta de DocumentDB que ya no se usa, utilice el comando **Eliminar** en la hoja **Cuenta de DocumentDB**.

> [AZURE.WARNING] En la versión de vista previa, no hay ninguna manera de restaurar el contenido de una cuenta de DocumentDB eliminada. Si se elimina una cuenta de DocumentDB, se eliminarán todos los recursos de la cuenta, incluidas las bases de datos, colecciones, documentos y datos adjuntos.

![](./media/documentdb-manage-account/image009.png)

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a la cuenta de DocumentDB que quiere eliminar.

2.      En la hoja **Cuenta de DocumentDB**, haga clic en el comando **Eliminar**.

3.      En la hoja de confirmación que aparece, escriba el nombre de la cuenta de DocumentDB para confirmar que quiere eliminarla.

4.      En la hoja de confirmación, haga clic en el botón **Eliminar**.

## <a id="next"></a>Pasos siguientes

Descubra cómo dar sus [primeros pasos con una cuenta de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Para obtener más información acerca de DocumentDB, consulte la documentación de Microsoft Azure DocumentDB en

[azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 

<!--HONumber=49--> 