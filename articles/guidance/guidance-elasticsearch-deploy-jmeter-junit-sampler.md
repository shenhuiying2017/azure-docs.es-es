<properties
   pageTitle="Creación e implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch | Microsoft Azure"
   description="Cómo utilizar una muestra de JUnit para generar y cargar datos en un clúster Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>
   
# Creación e implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch

Este artículo [forma parte de una serie](guidance-elasticsearch-introduction.md).

Este documento describe cómo crear y utilizar una muestra de JUnit que pueda generar y cargar datos en un clúster de Elasticsearch como parte de un plan de pruebas de JMeter. Este planteamiento proporciona un enfoque muy flexible para pruebas de carga que pueden generar grandes cantidades de datos de prueba sin depender de archivos de datos externos.

> [AZURE.NOTE] Las pruebas de carga que se usan para evaluar el rendimiento de la ingesta de datos que se describen en el documento Maximizing Data Ingestion Performance with Elasticsearch (Maximización del rendimiento de ingestión de datos con Elasticsearch) se construyeron siguiendo este patrón. Los detalles del código JUnit se describen en el documento.

Para probar el rendimiento de la ingestión de datos, el código JUnit se desarrolló usando Eclipse (Mars) y las dependencias se resolvieron con Maven. Los procedimientos siguientes describen el proceso paso a paso para instalar Eclipse, configurar Maven, crear una prueba de JUnit e implementar esta prueba como una muestra de JUnit Request en una prueba de JMeter.

> [AZURE.NOTE] Para obtener información detallada sobre la estructura y la configuración del entorno de prueba, consulte el documento sobre la creación de un entorno de pruebas de rendimiento para Elasticsearch.

## Instalación de los requisitos previos

Necesitará el entorno [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp) en el equipo de desarrollo.
También tendrá que instalar el [IDE de Eclipse para desarrolladores de Java](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE).

