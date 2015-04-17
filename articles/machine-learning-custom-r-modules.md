<properties 
	pageTitle="Crear módulos R personalizados en Aprendizaje automático de Azure | Azure" 
	description="Inicio rápido para la creación de módulos R personalizados en Aprendizaje automático de Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Crear módulos R personalizados en Aprendizaje automático de Azure

En este tema se describe cómo crear un módulo R personalizado en Aprendizaje automático de Azure. Describe qué módulo R personalizado es y qué archivos se utilizan para definirlos. Muestra cómo construir estos archivos y registrar el módulo para la implementación en un área de trabajo de aprendizaje automático. Los elementos y atributos que se utilizan en la definición del módulo personalizado se describen a continuación con más detalle. También se describe cómo utilizar las funciones auxiliares, los archivos y varias salidas. 

## ¿Qué es un módulo R personalizado?
Un módulo personalizado es un módulo definido por el usuario que se puede cargar en el área de trabajo de un usuario y ejecutar como parte de un experimento de Aprendizaje automático de Azure. Un módulo R personalizado es un módulo personalizado que ejecuta una función R definida por el usuario en el aprendizaje automático. R es un lenguaje de programación de computación estadística y gráficos utilizado ampliamente por científicos estadísticos y de datos para implementar algoritmos. La compatibilidad con lenguajes de aprendizaje automático está establecida de forma predeterminada para usar el lenguaje de R en los módulos personalizados.

Los módulos personalizados tienen un estado de primera clase en Aprendizaje automático de Azure en el sentido de que se pueden usar como cualquier otro módulo. Pueden ejecutarse con otros módulos, incluido en experimentos publicados o visualizados. Los usuarios tienen control sobre el algoritmo implementado por el módulo, los puertos de entrada y de salida a utilizar, los parámetros de modelado y otros distintos comportamientos en tiempo de ejecución.


## Archivos de un módulo R personalizado
Un módulo R personalizado se define mediante un archivo .zip que contiene, como mínimo, dos archivos:

* Un archivo que implementa la función R expuesta por el módulo
* Un archivo de definición XML que describe el módulo personalizado

También se pueden incluir archivos auxiliares adicionales en el archivo .zip que proporcionan una funcionalidad a la que se puede tener acceso desde el módulo personalizado. Esta opción se describe a continuación.

## Ejemplo de inicio rápido
En este ejemplo se muestra cómo construir los archivos requeridos por un módulo R personalizado, empaquetarlos en un archivo zip y, a continuación, registrar el módulo en el área de trabajo de aprendizaje automático.

Considere el ejemplo de un módulo personalizado Agregar filas que modifica la implementación estándar del módulo de Agregar filas que se usa para concatenar las filas (observaciones) de dos conjuntos de datos (tramas de datos). El módulo Agregar filas anexa las filas del segundo conjunto de datos de entrada al final del primer conjunto de datos entrada mediante el algoritmo rbind. La función `myAddRows` personalizada acepta dos conjuntos de datos de forma similar, pero también acepta un parámetro booleano de intercambio adicional como entrada. Si el parámetro de intercambio es **FALSE**, devuelve el mismo conjunto de datos que la implementación estándar. Pero si el parámetro de intercambio es **TRUE**, anexa filas del primer conjunto de datos de entrada al final del segundo conjunto de datos en su lugar. El archivo que implementa la función de R myAddRows expuesta por el módulo Agregar filas contiene el siguiente código de R.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

Para exponer esta `myAddRows` función como un módulo de Aprendizaje automático de Azure, debe crearse un archivo de definición XML para especificar la apariencia y comportamiento que debe tener el módulo Agregar filas. 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
Tenga en cuenta que es fundamental que el contenido del elemento identificador del archivo XML coincida exactamente con el nombre de la función. 

Guardar estos dos archivos como *myAddRows.R* y *myAddRows.xml* y comprímalos juntos en un *myAddRows.zip* archivo.

Para registrarlos en su área de trabajo de aprendizaje automático, vaya al área de trabajo de Machine Learning Studio, haga clic en el botón **+NUEVO** que se encuentra situado en la parte inferior y elija **MÓDULO -> DEL PAQUETE ZIP** para cargar el nuevo módulo personalizado Agregar filas.

