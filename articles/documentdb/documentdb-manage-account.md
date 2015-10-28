<properties 
	pageTitle="Administración de una cuenta de DocumentDB mediante el Portal de vista previa de Azure | Microsoft Azure" 
	description="Administración de su cuenta de DocumentDB mediante el portal de vista previa de Azure Guía sobre cómo usar el Portal de vista previa de Azure para ver, copiar, eliminar y obtener acceso a cuentas." 
	keywords="Azure preview portal, documentdb, azure, Microsoft azure"
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
	ms.date="07/28/2015" 
	ms.author="stbaro"/>

# Administración de una cuenta de DocumentDB mediante el portal de vista previa de Azure

Obtenga información sobre cómo trabajar con claves, la configuración de coherencia y cómo eliminar una cuenta desde el Portal de vista previa de Azure.

## <a id="keys"></a>Visualización, copia y regeneración de las claves de acceso
Cuando se crea una cuenta de DocumentDB, el servicio genera dos claves de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de DocumentDB. Al proporcionar dos claves de acceso, DocumentDB permite regenerar las claves sin interrupción en la cuenta de esta base de datos.

En el [Portal de vista previa de Microsoft Azure](https://portal.azure.com/), obtenga acceso al elemento **Claves** de la hoja de **cuenta de DocumentDB** para ver, copiar y regenerar las claves de acceso que se usan para obtener acceso a la cuenta de DocumentDB.

![Captura de pantalla de portal de vista previa de Azure, hoja Claves](media/documentdb-manage-account/keys.png)

### Visualización y copia de una clave de acceso en el portal de vista previa de Azure

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB. 

2.      En el modo **Resumen**, haga clic en **Claves**.

3.      En la hoja **Claves**, haga clic en el botón **Copiar**, a la derecha de la clave que quiere copiar.

  ![Visualización y copia de una clave de acceso en el Portal de vista previa de Azure, hoja Claves](./media/documentdb-manage-account/image004.jpg)

### Regenerar las claves de acceso

Cambie las claves de acceso de la cuenta de DocumentDB de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de DocumentDB, de modo que puede usar una clave de acceso mientras regenera la otra.

> [AZURE.WARNING]La regeneración de las claves de acceso afecta a las aplicaciones que dependen de una clave actual. Todos los clientes que usan la clave de acceso para obtener acceso a la cuenta de DocumentDB deben estar actualizados para usar la nueva clave.

Si cuenta con aplicaciones o servicios en la nube que usan la cuenta de DocumentDB y regenera las claves, perderá las conexiones. Para evitarlo, sustituya las claves. Los siguientes pasos describen el proceso de distribución de las claves.

1.      Actualice la clave de acceso en el código de aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de DocumentDB.

2.      Vuelva a generar la clave de acceso primaria para su cuenta de DocumentDB. En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.

3.      En el modo Resumen, haga clic en **Claves**.

4.      En la hoja** Claves**, haga clic en el comando **Regenerar principal** y haga clic en **Aceptar** para confirmar que quiere generar una clave nueva.

5.      Tras comprobar que la clave nueva está disponible para su uso (aproximadamente 5 minutos después de la regeneración), actualice la clave de acceso en el código de aplicación para hacer referencia a la nueva clave de acceso principal.

6.      Vuelva a generar la clave de acceso secundaria.

*Tenga en cuenta que pueden pasar varios minutos antes de poder obtener acceso a la cuenta de DocumentDB con la clave que acaba de crear.*

## <a id="consistency"></a>Administración de la configuración de coherencia de DocumentDB
DocumentDB admite cuatro niveles diferenciados de coherencia de datos que el usuario puede configurar para que los desarrolladores puedan equilibrar la coherencia, la disponibilidad y la latencia de forma predecible.

- Una coherencia **fuerte** garantiza que las operaciones de lectura siempre devolverán el último valor que se escribió.

- La coherencia de **uso vinculado** garantiza que las lecturas no quedarán obsoletas. Garantiza de forma específica que les lecturas serán, como máximo, de K versiones anteriores a la última versión que se escribió.

- La coherencia de **sesión** garantiza lecturas monotónicas (nunca se leen datos antiguos, nuevos y antiguos en ese orden), operaciones de escritura monotónicas (las operaciones se ordenan) y que usted leerá los elementos escritos más recientes desde un único punto de vista del cliente.

- La coherencia **ocasional** garantiza que las operaciones de lectura siempre procesarán un subconjunto válido de las operaciones de escritura y que pueden converger de forma ocasional.

*Tenga en cuenta que, de forma predeterminada, se realiza el aprovisionamiento de las cuentas de DocumentDB con coherencia de nivel de sesión. Para obtener información adicional sobre la configuración de coherencia de DocumentDB, consulte la sección [Nivel de coherencia](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Para especificar la coherencia predeterminada de una cuenta de DocumentDB:

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB. 

2.      En el modo **Configuración**, haga clic en **Coherencia predeterminada**.

3.      En la hoja **Coherencia predeterminada**, seleccione el nivel de coherencia predeterminado que quiere para la cuenta de DocumentDB.

![Sesión de coherencia predeterminada](./media/documentdb-manage-account/image005.png)

![Sesión de coherencia vinculada](./media/documentdb-manage-account/image006.png)

4.      Haga clic en **Guardar**.

5.      Puede supervisar el progreso de la operación a través del Centro de notificaciones del Portal de vista previa de Azure.

*Tenga en cuenta que pueden pasar varios minutos antes de que los cambios en la configuración de coherencia predeterminada se hagan efectivos en la cuenta de DocumentDB.*

## <a id="delete"></a> Eliminación de una cuenta de DocumentDB en el portal de vista previa de Azure
Para quitar del portal de vista previa de Azure una cuenta de DocumentDB que ya no usa, ejecute el comando **Eliminar** de la hoja **Cuenta de DocumentDB**.

![Eliminación de una cuenta de DocumentDB en el portal de vista previa de Azure](./media/documentdb-manage-account/image009.png)

1.      En el [Portal de vista previa de Azure](https://portal.azure.com/), obtenga acceso a la cuenta de DocumentDB que quiere eliminar. 

2.      En la hoja **Cuenta de DocumentDB**, haga clic en el comando **Eliminar**.

3.      En la hoja de confirmación que aparece, escriba el nombre de la cuenta de DocumentDB para confirmar que quiere eliminarla.

4.      En la hoja de confirmación, haga clic en el botón **Eliminar**.

## <a id="next"></a>Pasos siguientes

Descubra cómo [dar sus primeros pasos con una cuenta de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Para obtener más información sobre DocumentDB, vea la documentación correspondiente en [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 
 

<!---HONumber=Oct15_HO3-->