> [AZURE.NOTE] Si está usando la máquina virtual maestra de JMeter descrita en la sección [Configuring the Performance Testing Environment (Configuración del entorno de pruebas de rendimiento)](#overview) como entorno de desarrollo, descargue la versión de Windows de 32 bits del instalador de Eclipse.

## Creación de un proyecto de prueba de JUnit para la prueba de carga de Elasticsearch

1.  Inicie el IDE de Eclipse si aún no está en ejecución y cierre la página *principal*.

2.  En el menú *File (Archivo)*, haga clic en *New (Nuevo)* y luego en *Java Project (Proyecto de Java)*.

![](./media/guidance-elasticsearch-jmeter-deploy7.png)

3.  En la ventana *New Java Project (Nuevo proyecto de Java)*, escriba un nombre de proyecto, seleccione *Use default JRE (Usar JRE predeterminado)* y, a continuación, haga clic en *Finish (Finalizar)*.

![](./media/guidance-elasticsearch-jmeter-deploy8.png)

4.  En la ventana *Package Explorer (Explorador de paquetes)*, expanda el nodo con el nombre de su proyecto. Compruebe que contiene una carpeta denominada *src* y una referencia al JRE especificado.

![](./media/guidance-elasticsearch-jmeter-deploy9.png)

5.  Haga clic con el botón derecho en la carpeta *src* carpeta, haga clic en *New (Nuevo)* y, a continuación, haga clic en *JUnit Test Case (Caso de prueba de JUnit)*.

![](./media/guidance-elasticsearch-jmeter-deploy10.png)

6.  En la ventana *New JUnit Test Case (Nuevo caso de prueba de JUnit)*, seleccione *New Junit 4 test (Nueva prueba de JUnit 4)*, escriba un nombre para el paquete (puede ser el mismo que el nombre del proyecto, aunque por convención debe comenzar con una letra minúscula) y un nombre para la clase de prueba y seleccione las opciones que generan el código auxiliar del método necesario para la prueba. Deje el cuadro *Class under test (Clase sometida a prueba)* vacío y haga clic en *Finish (Finalizar)*.

![](./media/guidance-elasticsearch-jmeter-deploy11.png)

7.  Si aparece el siguiente cuadro de diálogo *New JUnit Test Case (Nuevo caso de prueba de JUnit)*, especifique la opción para agregar la biblioteca JUnit 4 a la ruta de acceso de compilación y haga clic en *OK (Aceptar)*.

![](./media/guidance-elasticsearch-jmeter-deploy12.png)

8.  Compruebe que se genera el código del esqueleto para la prueba de JUnit y que se muestra en la ventana del editor de Java.

![](./media/guidance-elasticsearch-jmeter-deploy13.png)

9.  En el *Package Explorer (Explorador de paquetes)*, haga clic con el botón derecho en el nodo del proyecto, haga clic en *Configure (Configurar)* y luego haga clic en *Convert to Maven Project (Convertir en proyecto Maven)*.

> [AZURE.NOTE] El uso de Maven le permite administrar más fácilmente las dependencias externas (como las bibliotecas de cliente de Java de Elasticsearch) de las que depende un proyecto.

![](./media/guidance-elasticsearch-jmeter-deploy14.png)

10.  En el cuadro de diálogo *Create new POM (Crear nuevo archivo POM)*, en el cuadro de la lista desplegable *Packaging (Empaquetado)*, seleccione *jar* y luego haga clic en *Finish (Finalizar)*.

![](./media/guidance-elasticsearch-jmeter-deploy15.png)

11.  El panel que aparece debajo del editor de POM puede mostrar la advertencia *La ruta de acceso de la compilación especifica el entorno de ejecución J2SE-1.5. No hay JRE instalados en el área de trabajo que sean totalmente compatibles con este entorno*, dependiendo de qué versión de Java haya instalado en el equipo de desarrollo. Si tiene una versión de Java que sea posterior a la versión 1.5, puede ignorar esta advertencia sin problema.

![](./media/guidance-elasticsearch-jmeter-deploy16.png)

12.  En el editor de POM, expanda *Properties (Propiedades)* y, a continuación, haga clic en *Create (Crear)*.

![](./media/guidance-elasticsearch-jmeter-deploy17.png)

13.  En el cuadro de diálogo *Add Property (Agregar propiedad)*, en el cuadro *Name (Nombre)* escriba *es.version*; en el cuadro *Value (Valor)*, escriba *1.7.2*; y luego haga clic en *OK (Aceptar)*. Esta es la versión de la biblioteca de cliente de Java de Elasticsearch que se debe usar (esta versión se puede reemplazar en el futuro, y mediante la definición de la versión como una propiedad POM y la referencia a esta propiedad en otro lugar en el proyecto es posible cambiar rápidamente la versión).

![](./media/guidance-elasticsearch-jmeter-deploy18.png)

14.  Haga clic en la pestaña *Dependencies (Dependencias)* en la base del editor de POM y, a continuación, haga clic en *Add (Agregar)* junto al cuadro de la lista *Dependencies (Dependencias)*.

![](./media/guidance-elasticsearch-jmeter-deploy19.png)

15.  En el cuadro de diálogo *Select Dependency (Seleccionar dependencia)*, en el cuadro *Group Id (Id. de grupo)*, escriba *org.elasticsearch*; en el cuadro *Artifact Id (Id. de artefacto)*, escriba *elasticsearch*; en el cuadro *Version (Versión)*, escriba *\\${es.version}*; y luego haga clic en *OK (Aceptar)*. La información sobre la biblioteca de cliente Elasticsearch de Java se almacena en el repositorio central de Maven en línea, y esta configuración descargará automáticamente la biblioteca y sus dependencias cuando se compile el proyecto.

![](./media/guidance-elasticsearch-jmeter-deploy20.png)

16.  En el menú *File (Archivo)*, haga clic en *Save All (Guardar todo)*. Con esta acción se guardará y compilará el proyecto, descargando las dependencias especificadas por Maven. Compruebe que aparece la carpeta *Maven Dependencies (Dependencias de Maven)* en el explorador de paquetes. Expanda esta carpeta para ver los archivos jar descargados para admitir la biblioteca de cliente de Java de Elasticsearch.

![](./media/guidance-elasticsearch-jmeter-deploy21.png)


## Importación de un proyecto de prueba existente de JUnit en Eclipse

> [AZURE.NOTE] En este procedimiento se supone que ha descargado un proyecto de Maven que se ha creado previamente utilizando Eclipse.

1.  Inicie el IDE de Eclipse.

2.  En el menú *File (Archivo)* , haga clic en *Import (Importar)*.

![](./media/guidance-elasticsearch-jmeter-deploy22.png)

3.  En la ventana *Select (Seleccionar)*, expanda la carpeta *Maven*, haga clic en *Existing Maven Projects (Proyectos de Maven existentes)* y luego en *Next (Siguiente)*.

![](./media/guidance-elasticsearch-jmeter-deploy23.png)

4.  En la ventana *Maven Projects (Proyectos de Maven)*, especifique la carpeta que contiene el proyecto (la carpeta que contiene el archivo pom.xml), haga clic en *Select All (Seleccionar todo)* y luego haga clic en *Finish (Finalizar)*.

![](./media/guidance-elasticsearch-jmeter-deploy24.png)

5.  En la ventana *Package Explorer (Explorador de paquetes)*, expanda el nodo correspondiente a su proyecto. Compruebe que el proyecto contiene una carpeta denominada *src*. Esta carpeta contiene el código fuente para la prueba de JUnit. El proyecto puede compilarse e implementarse siguiendo las instrucciones del procedimiento [Deploying a JUnit Test to JMeter (Implementación de una prueba de JUnit en JMeter)](#deploying-a-junit-test-to-jmeter).

![](./media/guidance-elasticsearch-jmeter-deploy25.png)

## Implementación de una prueba de JUnit en JMeter

> [AZURE.NOTE] En este proyecto se supone que ha creado un proyecto denominado LoadTest que contiene una clase de prueba de JUnit denominada BulkLoadTest.java que acepta parámetros de configuración que se pasan como una sola cadena a un constructor (este es el mecanismo que espera JMeter).

1.  En el IDE de Eclipse, en el explorador de paquetes, haga clic con el botón derecho en el nodo del proyecto y luego haga clic en *Export (Exportar)*.

![](./media/guidance-elasticsearch-jmeter-deploy26.png)

2.  En el *asistente para exportación*, en la página *Select (Seleccionar)* expanda el nodo *Java*, haga clic en *JAR file (Archivo JAR)* y luego haga clic en *Next (Siguiente)*.

![](./media/guidance-elasticsearch-jmeter-deploy27.png)

3.  En la página *JAR File Specification (Especificación de archivo JAR)*, en el cuadro *Select the resources to export (Seleccionar los recursos para exportar)*, expanda el proyecto que se va a exportar y anule la selección de todo excepto la carpeta *src*, anule la selección de *.classpath*, anule la selección de *Project*, y anule la selección de *pom.xml*. En el cuadro *JAR file (Archivo JAR)*, proporcione un nombre de archivo y una ubicación para el archivo JAR (se le debe asignar la extensión de archivo .jar) y luego haga clic en *Finish (Finalizar)*.

![](./media/guidance-elasticsearch-jmeter-deploy28.png)

4.  Mediante el Explorador de Windows, copie el archivo JAR que acaba de crear en la JVM maestra de JMeter y guárdelo en la carpeta *apache-jmeter-2.13\\lib\\junit* bajo la carpeta donde ha instalado JMeter (consulte el procedimiento [Installing and Configuring JMeter on the JMeter Master VM (Instalación y configuración de JMeter en la VM maestra de JMeter)](#_Installing_and_Configuring) para obtener más información).

5.  Vuelva a Eclipse, expanda la ventana del explorador de paquetes y tome nota de todos los archivos JAR y sus ubicaciones que aparecen en la carpeta *Maven Dependencies (Dependencias de Maven)* carpeta para el proyecto. Tenga en cuenta que los archivos mostrados en la siguiente imagen pueden variar, dependiendo de qué versión de Elasticsearch esté utilizando:

![](./media/guidance-elasticsearch-jmeter-deploy29.png)

6.  Mediante el Explorador de Windows, copie cada archivo JAR al que se hace referencia en la carpeta de dependencias de Maven en la carpeta *apache-jmeter-2.13\\lib\\junit* de la máquina virtual maestra de JMeter.

> [AZURE.NOTE] Si la carpeta l*ib\\junit* ya contiene versiones anteriores de estos archivos JAR, quítelos. Si los deja en su lugar, la prueba JUnit podría no funcionar, ya que las referencias podrían resolverse en los JAR incorrectos.

7.  En la VM maestra de JMeter, detenga JMeter si se está ejecutando actualmente.

8.  Inicie JMeter.

9.  En JMeter, haga clic con el botón derecho en *Test Plan (Plan de pruebas)*, haga clic en *Add (Agregar)*, haga clic en *Threads (Users) (Subprocesos (Usuarios))* y luego haga clic en *Thread Group (Grupo de subprocesos)*.

![](./media/guidance-elasticsearch-jmeter-deploy30.png)

10.  En el nodo *Test Plan (Plan de pruebas)*, haga clic con el botón derecho en *Thread-Group (Grupo de subprocesos)*, haga clic en *Add (Agregar)*, haga clic en *Sampler (Muestra)* y luego haga clic en *JUnit Request*.

![](./media/guidance-elasticsearch-jmeter-deploy31.png)

11.  En la página *JUnit Request*, seleccione *Search for JUnit4 annotations (Buscar anotaciones de JUnit4)* (en lugar de 3 JUnit). En la lista desplegable *Classname (Nombre de clase)*, seleccione la clase de prueba de carga de JUnit (que se mostrará en el formulario *&lt;package (paquete)&gt;.&lt;class (clase)&gt;*), en el cuadro de la lista desplegable *Test Method (Método de prueba)*, seleccione el método de prueba de JUnit (se trata del método que realiza realmente el trabajo asociado a la prueba y que debe haberse marcado con la anotación *@test* en el proyecto de Eclipse) y escriba los valores que se pasan al constructor en el cuadro *Constructor String Label (Etiqueta de cadena de constructor)* (los detalles mostrados en la siguiente imagen son solo algunos ejemplos; su *Classname (Nombre de clase)*, *Test Method (Método de prueba)* y *Constructor String Label (Etiqueta de cadena de constructor)* probablemente serán diferentes de los que se muestran).

![](./media/guidance-elasticsearch-jmeter-deploy32.png)

> [AZURE.NOTE]  Si la clase no aparece en el cuadro de la lista desplegable *Classname (Nombre de clase)*, es probable que el archivo JAR no se haya exportado correctamente o no se haya colocado en la carpeta *lib\\junit*, o que falten algunos archivos JAR dependientes de la carpeta *lib\\junit*. Si esto ocurre, vuelva a exportar el proyecto de Eclipse y asegúrese de que ha seleccionado el recurso *src*, copie el archivo JAR en la carpeta *lib\\junit* y compruebe que ha copiado todos los archivos JAR dependientes enumerados por Maven en la carpeta *lib*.

12.  Cierre JMeter. No es necesario guardar el plan de pruebas.

13.  Copie el archivo JAR que contiene la clase de prueba de JUnit a la carpeta */home/&lt;NombreUsuario&gt;/apache-jmeter-2.13/lib/junit* en cada una de las máquinas virtuales subordinadas de JMeter (*&lt;NombreUsuario&gt;* es el nombre del usuario administrativo que especificó cuando creó la máquina virtual; consulte [Creating the JMeter Subordinate Virtual Machines (Creación de máquinas virtuales subordinadas de JMeter)](#_Creating_the_JMeter_2) para obtener más información).

14.  Copie los archivos JAR dependientes requeridos por la clase de prueba de JUnit en la carpeta */home/&lt;username&gt;/apache-jmeter-2.13/lib/junit* en cada una de las máquinas virtuales subordinadas de JMeter. Asegúrese de quitar primero las versiones anteriores de los archivos JAR de esta carpeta.

> [AZURE.NOTE] Puede emplear la utilidad pscp para copiar archivos desde un equipo con Windows a Ubuntu.

<!---HONumber=AcomDC_0211_2016-->
