<properties title="Monitor a DocumentDB Account" pageTitle="Manage a DocumentDB account | Azure" description="Learn how to manage your DocumentDB account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/03/2014" ms.author="hawong" />

# Cómo administrar una cuenta de la Base de datos de documentos

## Tabla de contenido

-   [Direccionamiento del Ver, copiar y regenerar las claves de acceso de la Base de datos de documentos](#keys)
-   [Direccionamiento del Administrar la configuración de coherencia de la Base de datos de documentos](#consistency)
-   [Direccionamiento del Administrar la configuración de capacidad de la Base de datos de documentos](#capacity)
-   [Direccionamiento del Eliminar una cuenta de la Base de datos de documentos](#delete)
-   [Pasos siguientes](#next)

## <span id="keys"></span></a>Direccionamiento del Ver, copiar y regenerar las claves de acceso

Cuando se crea una cuenta de la Base de datos de documentos, se generan dos claves maestras de acceso que se pueden usar para la autenticación cuando se accede a esta base de datos. Al proporcionar dos claves de acceso, la Base de datos de documentos permite regenerar las claves sin interrupción en la cuenta de esta base de datos.

En el [Portal de vista previa de administración de Azure](https://portal.azure.com/)[](http://manage.windowsazure.com), acceda al elemento **Claves** de la hoja de cuenta de la Base de datos de documentos para ver, copiar y regenerar las claves de acceso que se usan para acceder a la cuenta de esta base de datos.

![](./media/documentdb-manage-account/image002.jpg)

### Ver y copiar una clave de acceso

1.      En el [Portal de vista previa de administraciónde Azure](https://portal.azure.com/), acceda a la cuenta de la Base de datos de documentos. 

2.      En modo Resumen, haga clic en **Claves**.

3.      En la hoja Claves, haga clic en el botón **Copiar**, a la derecha de la clave que quiere copiar.

4.      Presione Ctrl+C para copiar la clave.

  ![](./media/documentdb-manage-account/image004.jpg)

### Regenerar las claves de acceso

Cambie las claves de acceso de la cuenta de la Base de datos de documentos de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de la Base de datos de documentos, de modo que puede usar una clave de acceso mientras regenera la otra.

**Advertencia**

La regeneración de las claves de acceso afecta a las aplicaciones que dependen de una clave actual. Todos los clientes que usan la clave de acceso para acceder a la cuenta de la Base de datos de documentos deben estar actualizados para usar la nueva clave.

Si cuenta con aplicaciones o servicios en la nube que usan la cuenta de la Base de datos de documentos y regenera las claves, perderá las conexiones. Para evitarlo, sustituya las claves. Para ello, puede hacer lo siguiente:

1.      Actualice la clave de acceso en el código de aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de la Base de datos de documentos.

2.      Regenere la clave de acceso principal de la cuenta de la Base de datos de documentos. En el [Portal de vista previa de administración de Azure](https://portal.azure.com/), acceda a dicha cuenta.

3.      En modo Resumen, haga clic en **Claves**.

4.      En la hoja Claves, haga clic en el comando **Regenerar principal** y en **Aceptar** para confirmar que quiere generar una clave nueva.

5.      Tras comprobar que la clave nueva está disponible para su uso (aproximadamente 5 minutos después de la regeneración), actualice la clave de acceso en el código de aplicación para hacer referencia a la nueva clave de acceso principal.

6.      Regenere la clave de acceso secundaria.

*Tenga en cuenta que pueden pasar varios minutos antes de poder acceder a la cuenta
de la Base de datos de documentos con la clave que acaba de crear.*

## <span id="consistency"></span></a>Direccionamiento del Administrar la configuración de coherencia de la Base de datos de documentos

La Base de datos de documentos admite cuatro niveles diferenciados de coherencia de datos que el usuario puede configurar para que los desarrolladores puedan equilibrar la coherencia, la disponibilidad y la latencia de forma predecible.

·         Una coherencia **fuerte** garantiza que las operaciones de lectura siempre devolverán el último valor que se escribió.

·         La coherencia de **uso vinculado** garantiza que las lecturas no quedarán obsoletas. Garantiza de forma específica que les lecturas serán, como máximo, de K versiones anteriores a la última versión que se escribió.

·         La coherencia de **sesión** garantiza lecturas monotónicas (nunca se leen datos antiguos, nuevos y antiguos en ese orden), operaciones de escritura monotónicas (las operaciones se ordenan) y que usted leerá los elementos escritos más recientes desde un único punto de vista del cliente.

·         La coherencia **ocasional** garantiza que las operaciones de lectura siempre procesarán un subconjunto válido de las operaciones de escritura y que pueden converger de forma ocasional.

*Tenga en cuenta que, de forma predeterminada, las cuentas de la Base de datos de documentos disponen de un nivel de coherencia de sesión. Para obtener más información sobre la configuración de coherencia de esta base de datos, consulte esta sección sobre el [nivel de coherencia](http://go.microsoft.com/fwlink/p/?LinkId=402365).*

### Para especificar la coherencia predeterminada de una cuenta de la Base de datos de documentos:

1.      En el [Portal de vista previa de administración de Azure](https://portal.azure.com/), acceda a la cuenta de la Base de datos de documentos. 

2.      En modo Configuración, haga clic en **Coherencia**.

3.      En la hoja Coherencia predeterminada, seleccione el nivel de coherencia predeterminado que quiere para la cuenta de la Base de datos de documentos.

4.      Haga clic en **Guardar**.

5.      Puede supervisar el progreso de la operación a través del Centro de notificaciones del Portal de vista previa de administración de Azure.

 ![](./media/documentdb-manage-account/image005.png)

 ![](./media/documentdb-manage-account/image006.png)

*Tenga en cuenta que pueden pasar varios minutos antes de que los cambios en la
configuración de coherencia predeterminada se hagan efectivos en la cuenta de la Base de datos de documentos.*

## <span id="capacity"></span></a>Direccionamiento del Administrar la configuración de capacidad de la Base de datos de documentos

La Base de datos de documentos de Microsoft Azure permite escalar con elasticidad, según evoluciona la demanda de la aplicación a lo largo de su ciclo de vida. Para realizar el escalado de la Base de datos de documentos, se aumenta la capacidad de la cuenta de esta base de datos a través del Portal de vista previa de administración de Azure.

Cuando se crea una cuenta de base de datos, esta dispone de rendimiento reservado y almacenamiento de base de datos. Puede cambiar el rendimiento y el almacenamiento de base de datos disponible para la cuenta en cualquier momento. Para ello, agregue o quite unidades de capacidad en el Portal de vista previa de administración de Azure. 

### Para agregar o quitar unidades de capacidad:

1.      En el [Portal de vista previa de administración de Azure](https:portal.azure.com/), acceda a la cuenta de la Base de datos de documentos. 

2.      En modo Uso, haga clic en **Escala**.

3.      En la hoja Escala, especifique el número de unidades de capacidad para la cuenta de la Base de datos de documentos.

4.      Haga clic en **Guardar** (tenga en cuenta que pueden pasar varios minutos antes de que la operación de escalado se complete. Puede supervisar su progreso a través del Centro de notificaciones del Portal de vista previa de administración de Azure.

*Tenga en cuenta que la vista previa de la Base de datos de documentos admite un máximo de 5 unidades de capacidad por cada cuenta de esta base de datos.*

![](./media/documentdb-manage-account/image007.png)

 

## <span id="delete"></span></a>Direccionamiento del Eliminar una cuenta de la Base de datos de documentos

Para quitar una cuenta de la Base de datos de documentos que ya no usa, ejecute el comando
**Eliminar** en la hoja de cuenta de esta base de datos.

**Advertencia**

*En la vista previa no se puede restaurar el contenido de una cuenta de la Base de datos de documentos que se eliminó. Al eliminar esta cuenta, se eliminan también todos los recursos correspondientes, incluidos documentos, bases de datos, colecciones y datos adjuntos.*

![](./media/documentdb-manage-account/image009.png)

1.      En el [Portal de vista previa de administración de Azure](https://portal.azure.com/), acceda a la cuenta de la Base de datos de documentos que quiere eliminar. 

2.      En la hoja de cuenta, haga clic en el comando **Eliminar**.

3.      En la hoja de confirmación que aparece, escriba el nombre de la cuenta de la Base de datos de documentos para confirmar que quiere eliminarla.

4.      En la hoja de confirmación, haga clic en el botón **Eliminar**.

## <span id="next"></span></a>Pasos siguientes

-   Descubra cómo [dar sus primeros pasos con una cuenta de la Base de datos de
    documentos](http://go.microsoft.com/fwlink/p/?LinkId=402364).
-   Para obtener más información sobre la Base de datos de documentos,
    consulte la documentación correspondiente de Azure en
    [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).

 

  [Direccionamiento del Ver, copiar y regenerar las claves de acceso de la Base de datos de documentos]: #keys
  [Direccionamiento del Administrar la configuración de coherencia de la Base de datos de documentos]: #consistency
  [Direccionamiento del Administrar la configuración de capacidad de la Base de datos de documentos]: #capacity
  [Direccionamiento del Eliminar una cuenta de la Base de datos de documentos]: #delete
  [Pasos siguientes]: #next
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg

