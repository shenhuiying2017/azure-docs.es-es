<properties
	pageTitle="Administración de una cuenta de DocumentDB mediante el Portal de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo administración su cuenta de DocumentDB mediante el Portal de Azure. Guía sobre cómo usar el Portal de Azure para ver, copiar, eliminar y obtener acceso a cuentas."
	keywords="Portal de Azure, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="anhoh"/>

# Administración de una cuenta de DocumentDB

Aprenda a establecer coherencia global y a administrar varias regiones para la disponibilidad global de los datos en Azure DocumentDB. Aprenda también cómo trabajar con claves y cómo eliminar una cuenta en el Portal de Azure.

## <a id="consistency"></a>Administración de la configuración de coherencia de DocumentDB

La selección del nivel de coherencia adecuado depende de la semántica de su aplicación. Debe familiarizarse con los niveles de coherencia disponibles en DocumentDB: [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB][consistency]. DocumentDB ofrece garantías de coherencia, disponibilidad y rendimiento, en cada nivel de coherencia disponible para la cuenta de base de datos. La configuración de la cuenta de base de datos con un nivel de coherencia alto requiere que los datos estén confinados en una sola región de Azure y que no estén disponibles globalmente. Por otro lado, los niveles de coherencia moderados: uso vinculado, sesión o posible, le permiten asociar un número cualquiera de regiones de Azure con su cuenta de base de datos. Estos sencillos pasos le muestran cómo seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos.

### Para especificar la coherencia predeterminada de una cuenta de DocumentDB:

1. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.
2. En la hoja de cuenta, si la hoja **Configuración** no está ya abierta, haga clic en **Toda la configuración**. ![Sesión de coherencia predeterminada][5]

3. En la hoja **Toda la configuración**, haga clic en la entrada **Coherencia predeterminada** en **Característica**. ![Sesión de coherencia predeterminada][6]

4. En la hoja **Coherencia predeterminada**, seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.
5. Puede supervisar el progreso de la operación a través del Centro de notificaciones del Portal de Azure.

> [AZURE.NOTE] Pueden pasar varios minutos antes de que los cambios en la configuración de coherencia predeterminada se hagan efectivos en la cuenta de DocumentDB.

## <a id="addregion"></a>Agregar regiones

DocumentDB está disponible en la mayoría de las [regiones de Azure][azureregions]. Tras seleccionar el nivel de coherencia predeterminado para la cuenta de base de datos, puede asociar una o varias regiones (según la elección del nivel de coherencia y las necesidades de distribución global).

> [AZURE.NOTE] En este momento, se pueden agregar nuevas regiones a nuevas cuentas de DocumentDB creadas el 13 de junio de 2016 o con posterioridad a esta fecha. Seleccione "Azure DocumentDB - Multi-region database Account (Cuenta de base de datos de varias regiones)" en Marketplace para crear una cuenta de varias regiones. Las cuentas creadas antes del 13 de junio estarán habilitadas para disponibilidad global en un futuro cercano.

1. En la barra de accesos directos del [Portal de Azure](https://portal.azure.com/), haga clic en **Cuentas de DocumentDB**.
2. En la hoja **Cuenta de DocumentDB**, seleccione la cuenta de base de datos que se va a modificar.
3. En la hoja de cuenta, si la hoja **Toda la configuración** no está ya abierta, haga clic en **Toda la configuración**.
4. En la hoja **Toda la configuración**, haga clic en **Agregar o quitar regiones**. ![Agregar regiones en Cuenta de DocumentDB > Configuración > Agregar o quitar regiones][1]
5. En la hoja **Agregar o quitar regiones**, seleccione las regiones para agregar o quitar y luego haga clic en **Aceptar**. Existe un costo por agregar regiones; consulte la página de precios para más información.

    ![Hacer clic en las regiones en el mapa para agregarlas o quitarlas][2]

### Selección de regiones

Al configurar dos o más regiones, se recomienda que las regiones se seleccionen en función de los pares de regiones descritos en el artículo [Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure][bcdr].

En concreto, cuando se configuran varias regiones, asegúrese de seleccionar el mismo número de regiones (+/-1 para par o impar) de cada una de las columnas de regiones emparejadas. Por ejemplo, si quiere realizar la implementación en 4 regiones de Estados Unidos, seleccione 2 regiones de Estados Unidos en la columna izquierda y 2 en la derecha. Por lo tanto, el siguiente sería un conjunto adecuado: oeste de EE. UU., centro-norte de EE. UU. y centro-sur de EE. UU.

Cuando solo 2 regiones están configuradas para escenarios de recuperación ante desastres, es importante seguir estas instrucciones. Cuando hay más de 2 regiones, se recomienda seguir estas instrucciones, aunque no es obligatorio siempre y cuando algunas de las regiones seleccionadas respeten este emparejamiento.

## <a id="selectwriteregion"></a>Selección de la región de escritura

Aunque todas las regiones asociadas con la cuenta de base de datos de DocumentDB pueden atender lecturas (tanto de un elemento como paginadas de varios elementos) y consultas, solo una región puede recibir activamente las solicitudes de escritura (insert, upsert, replace, delete). Para establecer la región de escritura activa, haga lo siguiente:


1. En la hoja **Cuenta de DocumentDB**, seleccione la cuenta de base de datos que se va a modificar.
2. En la hoja de cuenta, si la hoja **Toda la configuración** no está ya abierta, haga clic en **Toda la configuración**.
3. En la hoja **Toda la configuración**, haga clic en **Write Region Priority** (Prioridad de región de escritura). ![Cambiar la región de escritura en Cuenta de DocumentDB > Configuración > Agregar o quitar regiones][3]
4. Haga clic y arrastre las regiones para ordenar la lista de regiones. La primera región en la lista de regiones es la región de escritura activa. ![Cambiar la región de escritura reordenando la lista de regiones en Cuenta de DocumentDB > Configuración > Cambiar regiones de escritura][4]

## <a id="keys"></a>Visualización, copia y regeneración de las claves de acceso
Cuando se crea una cuenta de DocumentDB, el servicio genera dos claves de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de DocumentDB. Al proporcionar dos claves de acceso, DocumentDB permite regenerar las claves sin interrupción en la cuenta de esta base de datos.

En el [Portal de Microsoft Azure](https://portal.azure.com/), acceda a la hoja **Claves** de la barra **Essentials** (Información básica) en la hoja **Cuenta de DocumentDB** para ver, copiar y regenerar las claves de acceso que se usan para obtener acceso a la cuenta de DocumentDB.

![Captura de pantalla del Portal de Azure, hoja Claves](./media/documentdb-manage-account/keys.png)

Otra opción consiste en acceder a la entrada **Claves** desde la hoja **Toda la configuración**.

![Toda la configuración, hoja Claves](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] Tenga en cuenta que la hoja **Claves** también incluye cadenas de conexión principal y secundaria que se pueden utilizar para conectarse a su cuenta desde la [Herramienta de migración de datos](documentdb-import-data.md).

También incluye las claves de solo lectura para proporcionar a los usuarios acceso de solo lectura a DocumentDB. Las lecturas y consultas son operaciones de solo lectura, mientras que las operaciones de creación, eliminación y reemplazo no lo son.

### Visualización y copia de una clave de acceso en el Portal de Azure

1. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.
2. En la barra **Essentials** (Información básica) de la hoja **Cuenta de DocumentDB**, haga clic en **Claves**.
3. En la hoja **Claves**, haga clic en el botón **Copiar**, a la derecha de la clave que quiere copiar. ![Visualización y copia de una clave de acceso en el Portal de Azure, hoja Claves](./media/documentdb-manage-account/copykeys.png)

### Regenerar las claves de acceso

Cambie las claves de acceso de la cuenta de DocumentDB de forma periódica para mantener sus conexiones más seguras. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de DocumentDB, de modo que puede usar una clave de acceso mientras regenera la otra.

> [AZURE.WARNING] La regeneración de las claves de acceso afecta a las aplicaciones que dependen de una clave actual. Todos los clientes que usan la clave de acceso para obtener acceso a la cuenta de DocumentDB deben estar actualizados para usar la nueva clave.

Si cuenta con aplicaciones o servicios en la nube que usan la cuenta de DocumentDB y regenera las claves, perderá las conexiones. Para evitarlo, sustituya las claves. Los siguientes pasos describen el proceso de distribución de las claves.

1. Actualice la clave de acceso en el código de aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de DocumentDB.

2. Vuelva a generar la clave de acceso primaria para su cuenta de DocumentDB. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a su cuenta de DocumentDB.

3. En la barra **Essentials** (Información básica) de la hoja **Cuenta de DocumentDB**, haga clic en **Claves**.

4. En la hoja** Claves**, haga clic en el comando **Regenerar principal** y haga clic en **Aceptar** para confirmar que quiere generar una clave nueva.

5. Tras comprobar que la clave nueva está disponible para su uso (aproximadamente 5 minutos después de la regeneración), actualice la clave de acceso en el código de aplicación para hacer referencia a la nueva clave de acceso principal.

6. Vuelva a generar la clave de acceso secundaria.


> [AZURE.NOTE] Pueden pasar varios minutos antes de poder obtener acceso a la cuenta de DocumentDB con la clave que acaba de crear.

## </a><a id="delete">Eliminación de una cuenta de DocumentDB
Para quitar del Portal de Azure una cuenta de DocumentDB que ya no usa, ejecute el comando **Eliminar** de la hoja **Cuenta de DocumentDB**.

![Eliminación de una cuenta de DocumentDB en el Portal de Azure](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. En el [Portal de Azure](https://portal.azure.com/), obtenga acceso a la cuenta de DocumentDB que quiere eliminar.
2. En la hoja **Cuenta de DocumentDB**, haga clic en el comando **Eliminar cuenta**.
3. En la hoja de confirmación que aparece, escriba el nombre de la cuenta de DocumentDB para confirmar que quiere eliminarla.
4. En la hoja de confirmación, haga clic en el botón **Eliminar**.

## <a id="next"></a>Pasos siguientes

Descubra cómo [dar sus primeros pasos con una cuenta de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Para obtener más información sobre DocumentDB, consulte la documentación correspondiente en [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/es-ES/regions/#services
[offers]: https://azure.microsoft.com/es-ES/pricing/details/documentdb/

<!---HONumber=AcomDC_0720_2016-->