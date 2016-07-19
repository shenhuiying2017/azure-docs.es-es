<properties
	pageTitle="Introducción al Catálogo de datos | Microsoft Azure"
	description="Tutorial integral que muestra los escenarios y las funcionalidades del Catálogo de datos de Azure."
	documentationCenter=""
	services="data-catalog"
	authors="steelanddata"
	manager=""
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="07/06/2016"
	ms.author="spelluru"/>

# Tutorial: Introducción al Catálogo de datos de Azure
El Catálogo de datos de Azure es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección para recursos de datos empresariales. Si desea información detallada, consulte [¿Qué es el Catálogo de datos de Azure](data-catalog-what-is-data-catalog.md).

Este tutorial le ayudará a empezar a trabajar con el Catálogo de datos de Azure. En este tutorial, realizará los siguientes pasos:

| Paso | Descripción |
| :--- | :---------- |
| [Aprovisionamiento del catálogo de datos](#provision-data-catalog) | En este paso, realizará el aprovisionamiento/configuración del Catálogo de datos de Azure. Tendrá que realizar este paso únicamente si el catálogo no se ha configurado antes. Solo puede tener un catálogo de datos por organización (dominio de Azure Active Directory), aunque haya varias suscripciones asociadas a su cuenta de Azure. | 
| [Registro de los recursos de datos](#register-data-assets) | En este paso, registrará los recursos de datos de la base de datos de ejemplo AdventureWorks2014 con el catálogo de datos. Registro es el proceso de extraer metadatos estructurales clave (como nombres, tipos y ubicaciones) del origen de datos, y copiar esos metadatos en el catálogo. El origen de datos y los recursos de datos permanecen donde están, pero el catálogo usa los metadatos para que se puedan detectar y comprender más fácilmente. |
| [Detección de los recursos de datos](#discover-data-assets) | En este paso, usará el portal del Catálogo de datos de Azure para detectar recursos de datos que se registraron en el paso anterior. Cuando se ha registrado un origen de datos con el Catálogo de datos de Azure, el servicio indexa sus metadatos, de forma que los usuarios pueden realizar con facilidad las búsquedas de los datos que necesitan. |
| [Anotación de los recursos de datos](#annotate-data-assets) | En este paso, proporcionará anotaciones (descripciones, etiquetas, documentación, expertos, etc.) para que los recursos de datos complementen los metadatos extraídos del origen de datos y con ello conseguir que más personas lo comprendan. | 
| [Conexión con los recursos de datos](#connect-to-data-assets) | En este paso, abrirá los recursos de datos en las herramientas de cliente integrado, como Excel y SQL Server Data Tools, así como en una herramienta no integrada (SQL Server Management Studio) utilizando información de conexión. |
| [Administración de recursos de datos](#manage-data-assets) | En este paso, verá cómo configurar la seguridad de los recursos de datos. Catálogo de datos no ofrece a los usuarios acceso a los mismos datos. El propietario del origen de datos es quien controla el acceso a los datos. <br/><br/>Catálogo de datos permite a los usuarios detectar los orígenes de datos y ver los **metadatos** relacionados con los orígenes registrados en el catálogo. Sin embargo, puede haber situaciones en las que los orígenes de datos solo deben ser visibles a usuarios específicos o a los miembros de grupos específicos. Para estos escenarios, Catálogo de datos permite a los usuarios tomar posesión de los recursos de datos registrados en el catálogo y, después, controlar la visibilidad de los recursos que poseen. | 
| [Eliminación de los recursos de datos](#remove-data-assets) | En este paso, aprenderá a quitar los recursos de datos del catálogo de datos. |  
 
## Requisitos previos de tutoriales

Antes de empezar este tutorial, debe contar con lo siguiente:

### Suscripción de Azure
Para configurar el Catálogo de datos de Azure, tiene que ser **propietario o copropietario** de una suscripción de Azure.

Las suscripciones de Azure le ayudan a organizar el acceso a los recursos de servicio en la nube como Catálogo de datos de Azure. También le ayudan a controlar cómo se informa, factura y paga el uso de recursos. Cada suscripción puede tener una configuración de facturación y pago diferente, por lo que puede tener varias suscripciones y planes diferentes por departamento, proyecto, oficina regional, etc. Cada servicio en la nube pertenece a una suscripción, y debe tener una suscripción para poder configurar el Catálogo de datos de Azure. Para obtener más información, vea [Administrar cuentas, suscripciones y roles administrativos](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Si no tiene una suscripción, puede crear una cuenta de prueba gratuita en tan solo un par de minutos. Consulte la [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) para más detalles.

### Azure Active Directory
Para configurar el Catálogo de datos de Azure, es necesario que inicie sesión con una **cuenta de usuario de Azure Active Directory**, este usuario tiene que ser el propietario o copropietario de una suscripción de Azure.

Azure Active Directory (Azure AD) proporciona una manera fácil para la empresa de administrar la identidad y acceso, tanto en la nube y como local. Los usuarios pueden usar una única cuenta profesional o educativa para efectuar el inicio de sesión único en cualquier aplicación web en la nube y local. Catálogo de datos de Azure usa Azure AD para autenticar el inicio de sesión. Para más información, consulte [¿Qué es Azure Active Directory?](../active-directory/active-directory-whatis.md)

### Configuración de directivas de Active Directory

En ocasiones, es posible que los usuarios se encuentren en una situación en la que puedan iniciar sesión en el portal del Catálogo de datos de Azure, pero cuando intenten iniciar sesión en la herramienta de registro de orígenes de datos reciban un mensaje de error que les impida iniciar sesión. El problema de este comportamiento puede ocurrir solamente cuando el usuario esté en la red de la empresa, o bien solo cuando el usuario se conecte desde fuera de la red de empresa.

La herramienta de registro usa la **Autenticación de formularios** para validar los inicios de sesión de usuario en Active Directory. Para iniciar sesión correctamente, un administrador de Active Directory tiene que habilitar la autenticación de formularios en la **directiva de autenticación global**.

La directiva de autenticación global permite habilitar los métodos de autenticación de forma independiente para las conexiones de extranet y de intranet, como se indica más adelante. Es posible que se produzcan errores de inicio de sesión si no está habilitada la autenticación de formularios para la red desde la que se conecta el usuario.

 ![Directiva de autenticación global de Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obtener más información, vea [Configuración de directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).

## Aprovisionamiento del catálogo de datos
Puede aprovisionar solamente un catálogo de datos por organización (Dominio de Azure Active Directory). Por lo tanto, si el propietario o copropietario de una suscripción de Azure que pertenezca a este dominio de Active Directory ya ha creado un catálogo, no podrá volver a crear un catálogo incluso si tiene varias suscripciones de Azure. Para comprobar si un usuario ha creado ya un catálogo de datos en el dominio de Active Directory, vaya a http://azuredatacatalog.com y compruebe si puede ver un catálogo. Si ya hay un catálogo creado, omita el procedimiento a continuación y vaya a la siguiente sección.

1. Vaya a [https://azure.microsoft.com/services/data-catalog](https://azure.microsoft.com/services/data-catalog).

	![Catálogo de datos de Azure: página de aterrizaje de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png) y haga clic en **Introducción**.
2. Inicie sesión con la cuenta de un usuario que sea el **propietario o copropietario** de una suscripción de Azure. Debería ver la página siguiente después de iniciar sesión correctamente.

	![Catálogo de datos de Azure: aprovisionamiento del catálogo de datos](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
2. Especifique un **nombre** para el catálogo de datos, la **suscripción** que desea utilizar y la **ubicación** para el catálogo.
3. Expanda **Precios** y especifique la **edición** (Gratis o Estándar) del Catálogo de datos de Azure. ![Catálogo de datos de Azure: selección de edición](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
4. Expanda **Usuarios de catálogo**, haga clic en **Agregar** para agregar usuarios para el catálogo de datos. Se le agregará automáticamente a este grupo. ![Catálogo de datos de Azure: usuarios](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
5. Expanda **Administradores de catálogo**, haga clic en **Agregar** para agregar administradores adicionales para el catálogo de datos. Se le agregará automáticamente a este grupo. ![Catálogo de datos de Azure: administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
6. Haga clic en el botón **Crear catálogo** para crear el catálogo de datos para su organización. Una vez que el catálogo de datos se haya creado correctamente podrá ver la página principal del mismo. ![Catálogo de datos de Azure: creado](media/data-catalog-get-started/data-catalog-created.png)

### Búsqueda de un catálogo de datos en el Portal de Azure
1. En una pestaña independiente o en una nueva ventana del explorador web, vaya a [https://portal.azure.com](https://portal.azure.com) e inicie sesión con la misma cuenta que utilizó para crear el catálogo de datos en el paso anterior.
2. Haga clic en **Examinar** y, luego, en **Catálogo de datos**.

	![Catálogo de datos de Azure: examinar en el Portal de Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)
3. Debería ver el catálogo de datos que creó.

	![Catálogo de datos de Azure: visualización del catálogo en lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Haga clic en el catálogo que creó y se le debería mostrar la hoja del **Catálogo de datos** en el Portal.

	![Catálogo de datos de Azure: hoja en el portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Puede ver las propiedades del catálogo de datos y puede también actualizarlas. Por ejemplo, haga clic en **Plan de tarifa** y cambie la edición.

	![Catálogo de datos de Azure: plan de tarifa](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### Base de datos de ejemplo Adventure Works 
En este tutorial, registrará recursos de datos (tablas) de la base de datos de ejemplo Adventure Works2014 para el motor de base de datos de SQL Server, pero puede usar cualquier origen de datos admitido si prefiere trabajar con datos que conozca y sean y pertinentes para su rol. Consulte [Orígenes de datos compatibles con el Catálogo de datos de Azure](data-catalog-dsr.md) para ver una lista de los orígenes de datos admitidos.

### Instalación de la base de datos Adventure Works 2014 OLTP
La base de datos Adventure Works admite escenarios de procesamiento de transacciones en línea estándar para un fabricante de bicicletas ficticio (Adventure Works Cycles), que incluye productos, ventas y compras. En este tutorial registrará información acerca de los productos en el **Catálogo de datos de Azure**.

A continuación se muestra cómo instalar la base de datos de ejemplo Adventure Works.

1. Descargue [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) en CodePlex.
2. Siga las instrucciones de este artículo: [Restaurar una copia de seguridad de base de datos (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx) para restaurar la base de datos en su equipo. **Pasos rápidos**:
	1. Abra SQL Server Management Studio y conéctese al motor de base de datos de SQL Server.
	2. Haga clic con el botón derecho en **Bases de datos**, seleccione **Restaurar base de datos**.
	3. En el cuadro de diálogo **Restaurar base de datos** seleccione la opción **Dispositivo** para **Origen**, y haga clic en **Examinar (...)**.
	4. En el cuadro de diálogo **Seleccionar dispositivos de copia de seguridad**, haga clic en **Agregar**.
	5. Navegue hasta la carpeta donde tenga el archivo **AdventureWorks2014.bak**, seleccione el archivo y haga clic en **Aceptar** para cerrar el cuadro de diálogo **Buscar archivo de copia de seguridad**.
	6. En el cuadro de diálogo **Seleccionar dispositivos de copia de seguridad**, haga clic en **Aceptar**.
	7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Restaurar base de datos**.

Ahora, veamos cómo registrar recursos de datos de la base de datos de ejemplo Adventure Works con el **Catálogo de datos de Azure**.

## Registro de los recursos de datos

En este ejercicio usará la herramienta de registro para registrar los recursos de datos de la base de datos Adventure Works en el catálogo. Registro es el proceso de extraer metadatos estructurales clave (como nombres, tipos y ubicaciones) del origen de datos y los recursos que contiene y copiar esos metadatos en el catálogo. El origen de datos y los recursos de datos permanecen donde están, pero el catálogo usa los metadatos para que se puedan detectar y comprender más fácilmente.

### A continuación se muestra cómo registrar un origen de datos

1.	Vaya a [https://azuredatacatalog.com](https://azuredatacatlog.com) y haga clic en **Publicar datos** en la página principal.

    ![Catálogo de datos de Azure: botón Publicar datos](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Haga clic en **Iniciar aplicación** para descargar, instalar y ejecutar la **herramienta de registro** en el equipo.

    ![Catálogo de datos de Azure: botón Iniciar](media/data-catalog-get-started/data-catalog-launch-application.png)

4. En la página **Bienvenida**, haga clic en **Iniciar sesión** y escriba sus credenciales.

	![Catálogo de datos de Azure: diálogo de bienvenida](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

5. En la página **Catálogo de datos de Microsoft Azure**, haga doble clic en **SQL Server**, o bien haga clic en **SQL Server** y luego en **Siguiente**.

    ![Catálogo de datos de Azure: orígenes de datos](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Especifique las propiedades de la conexión de SQL Server para **AdventureWorks2014** (consulte el ejemplo siguiente) y haga clic en **CONECTAR**.

    ![Catálogo de datos de Azure: configuración de la conexión con SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	En la página siguiente es donde se registran los metadatos de su recurso de datos. En este ejemplo, registrará los objetos de **Production/Product** desde el espacio de nombres Production de AdventureWorks. Aquí se muestra cómo hacerlo:
    
	1. En el árbol **Jerarquía de servidor**, expanda **AdventureWorks2014**, y haga clic en **Production**.
	2. Presione Ctrl + haga clic en **Product**, **ProductCategory**, **ProductDescription** y **ProductPhoto**.
	3. Haga clic en la **flecha de movimiento de selección** **(>)**. Con esto se moverán todos los objetos de Product seleccionados a la lista **Objetos que se registrarán**.
			
    	![Catálogo de datos de Azure: examinar u seleccionar objetos](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
	4. Seleccione **Incluir vista previa** para incluir una vista previa de instantánea de los datos. La instantánea incluye hasta 20 registros de cada tabla y se copia en el catálogo.
	5. Seleccione **Incluir perfil de datos** para incluir una instantánea de las estadísticas de objeto para el perfil de datos (por ejemplo: los valores mínimos, máximos y promedio para una columna, el número de filas, etc.).
	5. En **Agregar etiquetas**, escriba **adventure works, cycles**. De este modo se agregarán etiquetas de búsqueda para estos recursos de datos. Las etiquetas son una excelente manera de ayudar a los usuarios buscar un origen de datos registrados.
	6. (opcional) Especifique el nombre de un **experto** en estos datos.
	
    	![Tutorial del Catálogo de datos de Azure: objetos para registrar](media/data-catalog-get-started/data-catalog-objects-register.png)
    
	7. Haga clic en **REGISTRAR**. Catálogo de datos de Azure registra los objetos seleccionados. En este ejercicio, se registran los objetos seleccionados de Adventure Works. La herramienta de registro extraer metadatos del recurso de datos y los copia en el servicio Catálogo de datos de Azure. Los datos permanecen donde se encuentren en ese momento y quedan bajo el control de los administradores y las directivas del sistema actual.
	
		![Catálogo de datos de Azure: objetos registrados](media/data-catalog-get-started/data-catalog-registered-objects.png)

	8. Para ver los objetos de origen de datos registrados, haga clic en **Ver portal**. En el portal del **Catálogo de datos de Azure**, confirme que ve las cuatro tablas y la base de datos en la vista de cuadrícula.
 
    	![Objetos en el portal del Catálogo de datos de Azure](media/data-catalog-get-started/data-catalog-view-portal.png)
  
	
En este ejercicio se han registrado objetos de la base de datos de ejemplo Adventure Works para que puedan ser detectados fácilmente por los usuarios de su organización. En el siguiente ejercicio aprenderá a detectar los activos de datos registrados.

## Detección de los recursos de datos
La detección en el Catálogo de datos de Azure usa dos mecanismos principales: la búsqueda y el filtrado.

**La búsqueda** está diseñada para ser potente e intuitiva: de forma predeterminada, se comparan los términos de la búsqueda con cualquier propiedad del catálogo, incluidas las anotaciones proporcionadas por el usuario.

**El filtrado** está diseñado para complementar la búsqueda. Puede seleccionar características específicas como expertos, tipos de origen de datos, tipos de objeto y etiquetas, para ver solo los recursos de datos que coincidan, así como para restringir los resultados de la búsqueda a los recursos correspondientes.

Con una combinación de la búsqueda y el filtrado, puede navegar rápidamente a través de los orígenes de datos registrados con el Catálogo de datos de Azure para detectar los recursos de datos que necesita.

En este ejercicio usará el portal del **Catálogo de datos de Azure** para detectar recursos de datos que registró en el ejercicio anterior. Consulte [Referencia de sintaxis de búsqueda en el Catálogo de datos](https://msdn.microsoft.com/library/azure/mt267594.aspx), para encontrar detalles sobre la sintaxis de búsqueda.

Veamos algunos ejemplos para detectar recursos de datos en el catálogo.

### Búsqueda básica
La búsqueda básica le permite buscar en el catálogo usando uno o más términos de búsqueda. Los resultados son los recursos que tienen alguna propiedad que coincide con uno o más de los términos especificados.

1. Haga clic en el botón **Inicio** en el portal del Catálogo de datos de Azure. Si ha cerrado el explorador web, vaya a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. En el cuadro de búsqueda en la parte superior, escriba **cycles** y haga clic en el icono de **búsqueda** (o) presione **ENTRAR**.
	
	![Catálogo de datos de Azure: búsqueda de texto básico](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Confirme que ve las cuatro tablas y la base de datos (AdventureWorks2014) en los resultados. Puede alternar entre la **vista de cuadrícula** y la **vista de lista** haciendo clic en los botones en la parte superior, tal como se muestra en la siguiente imagen. Observe que la palabra clave de la búsqueda aparece resaltada en los resultados de búsqueda ya que la opción **Resaltar** en la parte superior del cuadro está **activada**. También puede especificar el número de **resultados por página** en Resultados de la búsqueda.

	![Catálogo de datos de Azure: resultados de la búsqueda de texto básico](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
	
	Puede ver el panel **Búsquedas** a la izquierda y el panel **Propiedades** a la derecha. El panel Búsquedas le permite cambiar los criterios de búsqueda y filtrar los resultados. El panel Propiedades muestra las propiedades de un objeto seleccionado en la cuadrícula o en la lista.

4. Haga clic en **Product** en los resultados de búsqueda. Haga clic en pestañas **Vista previa**, **Columnas**, **Perfil de datos**, y **Documentación** (o) use la flecha **ascendente** para expandir el panel inferior en el medio.
 
	![Catálogo de datos de Azure: panel inferior](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
	
	En la pestaña **Vista previa**, debería ver la vista previa de los datos de la tabla Product.
5. Haga clic en la pestaña **Columnas** para encontrar detalles acerca de las columnas (como **nombre** y **tipo de datos**) en el recurso de datos.
6. Haga clic en la pestaña **Perfil de datos** para ver la generación de perfiles de datos (por ejemplo: número de filas, tamaño de los datos, valor mínimo de una columna, etc.) en el recurso de datos.
6. Filtre los resultados utilizando **Filtros** a la izquierda. Por ejemplo, si hace clic en **Tabla** para **Tipo de objeto** debería ver solo las cuatro tablas, no la base de datos.

	![Catálogo de datos de Azure: resultados de búsqueda con filtro](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### Ámbito de propiedad
El ámbito de propiedad le permite detectar los recursos de datos donde el término de búsqueda coincide con la propiedad especificada.

3. Desactive el filtro **Tabla** para **Tipo de objeto** en **Filtros**.
4. En el cuadro de búsqueda, escriba **tags:cycles** y haga clic en el icono de **búsqueda** (o) presione **ENTRAR**. Consulte [Referencia de sintaxis de búsqueda en el Catálogo de datos](https://msdn.microsoft.com/library/azure/mt267594.aspx) para conocer todas las propiedades puede usar para buscar en el catálogo de datos.
3. Confirme que ve las cuatro tablas y la base de datos (AdventureWorks2014) en los resultados.

	![Catálogo de datos: resultados de búsqueda de ámbito de propiedad](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### Guardado de una búsqueda 
1. En el panel Búsquedas a la izquierda, en la sección Búsqueda actual, haga clic en Guardar para guardar el criterio de búsqueda actual. Escriba un nombre para la búsqueda y haga clic en Guardar.
	
	![Catálogo de datos de Azure: guardar búsqueda](media/data-catalog-get-started/data-catalog-save-search.png)
2. Confirme que la búsqueda guardada se muestra en Búsquedas guardadas.

	![Catálogo de datos de Azure: búsquedas guardadas](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Haga clic en la flecha abajo para ver las acciones que puede realizar en la búsqueda guardada (cambiar nombre, eliminar, establecer como búsqueda predeterminada). ![Catálogo de datos de Azure: opciones de búsquedas guardadas](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### Operadores booleanos
Los operadores booleanos permiten ampliar o reducir una búsqueda.

2. En el cuadro de búsqueda, escriba **tags:cycles AND objectType:table** y haga clic en el icono de **búsqueda** (o) presione **ENTRAR**.
3. Confirme que aparecen solo tablas en los resultados, no la base de datos.

	![Catálogo de datos de Azure: operador booleano en la búsqueda](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### Agrupación con paréntesis
La agrupación con paréntesis le permite usar paréntesis para agrupar partes de la consulta y así conseguir aislamiento lógico, especialmente en combinación con los operadores booleanos.

1. En el cuadro de búsqueda, escriba **name:product AND (tags:cycles AND objectType:table)** y haga clic en el icono de **búsqueda** (o) presione **ENTRAR**.
2. Confirme que aparece solo la tabla **Product** en los resultados de la búsqueda.

	![Catálogo de datos de Azure: búsqueda con agrupación](media/data-catalog-get-started/data-catalog-grouping-search.png)

### Operadores de comparación
Los operadores de comparación le permiten usar comparaciones diferentes de la igualdad de propiedades que tengan tipos de datos numéricos y de fechas.

1. En el cuadro de búsqueda, escriba **lastRegisteredTime:>"06/09/2016"**.
2. Desactive el filtro **Tabla** para **Tipo de objeto** en el panel de la izquierda.
3. Haga clic en el icono de **búsqueda** o presione **ENTRAR**.
2. Confirme que aparecen las tablas Product, ProductCategory, ProductDescription y ProductPhoto y la base de datos AdventureWorks2014 que registró en los resultados de búsqueda.

	![Catálogo de datos de Azure: resultados de búsqueda con comparación](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Consulte [Detección de orígenes de datos](data-catalog-how-to-discover.md) para una información detallada acerca de la detección de recursos de datos, y [Referencia de sintaxis de búsqueda en el Catálogo de datos](https://msdn.microsoft.com/library/azure/mt267594.aspx) para la sintaxis de búsqueda.

## Anotación de los recursos de datos
En este ejercicio, usará el portal del **Catálogo de datos de Azure** para anotar (agregar descripciones, etiquetas, expertos, etc.) los recursos de datos que ha registrado anteriormente en el catálogo. Las anotaciones que proporcione complementarán y mejorarán los metadatos estructurales extraídos del origen de datos durante el registro y hará que los recursos de datos sean mucho más fáciles de detectar y entender.

### A continuación se indica cómo agregar anotaciones a los activos de datos
En este paso, anotará un único conjunto de datos (ProductPhoto). Agregará un nombre descriptivo, descripción, etc... para el recurso de datos ProductPhoto.

1.  Si ha cerrado el explorador, vaya a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) y realice una búsqueda con **tags:cycles** para encontrar los recursos de datos que ha registrado.
2. Haga clic en **ProductPhoto** en los resultados de la búsqueda.
3. Escriba **Imágenes del producto** para **Nombre descriptivo** y **Fotos del producto para materiales de marketing** para el campo **Descripción**.

	![Catálogo de datos de Azure: descripción de ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)

	La **Descripción** ayudará a otros usuarios a detectar y entender por qué y cómo usar el recurso de datos seleccionado. También puede agregar más etiquetas y ver columnas. Ahora puede intentar buscar y filtrar para detectar recursos de datos mediante los metadatos descriptivos que haya agregado al catálogo.

Tenga en cuenta que también puede hacer lo siguiente en esta página:

- Agregar a expertos para el recurso de datos. Haga clic en **Agregar...** en **Expertos:** en el panel derecho.
- Agregar etiquetas en el nivel de conjunto de datos. Haga clic en **Agregar...** en **Etiquetas:** en el panel derecho. Una etiqueta puede ser una etiqueta de usuario o una etiqueta de glosario. La edición estándar del Catálogo de datos incluye un glosario empresarial que permite a los administradores del catálogo definir una taxonomía empresarial central. Después, los usuarios del catálogo pueden anotar los recursos de datos con los términos del glosario. Para más información, consulte [Configuración del glosario empresarial para el etiquetado regulado](data-catalog-how-to-business-glossary.md)
- Agregar etiquetas a nivel de columna. En el panel inferior, en la parte central, haga clic en **Agregar...** en **Etiquetas** para la columna que desee anotar.
- Agregar descripción en el nivel de columna. En el panel inferior, en la parte central, escriba **Descripción** para una columna. También puede ver los metadatos de descripción extraídos del origen de datos.
- Agregar información de **Solicitar acceso** que especifique a los usuarios cómo solicitar acceso al recurso de datos.

	![Catálogo de datos de Azure: incorporación de etiquetas, descripciones](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
  
- Haga clic en la pestaña **Documentación** en el panel inferior medio y proporcione documentación para el recurso de datos. La documentación del Catálogo de datos de Azure le permite usar el Catálogo de datos como un repositorio de contenido para crear una descripción completa de los recursos de datos.

	![Catálogo de datos de Azure: pestaña de documentación](media/data-catalog-get-started/data-catalog-documentation.png)


También puede realizar una selección múltiple o seleccionar todo, y agregar una anotación a varios o a todos los recursos de datos. Por ejemplo, puede seleccionar todos los recursos de datos que ha registrado y especificar a un experto para ellos.

![Catálogo de datos de Azure: anotación de varios recursos de datos](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

El Catálogo de datos de Azure admite el enfoque de colaboración abierta distribuida respecto a las anotaciones lo que permite que cualquier usuario del Catálogo de datos agregue etiquetas (de usuario o de glosario), descripciones y otros metadatos. Por tanto, cualquier usuario con una perspectiva sobre un recurso de datos y su uso puede capturar dicha perspectiva para que esté disponible para otros usuarios.

Consulte [Anotación de orígenes de datos](data-catalog-how-to-annotate.md) para ver información detallada acerca de las anotaciones de recursos de datos.
 
## Conexión con los recursos de datos
En este ejercicio, abrirá los recursos de datos en una herramienta de cliente integrado (Excel), así como una herramienta no integrada (SQL Server Management Studio) utilizando información de conexión.

> [AZURE.NOTE] Es importante recordar que **Catálogo de datos de Azure** no da a los usuarios acceso al origen de datos en sí: simplemente facilita a los usuarios detectarlos y comprenderlos. Cuando los usuarios se conectan a un origen de datos, la aplicación cliente que eligen usará sus credenciales de Windows o les pedirán las credenciales según sea necesario. Si no se ha concedido acceso al origen de datos previamente al usuario, deberá concederse acceso a este para que pueda conectarse.

### A continuación se indica cómo conectarse a un recurso de datos desde Excel

1. Seleccione **Product** en los resultados de búsqueda. Haga clic en **Abrir en** dentro de la barra de herramientas y seleccione **Excel**.
 
    ![Catálogo de datos de Azure: conexión con el recurso de datos](media/data-catalog-get-started/data-catalog-connect1.png)
5. Haga clic en **Abrir** en la ventana emergente de descarga en la parte inferior (esta experiencia puede variar dependiendo del explorador).

	![Catálogo de datos de Azure: archivo de conexión de Excel descargado](media/data-catalog-get-started/data-catalog-download-open.png)
6. En la ventana **Aviso de seguridad de Microsoft Excel**, haga clic en **Habilitar**.

	![Catálogo de datos de Azure: mensaje emergente de seguridad de Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
7. En el cuadro de diálogo **Importar datos**, mantenga los valores predeterminados y haga clic en **Aceptar**.

	![Catálogo de datos de Azure: datos de importación de Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
8. El origen de datos se abre en Excel.

    ![Catálogo de datos de Azure: tabla de producto en Excel](media/data-catalog-get-started/data-catalog-connect2.png)

En este ejercicio ha conectado a los recursos de datos detectados mediante el **Catálogo de datos de Azure**. El portal **Catálogo de datos de Azure** permite a los usuarios conectarse directamente con las aplicaciones de cliente integradas en su menú **Abrir en...**, y permite a los usuarios conectarse con cualquier aplicación que elijan usando la información de ubicación de conexión incluida en los metadatos de los recursos. Por ejemplo: puede utilizar SQL Server Management Studio para conectarse a la base de datos AdventureWorks2014 para tener acceso a los datos de los recursos de datos registrados en este tutorial.

1. Inicie **SQL Server Management Studio**.
2. En el cuadro de diálogo **Conectar con el servidor** escriba el **nombre del servidor** del panel **Propiedades** en el portal del Catálogo de datos de Azure.
3. Use **autenticación** y **credenciales** apropiadas para acceder al recurso de datos si ya tiene acceso al mismo. Si no tiene acceso, utilice la información en el campo Solicitar acceso para obtenerlo.

	![Catálogo de datos de Azure: solicitud de acceso](media/data-catalog-get-started/data-catalog-request-access.png)

Haga clic en **Ver cadenas de conexión** para ver y copiar las cadenas de conexión ADF.NET, ODBC y OLEDB en el Portapapeles para utilizarlas en la aplicación.

## Administración de recursos de datos
En este paso, verá cómo configurar la seguridad de los recursos de datos. Catálogo de datos no ofrece a los usuarios acceso a los mismos datos. El propietario del origen de datos es quien controla el acceso a los datos.

Catálogo de datos permite a los usuarios detectar los orígenes de datos y ver los **metadatos** relacionados con los orígenes registrados en el catálogo. Sin embargo, puede haber situaciones en las que los orígenes de datos solo deben ser visibles a usuarios específicos o a los miembros de grupos específicos. Para estos escenarios, Catálogo de datos permite a los usuarios tomar posesión de los recursos de datos registrados en el catálogo y, después, controlar la visibilidad de los recursos que poseen.

> [AZURE.NOTE] Las funcionalidades de administración descritas en este ejercicio solo están disponibles en la **edición Estándar del Catálogo de datos de Azure**, no en la **edición Gratis**. En **Catálogo de datos de Azure**, puede tomar posesión de los recursos de datos, agregar copropietarios a los recursos de datos y establecer la visibilidad de los recursos de datos.

### A continuación se muestra cómo tomar posesión de los recursos de datos y restringir la visibilidad

1. Si ha cerrado el explorador web, vaya a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com). En el cuadro de texto de búsqueda, escriba **tags:cycles** y presione **ENTRAR**.
3. Seleccione un elemento de la lista de resultados, por ejemplo, **Product**, haciendo clic en la casilla de la esquina superior derecha y luego haga clic en **Tomar propiedad** en la barra de herramientas, como se muestra en la siguiente imagen.
4. En el panel **Propiedades** dentro de la sección **Administración**, haga clic en **Tomar propiedad** debajo de **Administración** en el panel derecho.

	![Catálogo de datos de Azure: toma de posesión](media/data-catalog-get-started/data-catalog-take-ownership.png)
5. Para restringir la visibilidad, haga clic en **Propietarios y estos usuarios** en la sección **Visibilidad** y haga clic en **Agregar**. Escriba la dirección de correo electrónico del usuario en el cuadro de texto y presione ENTRAR.

    ![Catálogo de datos de Azure: restricción de acceso](media/data-catalog-get-started/data-catalog-ownership.png)

## Eliminación de los recursos de datos

En este ejercicio usará el portal del **Catálogo de datos de Azure** para quitar los datos de vista previa de los recursos de datos registrados y para eliminar los recursos de datos del catálogo.

En el **Catálogo de datos de Azure** se pueden eliminar uno o varios recursos.

### A continuación se muestra cómo eliminar uno o varios recursos de datos

1. Si ha cerrado el explorador web, vaya a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. En el cuadro de texto de búsqueda, escriba **tags:cycles** y presione **ENTRAR**.
3. Seleccione un elemento de la lista de resultados, por ejemplo, **ProductDescription**, haciendo clic en la casilla de la esquina superior derecha y luego haga clic en **Eliminar** en la barra de herramientas, como se muestra en la siguiente imagen.

	![Catálogo de datos de Azure: eliminación de artículo de cuadrícula](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
	
	Si utiliza la vista de lista (en lugar de la vista de cuadrícula), la casilla está a la izquierda del elemento como se muestra en la siguiente imagen.

	![Catálogo de datos de Azure: eliminación de artículo de lista](media/data-catalog-get-started/data-catalog-delete-list-item.png)

	También puede seleccionar varios recursos de datos y eliminarlos, según se muestra a continuación:

	![Catálogo de datos de Azure: eliminación de varios recursos de datos](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] El comportamiento predeterminado del catálogo es permitir que cualquier usuario registre cualquier origen de datos y permitir que cualquier usuario elimine cualquier recurso de datos que se haya registrado. Las capacidades de administración incluidas en la **edición Standard del Catálogo de datos de Azure** proporcionan opciones adicionales para la toma de posesión de los recursos y la restricción de quién puede detectar y eliminar los recursos.


## Resumen

En este tutorial ha explorado las funcionalidades esenciales del **Catálogo de datos de Azure**, entre las que se incluyen el registro, la anotación, la detección y la administración de recursos de datos empresariales. Ahora que ha completado el tutorial, ha llegado el momento de comenzar. Puede empezar hoy mismo por registrar los orígenes de datos en los que confían usted y su equipo, e invitando a compañeros a usar el catálogo.

## Referencias

- [Registro de recursos de datos](data-catalog-how-to-register.md)
- [Detección de recursos de datos](data-catalog-how-to-discover.md)
- [Anotación de recursos de datos](data-catalog-how-to-annotate.md)
- [Documentación de recursos de datos](data-catalog-how-to-documentation.md)
- [Conexión a recursos de datos](data-catalog-how-to-connect.md)
- [Cómo administrar recursos de datos](data-catalog-how-to-manage.md)

<!---HONumber=AcomDC_0713_2016-->