![](http://i.imgur.com/RFJhCls.png)

El módulo Agregar filas está ahora preparado para que accedan a él sus experimentos de aprendizaje automático.

## Elementos del archivo de definición XML

### Entradas y salidas
Las entradas y salidas de un módulo personalizado se especifican en los elementos secundarios de la sección de puertos del archivo de definición XML. El orden de estos elementos de entrada y salida determina el diseño visualizado (UX) por los usuarios. El primer elemento secundario que aparece en el elemento Puertos  del archivo XML será el puerto de entrada del extremo izquierdo de la experiencia del usuario del aprendizaje automático. Los tipos de datos que se admiten para los puertos de entrada y salida son los siguientes: 

**DataTable**: Este tipo se pasa a la función R como un data.frame. De hecho, los tipos (por ejemplo, archivos CSV o archivos ARFF) que se admiten  mediante el aprendizaje automático y que son compatibles con DataTable se convierten a data.frame automáticamente. 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip**: los módulos personalizados pueden aceptar un archivo zip como entrada. Esta entrada se desempaqueta en el directorio de ejecución de la función

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

Es necesario que los atributos de id asociados con cada uno de los puertos de entrada y salida tengan valores únicos y que estos valores coincidan con los parámetros con nombre de la función R. Además, debe especificarse un valor predeterminado para los atributos cuyas entradas son opcionales. El atributo IsOptional es opcional para los tipos de la DataTable y Zip y es false de forma predeterminada. Este valor predeterminado indica que el tipo de entrada no es opcional.


### Parámetros
Los parámetros de un módulo personalizado se especifican en los elementos secundarios de la sección Argumentos del archivo de definición XML. Al igual que con los elementos secundarios de la sección de puertos, el orden de los parámetros de la sección Argumentos define el diseño encontrado en la experiencia de usuario. El primer parámetro que aparece será el primer parámetro de función. A continuación se enumeran los tipos admitidos por el aprendizaje automático para los parámetros. Los atributos opcionales deben tener los valores predeterminados especificados en la definición de XML. Estos son los valores que se utiliza si el valor del parámetro no se especifica al utilizar la función. Para cada tipo se indica qué atributos son opcionales.


**int**: un parámetro de tipo (32 bits) entero.

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

IsOptional, MinValue y MaxValue son opcionales para int.

**doble**: un parámetro de tipo doble.

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
IsOptional, min., max., predeterminado son opcionales para double.

**bool**: un parámetro booleano representado mediante una casilla de verificación en la experiencia de usuario.

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

default es opcional para el tipo bool.

**string**: una cadena estándar

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

default e IsOptional son opcionales para string.

**ColumnPickerFor**: parámetro de selección de columna. Este tipo se representa en la experiencia de usuario como selector de columnas. El identificador de DataTable del que está seleccionando columnas debe reemplazar la parte de la tabla del valor del atributo de tipo. La variable se pasará a la función como una lista de cadenas. 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

Si, por ejemplo, tuvimos un DataTable con un identificador de dataset1, el tipo tendría 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<DropDown Type ID>**: una lista enumerada (desplegable). El valor elegido se pasa como una cadena a la función R. Este tipo requiere que los valores enumerados válidos se definan primero dentro de la sección Argumentos.

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

Al igual que con las entradas y salidas, es fundamental que cada uno de los parámetros tenga valores de identificadores únicos asociados a ellos. Además, los valores de identificador deben corresponderse a los parámetros con nombre de la función R. En nuestro ejemplo de inicio rápido, el parámetro/id asociado era *swap*.

### Definición de lenguaje
El elemento del lenguaje del archivo de definición XML define la funcionalidad específica del lenguaje. Para los módulos de R generalmente, tenemos 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

Especifica el idioma específico, el archivo en el que se define la función y el punto de entrada de esa definición. El atributo AddDisplayOutputPort es opcional para el elemento de lenguaje. Como con el módulo Ejecutar Script de R (vínculo TBD), si desea agregar un puerto de salida que puede utilizarse para visualizar gráficos y trazados, elija *true* para la etiqueta de AddDisplayOutputPort y se mostrará un puerto de salida adicional. 

### Funciones auxiliares

Hay una serie de atributos que no se exponen en este ejemplo, pero que pueden ser utilizadas por los autores del módulo personalizado. Por ejemplo, un módulo puede tener un comportamiento determinista o no determinista. Un módulo determinista no se ejecutará una segunda vez si se suministran los mismos datos de entrada y la misma configuración de parámetro. En su lugar, se utilizarán los resultados almacenados en caché en su lugar se utiliza y se propagarán a los módulos de bajada. Un ejemplo de un módulo determinista en Aprendizaje automático de Azure es el módulo Agregar filas. Un ejemplo de un módulo no determinista es Lector. Para hacer que el módulo personalizado sea no determinista, cambie la configuración predeterminada agregando el atributo siguiente a la definición de:

	<IsDeterministic>false</IsDeterministic>

### Archivos auxiliares

Cualquier archivo que se coloque en el archivo ZIP de módulo personalizado estará disponible para su uso durante el tiempo de ejecución. Si hay presente una estructura de directorios, se conservará. Esto significa que el abastecimiento de archivos funcionará igual localmente y en la ejecución de Aprendizaje automático de Azure. 

Por ejemplo, supongamos que desea quitar todas las filas con NA y todas las filas duplicadas del conjunto de datos antes de generarlas en myAddRows y ya ha escrito una función R que lo hace en un archivo removeDupNARows.R:

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Puede originar el archivo auxiliar removeDupNARows.R en la función myAddRows:

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

Y, a continuación, cargue un archivo zip que contenga "myAddRows.R", "myAddRows.xml" y "removeDupNARows.R" como un módulo R personalizado.

### Varias salidas

Para emitir más de un objeto de un tipo de datos admitidos, es necesario especificar los puertos de salida correspondientes en el archivo de definición de XML y los objetos deben devolverse como una lista. Los objetos emitidos se asignarán a los puertos de salida de izquierda a derecha, reflejando el orden en que los objetos se colocan en la lista devuelta.
 
Por ejemplo, si desea emitir dataset, Dataset1 y Dataset2 a los puertos de salida dataset, dataset1 y dataset2 de izquierda a derecha, respectivamente, defina los puertos de salida en el archivo "myAddRows.xml" del modo indicado a continuación:

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

Y devuelva la lista de objetos de una lista en el orden correcto en "myAddRows.R":

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## Entorno de ejecución

TBD

## Pasos siguientes

TBD

<!--HONumber=49-->