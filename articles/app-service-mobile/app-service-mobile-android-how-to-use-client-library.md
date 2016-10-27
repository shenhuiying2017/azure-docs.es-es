<properties
    pageTitle="Uso de la biblioteca de cliente de aplicaciones móviles de Android"
    description="Uso del SDK del cliente Android para aplicaciones móviles de Azure"
    services="app-service\mobile"
    documentationCenter="android"
    authors="yuaxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>



# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Uso de la biblioteca de cliente Android para Aplicaciones móviles

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

En esta guía se muestra cómo utilizar el SDK cliente de Android para Mobile Apps con el objetivo de implementar escenarios comunes, como los siguientes:

- Consultas de datos (inserción, actualización y eliminación)
- Autenticación
- Control de errores
- Personalización del cliente 

También realiza una profundización en el código de cliente común que se utiliza en la mayoría de aplicaciones móviles.

Esta guía se centra en el SDK de Android del cliente.  Para más información sobre los SDK del lado del servidor para Mobile Apps, consulte [Trabajar con el SDK del back-end de .NET][10] o [Uso del SDK del back-end de Node.js][11].

## <a name="reference-documentation"></a>Documentación de referencia

La [referencia de la API de Javadocs][12] para la biblioteca de cliente Android se puede encontrar en GitHub.

## <a name="supported-platforms"></a>Plataformas compatibles

El SDK de Android para Mobile Apps de Azure admite los niveles de API desde el 19 al 24 (de KitKat a Nougat).  

La autenticación de flujo de servidor utiliza una vista web para la interfaz de usuario presentada. Si el dispositivo no puede presentar una interfaz de usuario de vista web, hay que utilizar otros métodos de autenticación que están fuera del ámbito del producto.  Este SDK no es adecuado para dispositivos de tipo reloj o con restricciones similares.

## <a name="setup-and-prerequisites"></a>Configuración y requisitos previos

Complete el tutorial de [inicio rápido de Mobile Apps](app-service-mobile-android-get-started.md) .  Esta tarea garantiza que se cumplan todos los requisitos previos del desarrollo de aplicaciones de Mobile Apps de Azure.  El tutorial de inicio rápido ayuda a configurar la cuenta y a crear el primer back-end de aplicación móvil.

Si decide no completar el tutorial de inicio rápido, realice las tareas siguientes:

