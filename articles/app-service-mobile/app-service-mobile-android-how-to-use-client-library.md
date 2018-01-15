---
title: Uso del SDK de Azure Mobile Apps para Android | Microsoft Docs
description: Uso del SDK de Azure Mobile Apps para Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: f04f3fc7d2ff2e01baa78571b2ba267f8e4905c6
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Uso del SDK de Azure Mobile Apps para Android

En esta guía se muestra cómo utilizar el SDK cliente de Android para Mobile Apps con el objetivo de implementar escenarios comunes, como los siguientes:

* Consultas de datos (inserción, actualización y eliminación)
* Autenticación
* Control de errores
* Personalización del cliente

Esta guía se centra en el SDK de Android del cliente.  Para más información sobre los SDK del lado servidor para Mobile Apps, consulte [Trabajar con el SDK del back-end de .NET][10] o [Cómo usar el SDK del back-end de Node.js][11].

## <a name="reference-documentation"></a>Documentación de referencia

Puede encontrar la [referencia de API de Javadocs][12] para la biblioteca de cliente Android en GitHub.

## <a name="supported-platforms"></a>Plataformas compatibles

El SDK de Azure Mobile Apps para Android admite los niveles de API del 19 al 24 (de KitKat a Nougat) para teléfonos y tabletas.  En concreto, la recopilación de credenciales para la autenticación se basa en un marco web común.  La autenticación de flujo de servidor no funciona con dispositivos pequeños, por ejemplo, relojes.

## <a name="setup-and-prerequisites"></a>Configuración y requisitos previos

Complete el tutorial de [inicio rápido de Mobile Apps](app-service-mobile-android-get-started.md) .  Esta tarea garantiza que se cumplan todos los requisitos previos del desarrollo de aplicaciones de Mobile Apps de Azure.  El tutorial de inicio rápido ayuda a configurar la cuenta y a crear el primer back-end de aplicación móvil.

Si decide no completar el tutorial de inicio rápido, realice las tareas siguientes:

* [Cree un nuevo back-end de aplicación móvil][13] para usarlo con su aplicación Android.
* En Android Studio, [actualice los archivos de compilación de Gradle](#gradle-build).
* [Habilite permisos de Internet](#enable-internet).

### <a name="gradle-build"></a>Actualización del archivo de compilación de Gradle

Cambie ambos archivos **build.gradle** :

1. Agregue este código al archivo *build.gradle* del nivel **Project** dentro de la etiqueta *buildscript*:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Agregue este código al archivo *build.gradle* del nivel **Module app** dentro de la etiqueta *dependencies*:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Actualmente, la versión más reciente es la 3.4.0. Las versiones compatibles se indican [en Bintray][14].

### <a name="enable-internet"></a>Habilitación de permisos de Internet

Para obtener acceso a Azure, la aplicación debe tener habilitado el permiso de INTERNET. Si aún no está habilitado, agregue la siguiente línea de código a su archivo **AndroidManifest.xml** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Creación de una conexión de cliente

Azure Mobile Apps proporciona cuatro funciones para sus aplicaciones móviles:

* Acceso a datos y sincronización sin conexión con un servicio de Azure Mobile Apps.
* Llamada a API personalizadas escritas con el SDK de servidor de Azure Mobile Apps.
* Autenticación con Autenticación y autorización de Azure App Service.
* Registro de notificaciones push con Notification Hubs.

Cada una de estas funciones requiere en primer lugar que se cree un objeto `MobileServiceClient`.  Solo se debe crear un objeto `MobileServiceClient` dentro de su cliente móvil (es decir, debe tener un patrón singleton).  Para crear un objeto `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` es una cadena o un objeto de URL que apunta a su back-end móvil.  Si va a usar Azure App Service para hospedar su back-end móvil, asegúrese de utilizar la versión `https://` segura de la URL.

El cliente también necesita acceso a la actividad o contexto; en este ejemplo, el parámetro `this`.  La construcción de MobileServiceClient debe tener lugar dentro del método `onCreate()` de la actividad a la que se hace referencia en el archivo `AndroidManifest.xml`.

Como procedimiento recomendado, debe abstraer la comunicación del servidor en su propia clase (patrón singleton).  En este caso, debe pasar la actividad dentro del constructor para configurar el servicio de manera adecuada.  Por ejemplo: 

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Ahora puede llamar a `AzureServiceAdapter.Initialize(this);` en el método `onCreate()` de su actividad principal.  Los otros métodos que necesitan acceso al cliente usan `AzureServiceAdapter.getInstance();` para obtener una referencia al adaptador de servicio.

## <a name="data-operations"></a>Operaciones de datos

La base del SDK de Azure Mobile Apps es proporcionar acceso a los datos almacenados dentro de SQL Azure en el back-end de Mobile App.  Se puede acceder a estos datos mediante clases fuertemente tipadas (preferidas) o consultas sin tipo (no se recomienda).  La mayor parte de esta sección trata del uso de clases fuertemente tipadas.

### <a name="define-client-data-classes"></a>Definición de clases de datos de cliente

Para acceder a los datos de tablas de SQL Azure, defina las clases de datos cliente que corresponden a las tablas del back-end de aplicación móvil. En los ejemplos de este tema se usa una tabla denominada **MyDataTable**, que tiene las siguientes columnas:

* id
* text
* complete

El objeto de cliente con tipo correspondiente reside en un archivo denominado **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Agregue los métodos captador y establecedor para cada campo que agregue.  Si la tabla de SQL Azure contuviera más columnas, agregaría los campos correspondientes a esta clase.  Por ejemplo, si el DTO (objeto de transferencia de datos) tuviera una columna Prioridad de tipo entero, podría agregar este campo junto con sus métodos de captador y establecedor:

```java
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
```

Para más información sobre cómo crear tablas adicionales en el back-end de Mobile Apps, consulte [Cómo definir un controlador de tabla][15] (back-end de. NET) o [Definición de tablas con un esquema dinámico][16] (back-end de Node.js).

Una tabla de back-end de Azure Mobile Apps define cinco campos especiales, cuatro de los cuales están disponibles para los clientes:

* `String id`: el identificador único global del registro.  Como procedimiento recomendado, convierta el identificador en la representación de cadena de un objeto [UUID][17].
* `DateTimeOffset updatedAt`: la fecha y hora de la última actualización.  El campo updatedAt lo establece el servidor; nunca lo debe establecer el código de cliente.
* `DateTimeOffset createdAt`: la fecha y hora en que se creó el objeto.  El campo createdAt lo establece el servidor; nunca lo debe establecer el código de cliente.
* `byte[] version`: normalmente se representa como una cadena; la versión la establece también el servidor.
* `boolean deleted`: indica que el registro se ha eliminado pero no se ha purgado aún.  No use `deleted` como propiedad en la clase.

El campo `id` es obligatorio.  Los campos `updatedAt` y `version` se usan para la sincronización sin conexión (para la sincronización incremental y la resolución de conflictos, respectivamente).  El campo `createdAt` es un campo de referencia y el cliente no lo utiliza.  Los nombres son nombres "a través de la red" de las propiedades y no son ajustables.  Sin embargo, puede crear una asignación entre el objeto y los nombres "a través de la red" con la biblioteca [gson][3].  Por ejemplo: 

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getUpdatedAt() { return mCreatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Creación de una referencia de tabla

Para obtener acceso a una tabla, cree primero un objeto [MobileServiceTable][8] mediante una llamada al método **getTable** en [MobileServiceClient][9].  Este método tiene dos sobrecargas:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

En el código siguiente, **mClient** es una referencia al objeto MobileServiceClient.  La primera sobrecarga se utiliza cuando coinciden el nombre de clase y el nombre de tabla, y es la que se emplea en el tutorial de inicio rápido:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

La segunda sobrecarga se utiliza cuando el nombre de tabla es diferente del nombre de clase: el primer parámetro es el nombre de tabla.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Consulta de una tabla de back-end

En primer lugar, obtenga una referencia de tabla.  A continuación, ejecute una consulta en la referencia de tabla.  Una consulta es cualquier combinación de:

* Una `.where()` [cláusula de filtro](#filtering).
* Una `.orderBy()` [cláusula de ordenación](#sorting).
* Una `.select()` [cláusula de selección de campos](#selection).
* Una cláusula `.skip()` y `.top()` para [resultados paginados](#paging).

Las cláusulas deben presentarse en el orden anterior.

### <a name="filter"></a>Filtrado de los resultados

La forma general de una consulta es:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

En el ejemplo anterior se devuelven todos los resultados (hasta el tamaño máximo de página establecido por el servidor).  El método `.execute()` ejecuta la consulta en el back-end.  La consulta se convierte en una consulta [OData v3][19] antes de la transmisión al back.end de Mobile Apps.  A la recepción, el back-end de Mobile Apps convierte la consulta en una instrucción SQL antes de ejecutarla en la instancia de SQL Azure.  Dado que la actividad de red tarda algún tiempo, el método `.execute()` devuelve [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrado de datos devueltos

La ejecución de la consulta siguiente devuelve todos los elementos de la tabla **ToDoItem** en los que **complete** es igual a **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** es la referencia a la tabla de servicio móvil que se ha creado previamente.

Se define un filtro mediante la llamada al método **where** en la referencia de tabla. Al método **where** le sigue un método **field** y a este le sigue un método que especifica el predicado lógico. Los posibles métodos de predicado incluyen **eq** (igual a), **ne** (no igual a), **gt** (mayor que), **ge** (mayor o igual que), **lt** (menor que) o **le** (menor o igual que). Estos métodos permiten comparar campos de número y cadena con valores específicos.

Puede filtrar por fechas. Los métodos siguientes permiten comparar el campo de fecha entero o partes de la fecha: **year**, **month**, **day**, **hour**, **minute** y **second**. El ejemplo siguiente agrega un filtro para los elementos cuyo valor *due date* sea igual a 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Los métodos siguientes admiten filtros complejos en campos de cadena: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** y **length**. El ejemplo siguiente filtra las filas de tabla donde la columna *text* empieza por PRI0.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

En los campos de número se admiten los siguientes métodos de operador: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** y **round**. El ejemplo siguiente filtra las filas de tabla en las que **duration** es un número par.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Puede combinar predicados con estos métodos lógicos: **and**, **or** y **not**. En el ejemplo siguiente se combinan dos de los ejemplos anteriores.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Agrupe y anide los operadores lógicos:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Para obtener más información y ver ejemplos de filtrado, consulte [Exploring the richness of the Android client query model ][20] (Exploración de la riqueza del modelo de consulta del cliente Android).

### <a name="sorting"></a>Ordenación de datos devueltos

El código siguiente devuelve todos los elementos de una tabla de **ToDoItems** cuyo campo *text* sigue un orden ascendente. *mToDoTable* es la referencia a la tabla del back-end que ha creado anteriormente:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

El primer parámetro del método **orderBy** es una cadena igual al nombre del campo por el que realizar la ordenación. El segundo parámetro usa la enumeración **QueryOrder** para especificar si la ordenación será ascendente o descendente.  Si filtra con el método ***where***, este debe invocarse ***antes*** del método ***orderBy***.

### <a name="selection"></a>Selección de columnas específicas

El código siguiente ilustra cómo devolver todos los elementos de una tabla de **ToDoItems**, pero solo muestra los campos **complete** y **text**. **mToDoTable** es la referencia a la tabla de back-end que se ha creado previamente.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Los parámetros para la función de selección son los nombres de las cadenas de las columnas de la tabla que desea devolver.  El método **select** debe ir después de métodos como **where** y **orderBy**. Puede ir seguido de métodos de paginación como **skip** y **top**.

### <a name="paging"></a>Devolución de datos en páginas

Los datos **SIEMPRE** se devuelven en páginas.  El servidor establece el número máximo de registros devueltos.  Si el cliente solicita más registros, el servidor devuelve entonces el número máximo de registros.  De forma predeterminada, el tamaño máximo de página en el servidor es de 50 registros.

El primer ejemplo muestra cómo seleccionar los cinco primeros elementos de una tabla. La consulta devuelve los elementos de una tabla de **ToDoItems**. **mToDoTable** es la referencia a la tabla del back-end que ha creado anteriormente:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Aquí se muestra una consulta que omite los cinco primeros elementos y que, a continuación, devuelve los cinco siguientes:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Si desea obtener todos los registros en una tabla, implemente código para recorrer en iteración todas las páginas:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Una solicitud de todos los registros con este método crea dos solicitudes como mínimo al back-end de Mobile Apps.

> [!TIP]
> La elección del tamaño de página adecuado es un equilibrio entre el uso de memoria mientras tiene lugar la solicitud, el uso de ancho de banda y el retraso en la recepción completa de los datos.  El valor predeterminado (50 registros) es adecuado para todos los dispositivos.  Si usa exclusivamente dispositivos de memoria más grande, aumente hasta 500.  Hemos encontrado que si se aumenta el tamaño de página por encima de 500 registros da lugar a retrasos inaceptables y problemas de memoria graves.

### <a name="chaining"></a>Concatenación de métodos de consulta

Se pueden concatenar los métodos usados en la consulta de tablas de back-end. Gracias al encadenamiento de consultas, se pueden seleccionar columnas específicas de filas filtradas que se ordenan y paginan. Puede crear filtros lógicos complejos.  Cada método de consulta devuelve un objeto Query. Para finalizar las series de métodos y ejecutar la consulta, llame al método **execute** . Por ejemplo: 

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Los métodos de consulta encadenadas se deben ordenar de la siguiente forma:

1. Métodos de filtro (**where**)
2. Métodos de ordenación (**orderBy**)
3. Métodos de selección (**select**)
4. Métodos de paginación (**skip** y **top**)

## <a name="binding"></a>Enlace de datos a la interfaz de usuario

El enlace de datos implica tres componentes:

* El origen de datos
* El diseño de la pantalla
* El adaptador que une a ambos

En nuestro código de muestra, devolvemos los datos de la tabla SQL Azure de Mobile Apps **ToDoItem** en una matriz. Esta actividad es uno de los patrones más comunes para las aplicaciones de datos.  Las consultas en bases de datos normalmente devuelven una serie de filas que el cliente recibe en una lista o una matriz. En este ejemplo, la matriz es el origen de datos.  El código especifica un diseño de pantalla que define la vista de los datos que aparecerán en el dispositivo.  Los dos están vinculados mediante un adaptador, que en este código es una extensión de la clase **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="layout"></a>Definición del diseño

El diseño lo definen varios fragmentos de código XML. Dado el diseño existente, el código siguiente representa el elemento **ListView** que queremos rellenar con nuestros datos de servidor.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

En el código anterior, el atributo *listitem* especifica el identificador del diseño de una fila concreta de la lista. Este código especifica una casilla de verificación y su texto asociado, y crea una instancia de esta para cada elemento de la lista. Este diseño no muestra el campo **id** y un diseño más complejo especificaría campos adicionales en la pantalla. Este código está en el archivo **row_list_to_do.xml**.

```java
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
```

#### <a name="adapter"></a>Definición del adaptador
Como el origen de datos de nuestra vista es una matriz de **ToDoItem**, se realiza una subclase en nuestro adaptador desde una clase **ArrayAdapter&lt;ToDoItem&gt;**. Esta subclase producirá una vista para cada **ToDoItem** que use el diseño de **row_list_to_do**.  En el código, definimos la siguiente clase que es una extensión de la clase **ArrayAdapter&lt;E&gt;**:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Reemplace el método **getView** de los adaptadores. Por ejemplo: 

```
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
```

Creamos una instancia de esta clase en nuestra actividad de la forma siguiente:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

El segundo parámetro para el constructor ToDoItemAdapter es una referencia al diseño. Ahora podemos crear una instancia del elemento **ListView** y asignar el adaptador a **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Uso del adaptador para enlazar con la interfaz de usuario

Ahora ya puede usar el enlace de datos. El código siguiente muestra cómo obtener los elementos de la tabla y rellena el adaptador local con los elementos devueltos.

```java
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
```

Llame al adaptador en cualquier momento para modificar la tabla **ToDoItem** . Como las modificaciones se realizan de registro en registro, se ocupará de una sola fila en lugar de una serie de ellas. Al insertar un elemento, se llama al método **add** del adaptador; al eliminarlo, al método **remove**.

Puede encontrar un ejemplo completo en el [proyecto de inicio rápido de Android][21].

## <a name="inserting"></a>Inserción de datos en el back-end

Cree instancias de la clase *ToDoItem* y configure sus propiedades.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Después, utilice **insert()** insertar un objeto:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

La entidad devuelta coincide con los datos insertados en la tabla de back-end, incluido el identificador y los otros valores establecidos en el back-end (como los campos `createdAt`, `updatedAt` y `version`).

Las tablas de Mobile Apps requieren una columna de clave principal denominada " **id**". Esta columna debe ser una cadena. El valor predeterminado de la columna de identificador es un GUID.  Puede proporcionar otros valores únicos, como nombres de usuario o direcciones de correo electrónico. Cuando no se proporciona un valor de identificador de cadena para un registro insertado, el back-end genera un nuevo GUID.

Los valores de identificador de cadena proporcionan las siguientes ventajas:

* Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta a la base de datos.
* Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
* Los valores de los identificadores se integran mejor con una lógica de aplicación.

Los valores de identificador de cadena son **OBLIGATORIOS** para poder utilizar la sincronización sin conexión.  No se puede cambiar un identificador una vez que se almacena en la base de datos de back-end.

## <a name="updating"></a>Actualización de los datos en una aplicación móvil

Para actualizar los datos de una tabla, pase el nuevo objeto al método **update()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

En este ejemplo, *item* es una referencia a una fila de la tabla *ToDoItem*, en la que se le han realizado algunos cambios.  Se actualiza la fila con el mismo campo **id** .

## <a name="deleting"></a>Eliminación de datos en una aplicación móvil

El código siguiente muestra cómo eliminar datos de una tabla especificando el objeto de datos.

```java
mToDoTable
    .delete(item);
```

También puede eliminar un elemento especificando el campo **id** de la fila que se va a eliminar.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Búsqueda de un elemento específico por el identificador

Busque un elemento con un campo **id** concreto con el método **lookUp()**:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Trabajo con datos sin tipo

El modelo de programación sin tipo proporciona un control exacto de la serialización de JSON.  Existen algunos escenarios comunes donde recomendamos usar un modelo de programación sin tipo. Por ejemplo, si la tabla de back-end contiene muchas columnas y solo necesita hacer referencia a un subconjunto de ellas.  El modelo con tipo precisa definir todas las columnas especificadas en el back-end de Mobile Apps de su clase de datos.  La mayoría de las llamadas de API para obtener acceso a los datos son similares a las llamadas de programación con tipo. La diferencia principal es que en el modelo sin tipo se invocan métodos en el objeto **MobileServiceJsonTable**, en lugar del objeto **MobileServiceTable**.

### <a name="json_instance"></a>Creación de una instancia de tabla sin tipo

Como en el modelo con tipo, se empieza obteniendo una referencia de tabla, aunque en este caso se trate de un objeto **MobileServicesJsonTable** . Obtenga la referencia llamando al método **getTable** en una instancia del cliente:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Cuando haya creado una instancia de **MobileServiceJsonTable**, tendrá disponible prácticamente la misma API que con el modelo de programación con tipo. En algunos casos, los métodos toman un parámetro sin tipo en lugar de uno con tipo.

### <a name="json_insert"></a>Inserción de una tabla sin tipo
El código siguiente muestra cómo realizar una inserción. El primer paso es crear un [JsonObject][1], que forma parte de la biblioteca de [gson][3].

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Después, utilice **insert()** para insertar el objeto sin tipo en la tabla.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Si necesita obtener el identificador del objeto insertado, use el método **getAsJsonPrimitive()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Eliminación de una tabla sin tipo
El código siguiente muestra cómo eliminar una instancia, en este caso, la misma instancia de un **JsonObject** que se creó en el ejemplo de *insert* anterior. El código es el mismo que con el caso con tipo, pero el método tiene una firma diferente, ya que hace referencia a un elemento **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

También puede eliminar una instancia directamente usando su identificador:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Devolución de todas las filas de una tabla sin tipo
El código siguiente muestra cómo recuperar una tabla completa. Puesto que está utilizando una tabla de JSON, solamente puede recuperar de manera selectiva algunas de las columnas de la tabla.

```java
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
```

En el modelo con tipo se encuentra disponible el mismo conjunto de métodos de filtrado y paginación que con el modelo sin tipo.

## <a name="offline-sync"></a>Implementación de la sincronización sin conexión

El SDK de cliente de Azure Mobile Apps también implementa la sincronización de datos sin conexión mediante una base de datos SQLite que almacena una copia de los datos de servidor de manera local.  Las operaciones realizadas en una tabla sin conexión no necesitan conectividad móvil para funcionar.  La sincronización sin conexión favorece la resistencia y el rendimiento; a cambio, la resolución de conflictos conlleva una lógica más compleja.  El SDK de cliente de Azure Mobile Apps implementa las siguientes características:

* Sincronización incremental: solo se descargan registros nuevos y actualizados, lo que ahorra ancho de banda y consumo de memoria.
* Simultaneidad optimista: se supone que las operaciones se realizan correctamente.  La resolución de conflictos se aplaza hasta que se realizan actualizaciones en el servidor.
* Resolución de conflictos: el SDK detecta cuándo se ha realizado un cambio conflictivo en el servidor y proporciona enlaces para avisar al usuario.
* Eliminación temporal: los registros eliminados se marcan como eliminados, lo que permite que otros dispositivos actualicen su caché sin conexión.

### <a name="initialize-offline-sync"></a>Inicialización de la sincronización sin conexión

Cada tabla sin conexión se debe definir en la caché sin conexión antes de su uso.  Normalmente, la definición de la tabla se realiza inmediatamente después de la creación del cliente:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obtención de una referencia a la tabla de caché sin conexión

Para una tabla en línea, use `.getTable()`.  Para una tabla sin conexión, use `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Todos los métodos que están disponibles para tablas en línea (incluido el filtrado, la ordenación, la paginación, la inserción de datos, la actualización de datos y la eliminación de datos) funcionan igual de bien en tablas en línea que en tablas sin conexión.

### <a name="synchronize-the-local-offline-cache"></a>Sincronización de la caché sin conexión local

La sincronización tiene lugar dentro del control de la aplicación.  A continuación se muestra un método de sincronización de ejemplo:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Si se proporciona un nombre de consulta al método `.pull(query, queryname)`, se usa la sincronización incremental para devolver solo los registros que se han creado o cambiado desde la última extracción realizada correctamente.

### <a name="handle-conflicts-during-offline-synchronization"></a>Control de los conflictos durante la sincronización sin conexión

Si tiene lugar un conflicto durante una operación `.push()`, se produce una excepción `MobileServiceConflictException`.   El elemento emitido por el servidor se inserta en la excepción y se puede recuperar mediante `.getItem()` en la excepción.  Para ajustar la inserción, llame a los siguientes elementos del objeto MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Cuando todos los conflictos estén marcados como desea, llame de nuevo a `.push()` para resolverlos.

## <a name="custom-api"></a>Llamada a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

Desde un cliente Android, llame al método **invokeApi** para llamar al punto de conexión de API personalizado. En el ejemplo siguiente se muestra cómo llamar a un punto de conexión de API denominado **completeAll**, que devuelve una clase de colección denominada **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
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
```

El método **invokeApi** se llama en el cliente, el cual envía una solicitud de POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores. Otras versiones de **invokeApi** le permiten enviar opcionalmente un objeto en el cuerpo de solicitud, especificar el método HTTP y enviar parámetros de consulta con la solicitud. También se proporcionan versiones sin tipo de **invokeApi** .

## <a name="authentication"></a>Adición de autenticación a la aplicación

Los tutoriales ya describen detalladamente cómo agregar estas características.

App Service admite la [autenticación de los usuarios de aplicaciones](app-service-mobile-android-get-started-users.md) mediante diversos proveedores de identidades externos: Facebook, Google, cuenta de Microsoft, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en el back-end.

Se admiten dos flujos de autenticación: un flujo de **servidor** y un flujo de **cliente**. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web de los proveedores de identidades.  No se requieren más SDK para implementar la autenticación de flujo de servidor. Este tipo de autenticación no proporciona una integración perfecta con el dispositivo móvil y solo se recomienda en escenarios de pruebas de concepto.

El flujo de cliente posibilita una mayor integración con funcionalidades específicas de dispositivos, como el inicio de sesión único, ya que se basa en SDK que proporciona el proveedor de identidades.  Por ejemplo, puede integrar el SDK de Facebook en la aplicación móvil.  El cliente móvil se coloca en la aplicación de Facebook y confirma el inicio de sesión antes de pasar a la aplicación móvil.

Hay que realizar cuatro pasos para habilitar la autenticación en su aplicación:

* Registre la aplicación para llevar a cabo la autenticación con un proveedor de identidades.
* Configure el back-end de App Service.
* Restrinja los permisos de tabla a solo los usuarios autenticados en el back-end de App Service.
* Agregar código de autenticación a su aplicación.

Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar el SID de un usuario autenticado para modificar las solicitudes.  Para obtener más información, consulte [Introducción a la autenticación] y la documentación de los procedimientos del SDK de servidor.

### <a name="caching"></a>Autenticación: flujo de servidor

El código siguiente inicia un proceso de inicio de sesión del flujo de servidor mediante el proveedor de Google:  Hace falta configuración adicional debido a los requisitos de seguridad del proveedor de Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Además, agregue el siguiente método a la clase principal Activity:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

El valor de `GOOGLE_LOGIN_REQUEST_CODE` definido en la actividad principal se usa para el método `login()` y dentro del método `onActivityResult()`.  Puede elegir cualquier número único, siempre y cuando el mismo número se use dentro del método `login()` y del método `onActivityResult()`.  Si abstrae el código de cliente en un adaptador de servicio (como se mostró anteriormente), debe llamar a los métodos adecuados en el adaptador de servicio.

También debe configurar el proyecto para customtabs.  En primer lugar, especifique una dirección URL de redireccionamiento.  Agregue el siguiente fragmento de código a `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Agregue **redirectUriScheme** al archivo `build.gradle` de su aplicación:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Por último, agregue `com.android.support:customtabs:23.0.1` a la lista de dependencias del archivo `build.gradle`:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Obtenga el identificador del usuario que ha iniciado sesión desde una clase **MobileServiceUser** utilizando el método **getUserId**. Para obtener un ejemplo de cómo usar Futures para llamar a las API de inicio de sesión asincrónico, consulte [Introducción a la autenticación].

> [!WARNING]
> El esquema de dirección URL mencionado distingue mayúsculas de minúsculas.  Asegúrese de que todas las apariciones de `{url_scheme_of_you_app}` coincidan en las mayúsculas y minúsculas.

### <a name="caching"></a>Almacenamiento en caché de tokens de autenticación

El almacenamiento en caché de los tokens de autenticación requiere el almacenamiento del identificador de usuario y el token de autenticación localmente en el dispositivo. La próxima vez que se inicie la aplicación, compruebe la caché y, si estos valores están presentes, puede omitir el procedimiento de inicio de sesión y rehidratar el cliente con estos datos. No obstante, estos datos son confidenciales y deben almacenarse cifrados por seguridad en caso de que le roben el teléfono.  Puede ver un ejemplo completo de cómo almacenar tokens de autenticación en la memoria caché en la [sección Almacenamiento de tokens de autenticación en la caché][7].

Si trata de utilizar un token caducado, recibirá como respuesta *401 unauthorized* . Puede controlar los errores de autenticación usando filtros.  Los filtros interceptan las solicitudes al back-end de App Service. El código de filtro probará la respuesta a un error 401, desencadenará el proceso de inicio de sesión y, luego, reanudará la solicitud que generó el 401.

### <a name="refresh"></a>Uso de tokens de actualización

El token devuelto por la característica Autenticación y autorización de Azure App Service tiene una duración definida de una hora.  Después de este período, es necesario volver a autenticar al usuario.  Si usa un token de larga duración que haya recibido mediante la autenticación de flujo de cliente, entonces puede volver a autenticar con Autenticación y autorización de Azure App Service usando el mismo token.  Se genera otro token de Azure App Service con una nueva duración.

También puede registrar el proveedor para que use tokens de actualización.  Un token de actualización no está siempre disponible.  Se requiere configuración adicional:

* Para **Azure Active Directory**, configure un secreto de cliente para la aplicación de Azure Active Directory.  Especifique el secreto del cliente en Azure App Service al configurar la autenticación de Azure Active Directory.  Al llamar a `.login()`, pase `response_type=code id_token` como parámetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **Google**, pase `access_type=offline` como parámetro:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Para **Cuenta de Microsoft**, seleccione el ámbito `wl.offline_access`.

Para actualizar un token, llame a `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Como procedimiento recomendado, cree un filtro que detecte una respuesta 401 del servidor y que intente actualizar el token de usuario.

## <a name="log-in-with-client-flow-authentication"></a>Inicio de sesión con la autenticación de flujo de cliente

El proceso general para iniciar sesión con la autenticación de flujo de cliente es el siguiente:

* Configure Autenticación y autorización de Azure App Service como haría con la autenticación de flujo de servidor.
* Integre el SDK del proveedor de autenticación para que la autenticación genere un token de acceso.
* Llame al método `.login()` de la manera siguiente:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
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
    ```

Reemplace el método `onSuccess()` por cualquier código que desee usar en un inicio de sesión correcto.  La cadena `{provider}` es un proveedor válido: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount** o **twitter**.  Si ha implementado autenticación personalizada, también puede usar la etiqueta de proveedor de autenticación personalizada.

### <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory (ADAL)

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. La opción del inicio de sesión de flujo de cliente es, con frecuencia, preferible al uso de los métodos `loginAsync()` , ya que proporciona una experiencia UX más nativa y permite realizar más personalizaciones.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación de App Service para usar el inicio de sesión de Azure Active Directory][22]. Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa.
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

1. Agregue el siguiente código a la aplicación y realice las siguientes sustituciones:

* Reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación. El formato debería ser https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Reemplace **INSERT-RESOURCE-ID-HERE** por el Id. de cliente del back-end de la aplicación móvil. El Id. de cliente en la pestaña **Opciones avanzadas** de **Configuración de Azure Active Directory** en el portal.
* Reemplace **INSERT-CLIENT-ID-HERE** por el Id. de cliente que copió de la aplicación cliente nativa.
* Reemplace **INSERT-REDIRECT-URI-HERE** por el punto de conexión */.auth/login/done* del sitio, mediante el esquema HTTPS. Este valor debe ser similar a *https://contoso.azurewebsites.net/.auth/login/done*.

```java
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
```

## <a name="filters"></a>Ajuste de la comunicación cliente-servidor

La conexión de cliente es normalmente una conexión HTTP básica que usa la biblioteca HTTP subyacente que se suministra con el SDK de Android.  Hay varios motivos por los que desearía cambiar esta conexión:

* Desea usar una biblioteca HTTP alternativa para ajustar los tiempos de espera.
* Desea proporcionar una barra de progreso.
* Desea agregar un encabezado personalizado para admitir la funcionalidad de administración de API.
* Desea interceptar una respuesta errónea para que pueda implementar la reautenticación.
* Desea registrar las solicitudes de back-end a un servicio de análisis.

### <a name="using-an-alternate-http-library"></a>Uso de una biblioteca HTTP alternativa

Llame al método `.setAndroidHttpClientFactory()` inmediatamente después de crear la referencia de cliente.  Por ejemplo, para establecer el tiempo de espera de conexión en 60 segundos (en lugar del valor predeterminado de 10 segundos):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementación de un filtro de progreso

Puede implementar una intersección de todas las solicitudes mediante la implementación de `ServiceFilter`.  Por ejemplo, a continuación se actualiza una barra de progreso creada previamente:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Puede adjuntar este filtro al cliente de la manera siguiente:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personalización de los encabezados de solicitud

Use el elemento siguiente `ServiceFilter` y adjunte el filtro de la misma manera que `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configuración de serialización automática

Puede especificar una estrategia de conversión que se aplique a todas las columnas utilizando la API de [gson][3]. La biblioteca de cliente Android utiliza la biblioteca [gson][3] en segundo plano para serializar los objetos de Java en datos JSON, que se envían a Azure App Service.  El siguiente código utiliza el método **setFieldNamingStrategy()** para establecer la estrategia. Este ejemplo eliminará el carácter inicial (una "m") y, después, pondrá el siguiente carácter en minúscula (en cada nombre de campo). Por ejemplo, convertiría "mld" en "id".  Implemente una estrategia de conversión para reducir la necesidad de anotaciones `SerializedName()` en la mayoría de los campos.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Este código debe ejecutarse antes de crear una referencia de cliente móvil mediante **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
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
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