- [Cree un nuevo back-end de aplicación móvil][13] para usarlo con su aplicación Android.
- En Android Studio, [actualice los archivos de compilación de Gradle](#gradle-build).
- [Habilite permisos de Internet](#enable-internet).

###<a name="<a-name="gradle-build"></a>update-the-gradle-build-file"></a><a name="gradle-build"></a>Actualización del archivo de compilación de Gradle

Cambie ambos archivos **build.gradle** :

1. Agregue este código al archivo *build.gradle* del nivel **Project** dentro de la etiqueta *buildscript*:

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Agregue este código al archivo *build.gradle* del nivel **Module app** dentro de la etiqueta *dependencies*:

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Actualmente, la versión más reciente es la 3.1.0. [Aquí] se enumeran las versiones compatibles[14].

###<a name="<a-name="enable-internet"></a>enable-internet-permission"></a><a name="enable-internet"></a>Habilitación de permisos de Internet

Para obtener acceso a Azure, la aplicación debe tener habilitado el permiso de INTERNET. Si aún no está habilitado, agregue la siguiente línea de código a su archivo **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Profundización en los conceptos básicos

En esta sección se describe parte del código de la aplicación de inicio rápido relacionado con el uso de Mobile Apps de Azure.  

###<a name="<a-name="data-object"></a>define-client-data-classes"></a><a name="data-object"></a>Definición de clases de datos de cliente

Para acceder a los datos de tablas de SQL Azure, defina las clases de datos cliente que corresponden a las tablas del back-end de aplicación móvil. En los ejemplos de este tema se usa una tabla denominada **ToDoItem**, que tiene las siguientes columnas:

- id
- text
- complete

El objeto del cliente con tipo correspondiente:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

El código reside en un archivo denominado " **ToDoItem.java**".

Si la tabla de SQL Azure contuviera más columnas, agregaría los campos correspondientes a esta clase.  Por ejemplo, si el DTO (objeto de transferencia de datos) tuviera una columna Prioridad de tipo entero, podría agregar este campo junto con sus métodos de captador y establecedor:

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Para más información acerca de cómo crear tablas adicionales en el back-end de Mobile Apps, consulte [Definición de un controlador de tabla][15] (back-end de. NET) o [Definición de tablas con un esquema dinámico][16] (back-end de Node.js). Para un back-end de Node.js, también puede usar la configuración **Tablas fáciles** en [Azure Portal].

###<a name="<a-name="create-client"></a>how-to:-create-the-client-context"></a><a name="create-client"></a>Creación del contexto de cliente

Este código crea el objeto **MobileServiceClient** que se usa para obtener acceso al back-end de la aplicación móvil. El código está en el método `onCreate` de la clase **Activity** especificada en *AndroidManifest.xml* como una acción **MAIN** y una categoría **LAUNCHER**. En el código de inicio rápido, está en el archivo **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

En este código, reemplace `MobileAppUrl` por la dirección URL del back-end de la aplicación móvil, que se puede encontrar en el [Portal de Azure] en la hoja del back-end de la aplicación móvil. Para que esta línea de código se compile, también debe agregar la siguiente instrucción **import** :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="<a-name="instantiating"></a>how-to:-create-a-table-reference"></a><a name="instantiating"></a>Creación de una referencia de tabla

La forma más sencilla de consultar o modificar los datos del back-end es utilizar el *modelo de programación con tipo*, ya que Java es un lenguaje fuertemente tipado. Este modelo ofrece una perfecta serialización y deserialización de JSON mediante el uso de la biblioteca [gson][3] al enviar datos entre tablas y objetos cliente en el servidor SQL de Azure de back-end.

Para acceder a una tabla, cree primero un objeto [MobileServiceTable][8] mediante una llamada al método **getTable** en [MobileServiceClient][9].  Este método tiene dos sobrecargas:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

En el código siguiente, **mClient** es una referencia al objeto MobileServiceClient.  La primera sobrecarga se utiliza cuando coinciden el nombre de clase y el nombre de tabla, y es la que se emplea en el tutorial de inicio rápido:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

La segunda sobrecarga se utiliza cuando el nombre de tabla es diferente del nombre de clase: el primer parámetro es el nombre de tabla.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="<a-name="binding"></a>how-to:-bind-data-to-the-user-interface"></a><a name="binding"></a>Enlace de datos a la interfaz de usuario

El enlace de datos implica tres componentes:

- El origen de datos
- El diseño de la pantalla
- El adaptador que une a ambos

En nuestro código de muestra, devolvemos los datos de la tabla SQL Azure de Aplicaciones móviles **ToDoItem** en una matriz. Esta actividad es uno de los patrones más comunes para las aplicaciones de datos.  Las consultas en bases de datos normalmente devuelven una serie de filas que el cliente recibe en una lista o una matriz. En este ejemplo, la matriz es el origen de datos.

El código especifica un diseño de pantalla que define la vista de los datos que aparecerán en el dispositivo.  Los dos están vinculados mediante un adaptador, que en este código es una extensión de la clase **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="<a-name="layout"></a>how-to:-define-the-layout"></a><a name="layout"></a>Definición del diseño

El diseño lo definen varios fragmentos de código XML. Dado el diseño existente, el código siguiente representa el elemento **ListView** que queremos rellenar con nuestros datos de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

En el código anterior, el atributo *listitem* especifica el identificador del diseño de una fila concreta de la lista. Este código especifica una casilla de verificación y su texto asociado, y crea una instancia de esta para cada elemento de la lista. Este diseño no muestra el campo **id** y un diseño más complejo especificaría campos adicionales en la pantalla. Este código está en el archivo **row_list_to_do.xml**.

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="<a-name="adapter"></a>how-to:-define-the-adapter"></a><a name="adapter"></a>Definición del adaptador

Como el origen de datos de nuestra vista es una matriz de **ToDoItem**, se realiza una subclase en nuestro adaptador desde una clase **ArrayAdapter&lt;ToDoItem&gt;**. Esta subclase producirá una vista para cada **ToDoItem** que use el diseño de **row_list_to_do**.

En el código, definimos la siguiente clase que es una extensión de la clase **ArrayAdapter&lt;E&gt;**:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Reemplace el método **getView** de los adaptadores. Por ejemplo:

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Creamos una instancia de esta clase en nuestra actividad de la forma siguiente:

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

El segundo parámetro para el constructor ToDoItemAdapter es una referencia al diseño. Ahora podemos crear una instancia de **ListView** y asignarle el adaptador.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="<a-name="api"></a>the-api-structure"></a><a name="api"></a>La estructura de API

Las operaciones de tabla de Mobile Apps y las llamadas de API personalizadas son asincrónicas. Utilice los objetos [Future] y [AsyncTask] para los métodos asincrónicos que implican consultas, inserciones, actualizaciones y eliminaciones. El uso de objetos Future facilita la realización de varias operaciones en un subproceso en segundo plano sin tener que tratar con varias devoluciones de llamada anidadas.

Para ver un ejemplo, revise el archivo **ToDoActivity.java** del proyecto de inicio rápido de Android en [Azure Portal] .

#### <a name="<a-name="use-adapter"></a>how-to:-use-the-adapter"></a><a name="use-adapter"></a>Uso del adaptador

Ahora ya puede usar el enlace de datos. El código siguiente muestra cómo obtener los elementos de la tabla y rellena el adaptador local con los elementos devueltos.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Llame al adaptador en cualquier momento para modificar la tabla **ToDoItem** . Como las modificaciones se realizan de registro en registro, se ocupará de una sola fila en lugar de una serie de ellas. Al insertar un elemento, se llama al método **add** del adaptador; al eliminarlo, al método **remove**.

##<a name="<a-name="querying"></a>how-to:-query-data-from-your-mobile-app-backend"></a><a name="querying"></a>Consulta de datos desde el back-end de la aplicación móvil

En esta sección se describe cómo generar consultas al back-end de la aplicación móvil, lo cual incluye las siguientes tareas:

- [Devolver todos los elementos]
- [Filtro de datos devueltos]
- [Ordenar datos devueltos]
- [Devolver datos en páginas]
- [Seleccionar columnas específicas]
- [métodos de consulta](#chaining)

### <a name="<a-name="showall"></a>how-to:-return-all-items-from-a-table"></a><a name="showAll"></a>Devolver todos los elementos de una tabla

La consulta siguiente devuelve todos los elementos de la tabla **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

La variable *results* devuelve el conjunto de resultados de la consulta como una lista.

### <a name="<a-name="filtering"></a>how-to:-filter-returned-data"></a><a name="filtering"></a>Filtro de datos devueltos

La ejecución de la consulta siguiente devuelve todos los elementos de la tabla **ToDoItem** en los que **complete** es igual a **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** es la referencia a la tabla de servicio móvil que se ha creado previamente.

Se define un filtro mediante la llamada al método **where** en la referencia de tabla. Al método **where** le sigue un método **field** y a este le sigue un método que especifica el predicado lógico. Los posibles métodos de predicado incluyen **eq** (igual a), **ne** (no igual a), **gt** (mayor que), **ge** (mayor o igual que), **lt** (menor que) o **le** (menor o igual que). Estos métodos permiten comparar campos de número y cadena con valores específicos.

Puede filtrar por fechas. Los métodos siguientes permiten comparar el campo de fecha entero o partes de la fecha: **year**, **month**, **day**, **hour**, **minute** y **second**. El ejemplo siguiente agrega un filtro para los elementos cuyo valor *due date* sea igual a 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Los métodos siguientes admiten filtros complejos en campos de cadena: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** y **length**. El ejemplo siguiente filtra las filas de tabla donde la columna *text* empieza por PRI0.

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

En los campos de número se admiten los siguientes métodos de operador: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** y **round**. El ejemplo siguiente filtra las filas de tabla en las que **duration** es un número par.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Puede combinar predicados con estos métodos lógicos: **and**, **or** y **not**. En el ejemplo siguiente se combinan dos de los ejemplos anteriores.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Agrupe y anide los operadores lógicos:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Para obtener más información y ver ejemplos de filtrado, consulte [Exploring the richness of the Android client query model (Exploración de la riqueza del modelo de consulta del cliente Android)](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="<a-name="sorting"></a>how-to:-sort-returned-data"></a><a name="sorting"></a>Clasificación de datos devueltos

El código siguiente devuelve todos los elementos de una tabla de **ToDoItems** cuyo campo *text* sigue un orden ascendente. *mToDoTable* es la referencia a la tabla del back-end que ha creado anteriormente:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

El primer parámetro del método **orderBy** es una cadena igual al nombre del campo por el que realizar la ordenación. El segundo parámetro usa la enumeración **QueryOrder** para especificar si la ordenación será ascendente o descendente.  Si filtra con el método ***where***, este debe invocarse antes del método ***orderBy***.

### <a name="<a-name="paging"></a>how-to:-return-data-in-pages"></a><a name="paging"></a>Devolución de datos en páginas

El primer ejemplo muestra cómo seleccionar los cinco primeros elementos de una tabla. La consulta devuelve los elementos de una tabla de **ToDoItems**. **mToDoTable** es la referencia a la tabla del back-end que ha creado anteriormente:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Aquí se muestra una consulta que omite los cinco primeros elementos y que, a continuación, devuelve los cinco siguientes:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="<a-name="selecting"></a>how-to:-select-specific-columns"></a><a name="selecting"></a>Selección de columnas específicas

El código siguiente ilustra cómo devolver todos los elementos de una tabla de **ToDoItems**, pero solo muestra los campos **complete** y **text**. **mToDoTable** es la referencia a la tabla de back-end que se ha creado previamente.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Los parámetros para la función de selección son los nombres de las cadenas de las columnas de la tabla que desea devolver.

El método **select** debe ir después de métodos como **where** y **orderBy**. Puede ir seguido de métodos de paginación como **top**.

### <a name="<a-name="chaining"></a>how-to:-concatenate-query-methods"></a><a name="chaining"></a>Concatenación de métodos de consulta

Se pueden concatenar los métodos usados en la consulta de tablas de back-end. Gracias al encadenamiento de consultas, se pueden seleccionar columnas específicas de filas filtradas que se ordenan y paginan. Puede crear filtros lógicos complejos.
Cada método de consulta devuelve un objeto Query. Para finalizar las series de métodos y ejecutar la consulta, llame al método **execute** . Por ejemplo:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Los métodos de consulta encadenadas se deben ordenar de la siguiente forma:

1. Métodos de filtro (**where**)
2. Métodos de ordenación (**orderBy**)
3. Métodos de selección (**select**)
4. Métodos de paginación (**skip** y **top**)

##<a name="<a-name="inserting"></a>how-to:-insert-data-into-the-backend"></a><a name="inserting"></a>Inserción de datos en el back-end

Cree instancias de la clase *ToDoItem* y configure sus propiedades.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Después, utilice **insert()** insertar un objeto:

    ToDoItem entity = mToDoTable.insert(item).get();

La entidad devuelta coincide con los datos insertados en la tabla de back-end, incluido el identificador y los otros valores establecidos en el back-end.

Las tablas de Mobile Apps requieren una columna de clave principal denominada " **id**". De forma predeterminada, esta columna es una cadena. El valor predeterminado de la columna de identificador es un GUID.  Puede proporcionar otros valores únicos, como nombres de usuario o direcciones de correo electrónico. Cuando no se proporciona un valor de identificador de cadena para un registro insertado, el back-end genera un nuevo GUID.

Los valores de identificador de cadena proporcionan las siguientes ventajas:

+ Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta a la base de datos.
+ Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
+ Los valores de los identificadores se integran mejor con una lógica de aplicación.

Los valores de identificador de cadena son **OBLIGATORIOS** para poder utilizar la sincronización sin conexión.

##<a name="<a-name="updating"></a>how-to:-update-data-in-a-mobile-app"></a><a name="updating"></a>Actualización de los datos en una aplicación móvil

Para actualizar los datos de una tabla, pase el nuevo objeto al método **update()** .

    mToDoTable.update(item).get();

En este ejemplo, *item* es una referencia a una fila de la tabla *ToDoItem*, en la que se le han realizado algunos cambios.
Se actualiza la fila con el mismo campo **id** .

##<a name="<a-name="deleting"></a>how-to:-delete-data-in-a-mobile-app"></a><a name="deleting"></a>Eliminación de datos en una aplicación móvil

El código siguiente muestra cómo eliminar datos de una tabla especificando el objeto de datos.

    mToDoTable.delete(item);

También puede eliminar un elemento especificando el campo **id** de la fila que se va a eliminar.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="<a-name="lookup"></a>how-to:-look-up-a-specific-item"></a><a name="lookup"></a>Búsqueda de un elemento específico

Busque un elemento con un campo **id** concreto con el método **lookUp()**:

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="<a-name="untyped"></a>how-to:-work-with-untyped-data"></a><a name="untyped"></a>Trabajo con datos sin tipo

El modelo de programación sin tipo proporciona un control exacto de la serialización de JSON.  Existen algunos escenarios comunes donde recomendamos usar un modelo de programación sin tipo. Por ejemplo, si la tabla de back-end contiene muchas columnas y solo necesita hacer referencia a un subconjunto de ellas.  El modelo con tipo precisa definir todas las columnas de la tabla de aplicaciones móviles en clase de datos.  

La mayoría de las llamadas de API para obtener acceso a los datos son similares a las llamadas de programación con tipo. La diferencia principal es que en el modelo sin tipo se invocan métodos en el objeto **MobileServiceJsonTable**, en lugar del objeto **MobileServiceTable**.

### <a name="<a-name="json_instance"></a>how-to:-create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Creación de una instancia de tabla sin tipo

Como en el modelo con tipo, se empieza obteniendo una referencia de tabla, aunque en este caso se trate de un objeto **MobileServicesJsonTable** . Obtenga la referencia llamando al método **getTable** en una instancia del cliente:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Cuando haya creado una instancia de **MobileServiceJsonTable**, tendrá disponible prácticamente la misma API que con el modelo de programación con tipo. En algunos casos, los métodos toman un parámetro sin tipo en lugar de uno con tipo.

### <a name="<a-name="json_insert"></a>how-to:-insert-into-an-untyped-table"></a><a name="json_insert"></a>Inserción de una tabla sin tipo

El código siguiente muestra cómo realizar una inserción. El primer paso es crear un objeto [JsonObject][1], que forma parte de la biblioteca de [gson][3].

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Después, utilice **insert()** para insertar el objeto sin tipo en la tabla.

    mJsonToDoTable.insert(jsonItem).get();

Si necesita obtener el identificador del objeto insertado, use el método **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="<a-name="json_delete"></a>how-to:-delete-from-an-untyped-table"></a><a name="json_delete"></a>Eliminación de una tabla sin tipo

El código siguiente muestra cómo eliminar una instancia, en este caso, la misma instancia de un **JsonObject** que se creó en el ejemplo de *insert* anterior. El código es el mismo que con el caso con tipo, pero el método tiene una firma diferente, ya que hace referencia a un elemento **JsonObject**.

         mToDoTable.delete(jsonItem);

También puede eliminar una instancia directamente usando su identificador:

         mToDoTable.delete(ID);

### <a name="<a-name="json_get"></a>how-to:-return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Devolución de todas las filas de una tabla sin tipo

El código siguiente muestra cómo recuperar una tabla completa. Puesto que está utilizando una tabla de JSON, solamente puede recuperar de manera selectiva algunas de las columnas de la tabla.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

En el modelo con tipo se encuentra disponible el mismo conjunto de métodos de filtro y paginación que con el de sin tipo.

##<a name="<a-name="custom-api"></a>how-to:-call-a-custom-api"></a><a name="custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

Desde un cliente Android, llame al método **invokeApi** para llamar al punto de conexión de API personalizado. En el ejemplo siguiente se muestra cómo llamar a un punto de conexión de API denominado **completeAll**, que devuelve una clase de colección denominada **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

El método **invokeApi** se llama en el cliente, el cual envía una solicitud de POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores. Otras versiones de **invokeApi** le permiten enviar opcionalmente un objeto en el cuerpo de solicitud, especificar el método HTTP y enviar parámetros de consulta con la solicitud. También se proporcionan versiones sin tipo de **invokeApi** .

##<a name="<a-name="authentication"></a>how-to:-add-authentication-to-your-app"></a><a name="authentication"></a>Incorporación de autenticación a la aplicación

Los tutoriales ya describen detalladamente cómo agregar estas características.

Servicio de aplicaciones es compatible con la [autenticación de los usuarios de aplicaciones](app-service-mobile-android-get-started-users.md) mediante diversos proveedores de identidades externas: Facebook, Google, cuenta de Microsoft, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en el back-end.

Se admiten dos flujos de autenticación: un flujo de **servidor** y un flujo de **cliente**. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web de los proveedores de identidades.  No se requieren más SDK para implementar la autenticación de flujo de servidor. Este tipo de autenticación no proporciona una integración perfecta con el dispositivo móvil y solo se recomienda en escenarios de pruebas de concepto.

El flujo de cliente posibilita una mayor integración con funcionalidades específicas de dispositivos, como el inicio de sesión único, ya que se basa en SDK que proporciona el proveedor de identidades.  Por ejemplo, puede integrar el SDK de Facebook en la aplicación móvil.  El cliente móvil se coloca en la aplicación de Facebook y confirma el inicio de sesión antes de pasar a la aplicación móvil.

Hay que realizar cuatro pasos para habilitar la autenticación en su aplicación:

- Registre la aplicación para llevar a cabo la autenticación con un proveedor de identidades.
- Configure el back-end de App Service.
- Restrinja los permisos de tabla a solo los usuarios autenticados en el back-end de App Service.
- Agregar código de autenticación a su aplicación.

Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar el SID de un usuario autenticado para modificar las solicitudes.  Para obtener más información, consulte [Introducción a la autenticación] y la documentación de los procedimientos del SDK de servidor.

### <a name="<a-name="caching"></a>how-to:-add-authentication-code-to-your-app"></a><a name="caching"></a>Adición del código de autenticación a su aplicación

El código siguiente inicia el proceso de inicio de sesión del flujo de servidor mediante el proveedor de Google:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtenga el identificador del usuario que ha iniciado sesión desde una clase **MobileServiceUser** utilizando el método **getUserId**. Para obtener un ejemplo de cómo usar Futures para llamar a las API de inicio de sesión asincrónico, consulte [Agregar autenticación a su aplicación de Android].

### <a name="<a-name="caching"></a>how-to:-cache-authentication-tokens"></a><a name="caching"></a>Almacenamiento de tokens de autenticación en la memoria caché

El almacenamiento en caché de los tokens de autenticación requiere el almacenamiento del identificador de usuario y el token de autenticación localmente en el dispositivo. La próxima vez que se inicie la aplicación, compruebe la caché y, si estos valores están presentes, puede omitir el procedimiento de inicio de sesión y rehidratar el cliente con estos datos. No obstante, estos datos son confidenciales y deben almacenarse cifrados por seguridad en caso de que le roben el teléfono.

Puede ver un ejemplo completo de cómo almacenar tokens de autenticación en la memoria caché en la [sección Almacenamiento de tokens de autenticación en la memoria caché][7].

Si trata de utilizar un token caducado, recibirá como respuesta *401 unauthorized* . Puede controlar los errores de autenticación usando filtros.  Los filtros interceptan las solicitudes al back-end de App Service. El código de filtro probará la respuesta a un error 401, desencadenará el proceso de inicio de sesión y, luego, reanudará la solicitud que generó el 401. 

## <a name="<a-name="adal"></a>how-to:-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. La opción del inicio de sesión de flujo de cliente es, con frecuencia, preferible al uso de los métodos `loginAsync()` , ya que proporciona una experiencia UX más nativa y permite realizar más personalizaciones.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa.

2. Instale ADAL modificando el archivo build.gradle para incluir las siguientes definiciones:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Agregue el siguiente código a la aplicación y realice las siguientes sustituciones:

* Reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de Azure clásico].

* Reemplace **INSERT-RESOURCE-ID-HERE** por el Id. de cliente del back-end de la aplicación móvil. El Id. de cliente en la pestaña **Opciones avanzadas** de **Configuración de Azure Active Directory** en el portal.

* Reemplace **INSERT-CLIENT-ID-HERE** por el Id. de cliente que copió de la aplicación cliente nativa.

* Reemplace **INSERT-REDIRECT-URI-HERE** por el punto de conexión _/.auth/login/done_ del sitio, mediante el esquema HTTPS. Este valor debe ser similar a _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to:-add-push-notification-to-your-app"></a>Procedimiento: Incorporación de una notificación push a la aplicación

Puede [leer una introducción][6] en la que se describe la forma en que Microsoft Azure Notification Hubs admite una amplia variedad de notificaciones push.  En [este tutorial][5], cada vez que se inserta un registro, se envía una notificación push a todos los dispositivos.

## <a name="how-to:-add-offline-sync-to-your-app"></a>Incorporación de sincronización sin conexión a la aplicación

El tutorial de inicio rápido contiene código que implementa la sincronización sin conexión. Busque código que tenga comentarios como este en el prefijo:

    // Offline Sync

Al quitar el comentario de las siguientes líneas de código se puede implementar la sincronización sin conexión, y se puede agregar código similar a otro código de aplicaciones móviles.

##<a name="<a-name="customizing"></a>how-to:-customize-the-client"></a><a name="customizing"></a>Personalización del cliente

Hay varias maneras de personalizar el comportamiento predeterminado del cliente.

### <a name="<a-name="headers"></a>how-to:-customize-request-headers"></a><a name="headers"></a>Personalización de encabezados de solicitud

Configure una clase **ServiceFilter** para agregar un encabezado HTTP personalizado a cada solicitud:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="<a-name="serialization"></a>how-to:-customize-serialization"></a><a name="serialization"></a>Personalización de la serialización

El cliente supone que los nombres de tabla, los nombres de columna y los tipos de datos del back-end coinciden exactamente con los objetos de datos definidos en el cliente. Sin embargo, puede haber un sinnúmero de motivos por los que los nombres del servidor y el cliente no coinciden. En su situación, es posible que desee realizar los siguientes tipos de personalizaciones:

- Los nombres de columna usados en la tabla de App Service no coinciden con los nombres que usa en el cliente.
- Use una tabla de App Service que tenga un nombre diferente que la clase a la que se asigna en el cliente.
- Activar el uso automático de mayúsculas para las propiedades.
- Agregar propiedades complejas a un objeto.

### <a name="<a-name="columns"></a>how-to:-map-different-client-and-server-names"></a><a name="columns"></a>Asignación de nombres de servidor y cliente diferentes

Supongamos que su código de cliente Java usa nombres de tipo Java estándar para las propiedades del objeto **ToDoItem** , como los que se indican a continuación:

- mId
- mText
- mComplete
- mDuration

Serialice los nombres de cliente en nombres JSON que coincidan con los nombres de columna de la tabla **ToDoItem** del servidor. El siguiente código utiliza la biblioteca [gson][3] para anotar las propiedades:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="<a-name="table"></a>how-to:-map-different-table-names-between-the-client-and-the-backend"></a><a name="table"></a>Asignación de nombres de tabla diferentes entre el cliente y el back-end

Asigne el nombre de la tabla de cliente a un nombre de tabla de Mobile Services diferente mediante el uso de una de las invalidaciones del método [getTable()][4]:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="<a-name="conversions"></a>how-to:-automate-column-name-mappings"></a><a name="conversions"></a>Asignación automática de nombres de columna

Con la API de [gson][3] puede especificar una estrategia de conversión que se aplique a todas las columnas. La biblioteca de cliente Android utiliza la biblioteca [gson][3] en segundo plano para serializar los objetos de Java en datos JSON antes de que los datos se envíen a Azure App Service.  El siguiente código utiliza el método **setFieldNamingStrategy()** para establecer la estrategia. Este ejemplo eliminará el carácter inicial (una "m") y, después, pondrá el siguiente carácter en minúscula (en cada nombre de campo). Por ejemplo, convertiría "mld" en "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Este código debe ejecutarse antes de usar el objeto **MobileServiceClient**.

### <a name="<a-name="complex"></a>how-to:-store-an-object-or-array-property-into-a-table"></a><a name="complex"></a>Almacenamiento de una propiedad de objeto o matriz en una tabla

Hasta ahora, nuestros ejemplos de serialización han empleado tipos primitivos, como enteros y cadenas.  Los tipos primitivos se serializan fácilmente en JSON.  Si queremos agregar un objeto complejo que no se serialice automáticamente en JSON, hay que proporcionar el método de serialización de JSON.  Para ver un ejemplo de cómo proporcionar una serialización de JSON personalizada, consulte la entrada del blog relativa a la [personalización de la serialización con la biblioteca gson en el cliente Android de Mobile Services][2].

<!-- Anchors. -->

[¿Qué es Mobile Services?]: #what-is
[Conceptos]: #concepts
[Creación del cliente de Servicios móviles]: #create-client
[Creación de una referencia de tabla]: #instantiating
[La estructura de API]: #api
[Consulta de datos desde un servicio móvil]: #querying
[Devolver todos los elementos]: #showAll
[Filtro de datos devueltos]: #filtering
[Ordenar datos devueltos]: #sorting
[Devolver datos en páginas]: #paging
[Seleccionar columnas específicas]: #selecting
[Concatenación de métodos de consulta]: #chaining
[Enlace de datos a la interfaz de usuario]: #binding
[How to: Define the layout]: #layout
[Definición del adaptador]: #adapter
[Uso del adaptador]: #use-adapter
[Inserción de datos en un servicio móvil]: #inserting
[How to: update data in a mobile service]: #updating
[Eliminación de datos en un servicio móvil]: #deleting
[Búsqueda de un elemento específico]: #lookup
[Trabajo con datos sin tipo]: #untyped
[Autenticación de usuarios]: #authentication
[Almacenamiento de tokens de autenticación en la memoria caché]: #caching
[Gestión de errores]: #errors
[Diseño de pruebas en la unidad]: #tests
[Personalización del cliente]: #customizing
[Personalización de encabezados de solicitud]: #headers
[Personalización de serialización]: #serialization
[Next Steps]: #next-steps
[Configuración y requisitos previos]: #setup

<!-- Images. -->

<!-- URLs. -->
[Introducción a Azure Mobile Apps]: app-service-mobile-android-get-started.md
[Códigos de control ASCII C0 y C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[SDK de Mobile Services para Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portal de Azure]: https://portal.azure.com
[Introducción a la autenticación]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html


<!--HONumber=Oct16_HO2-->


