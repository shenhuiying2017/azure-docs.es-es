---
title: "Transformación Derivar columna por ejemplos mediante Azure Machine Learning Workbench"
description: "Documento de referencia para la transformación \"Derivar columna por ejemplos\""
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a02f5e827345a1d28f01d691e1b6fbccfc03ae8a
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---


# <a name="derive-column-by-example-transformation"></a>Transformación Derivar columna por ejemplos

La transformación **Derivar columna por ejemplos** permite que los usuarios creen un derivado de una o varias columnas existentes usando ejemplos proporcionados por el usuario del resultado derivado. El derivado puede ser cualquier combinación de las transformaciones de cadena, fecha y número admitidas. 

Se admiten las siguientes transformaciones de cadena, fecha y número:

**Transformaciones de cadena:** 

Subcadena que incluye la extracción inteligente de números y fechas, concatenación, manipulación de mayúsculas y minúsculas y valores de constantes de asignación.

**Transformaciones de fecha:** 

Cambio del formato de fecha, extracción de partes de fecha y asignación de fechas y horas a períodos de tiempo.

Las transformaciones de fecha son más o menos genéricas, con algunas limitaciones importantes:
* No se admiten las zonas horarias.
* Algunos formatos comunes que no son compatibles:
    * Formato de semana ISO 8601 (por ejemplo, "2009-W53-7") 
    * Tiempo Unix.
* Todos los formatos distinguen mayúsculas de minúsculas (en concreto, "4 am" no se reconoce como una hora, pero "4 AM" sí).

**Transformaciones de número:** 

Redondeo, múltiplo inferior, múltiplo superior, discretización, relleno con ceros o espacios, división o multiplicación por una potencia de 1000.

**Transformaciones compuestas:** 

Cualquier combinación de transformaciones de fecha, número o cadena.

## <a name="how-to-use-this-transformation"></a>Cómo usar esta transformación

Para llevar a cabo esta transformación, siga estos pasos:
1. Seleccione una o varias columnas de las que quiera derivar el valor. 
2. Seleccione **Derive Column by Example** (Derivar columna por ejemplos) en el menú **Transforms** (Transformaciones). También puede hacer clic con el botón derecho en el encabezado de cualquiera de las columnas seleccionadas y seleccionar **Derive Column by Example** (Derivar columna por ejemplos) en el menú contextual. Se abrirá el Editor de transformación y se agregará una nueva columna junto a la columna seleccionada situada más a la derecha. Las columnas seleccionadas se pueden identificar por las casillas de los encabezados de columna. Para agregar o quitar columnas a la selección, use las casillas de los encabezados de las columnas.
3. Escriba un ejemplo de la *salida* en una fila y pulse Entrar. En este momento, Workbench analiza la columna de entrada y la salida proporcionada para sintetizar un programa que pueda transformar las entradas proporcionadas en una salida. El programa sintetizado se ejecuta en todas las filas de la cuadrícula de datos. Para los casos ambiguos y complicados, es posible que hagan falta varios ejemplos. Dependiendo de si se encuentra en el modo básico o en el modo avanzado, se pueden proporcionar varios ejemplos de distintas formas.
4. Revise la salida y haga clic en **Aceptar** para aceptar la transformación.

### <a name="transform-editor-basic-mode"></a>Editor de transformación: Modo básico

El modo básico proporciona una experiencia de edición directa en la cuadrícula de datos. Para proporcionar ejemplos de la salida, vaya a la celda que quiera y escriba el valor. 

Workbench analiza los datos e intenta identificar los casos límite que debe revisar el usuario. Mientras se analizan los datos, en el encabezado del editor de transformación aparece **Analyzing Data** (Analizando datos). Una vez efectuado el análisis, en el encabezado aparecerá **No suggestions** (Sin sugerencias) o **Review next suggested row** (Revisar la próxima fila sugerida). Para navegar por los casos límite, haga clic en **Review next suggested row** (Revisar la próxima fila sugerida). En el caso de que el valor sea incorrecto para una fila, debe especificar el valor correcto como un ejemplo adicional. 

### <a name="transform-editor-advanced-mode"></a>Editor de transformación: Modo avanzado

El modo avanzado ofrece una experiencia más completa para la transformación "Derivar columna por ejemplo". Todos los ejemplos se muestran en un solo lugar. También puede revisar todos los casos límite desde un solo lugar. Para ello, haga clic en **Show suggested examples** (Mostrar ejemplos sugeridos). 

En el modo avanzado puede agregar cualquier fila como una fila de ejemplo haciendo doble clic en la fila de la cuadrícula. Una vez copiada una fila como fila de ejemplo, también puede editar los datos de las columnas de origen para crear un ejemplo sintético. Al hacerlo, puede agregar casos que no estén presentes en los datos de ejemplo.

El usuario puede alternar entre el **modo básico** y el **modo avanzado**. Para ello, debe hacer clic en los vínculos del Editor de transformación.

### <a name="editing-existing-transformation"></a>Editar la transformación existente

Para editar una transformación **Derivar columna por ejemplos** existente, seleccione la opción **Edit** (Editar) del paso de transformación. Al hacer clic en **Edit** (Editar), se abre el Editor de transformación en **modo avanzado** y se muestran todos los ejemplos proporcionados durante la creación de la transformación.

## <a name="examples-of-string-transformations-by-example"></a>Ejemplos de transformaciones de cadena por ejemplo


>[!NOTE] 
>Los valores que aparecen en **negrita** representan los ejemplos proporcionados para poder llevar a cabo la transformación en el conjunto de datos mostrado.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extracción de nombres de archivo de las rutas de acceso de archivo

Número de ejemplos necesarios para este caso: 2

|Entrada|Salida|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Manipulación de mayúsculas y minúsculas durante la extracción de las cadenas

Número de ejemplos necesarios para este caso: 3

|Entrada|Salida|
|:-----|:-----|
|REINDEER CT & DEAD END;  NEW HANOVER; Station 332; 2015-12-10 @ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-Station:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Station 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Station 329; 2015-12-10 @ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Station 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Station 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE;  LOWER SALFORD; Station 344; 2015-12-10 @ 16:51:42;|Lower Salford|
|BLUEROUTE  & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Manipulación del formato de fecha durante la extracción de cadena

Número de ejemplos necesarios para este caso: 1

|Entrada|Salida|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Station 313; 2015-12-11 @ 04:11:35;|**12 nov 2015 4 a. m.**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Station 313; 2015-12-11 @ 01:29:52;|12 nov 2015 1 a. m.|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 2015-12-11 @ 07:29:58;|12 nov 2015 7 a. m.|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 2015-12-10 @ 20:53:30;|12 Oct 2015 8 p. m.|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 2015-12-10 @ 23:02:27;|12 oct 2015 11 p. m.|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 2015-12-10 @ 19:25:22;|12 oct 2015 7 p. m.|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Station 313; 2015-12-10 @ 18:43:07;|12 oct 2015 6 p. m.|
|AVIGNON DR & DEAD END; LOWER MERION; 2015-12-10 @ 20:01:29-Station:STA24;|12 Oct 2015 8 p. m.|

### <a name="s4-concatenating-strings"></a>S4. Concatenación de las cadenas

Número de ejemplos necesarios para este caso: 1

>[!NOTE] 
>En este ejemplo, el carácter especial · representa espacios en la columna Salida.

|Nombre|Inicial del segundo nombre|Apellido|Salida|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|Una |Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico··Ropp|
|Lauren-May||Fullmer|Lauren-May··Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill··Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Generación de iniciales

Número de ejemplos necesarios para este caso: 2

|Nombre completo|Salida|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Asignación de valores constantes

Número de ejemplos necesarios para este caso: 3

|Sexo oficial|Salida|
|:-----|:-----:|
|Hombre|**0**|
|Mujer|**1**|
|Desconocido|**2**|
|Mujer|1|
|Mujer|1|
|Hombre|0|
|Desconocido|2|
|Hombre|0|
|Mujer|1|

## <a name="examples-of-number-transformations-by-example"></a>Ejemplos de transformaciones de número por ejemplo

>[!NOTE] 
>Los valores que aparecen en **negrita** representan los ejemplos proporcionados para poder llevar a cabo la transformación en el conjunto de datos mostrado.


### <a name="n1-rounding-to-nearest-10"></a>N1. Redondeo a la decena más cercana

Número de ejemplos necesarios para este caso: 1

|Entrada|Salida|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Redondeo hacia abajo a la decena más cercana

Número de ejemplos necesarios para este caso: 2

|Entrada|Salida|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Redondeo a la cifra 0,05 más cercana

Número de ejemplos necesarios para este caso: 2

|Entrada|Salida|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Discretización

Número de ejemplos necesarios para este caso: 1

|Entrada|Salida|
|-----:|:-----:|
|20.16|**20-25**|
|14,32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Escalado a 1000

Número de ejemplos necesarios para este caso: 1

|Entrada|Salida|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Relleno

Número de ejemplos necesarios para este caso: 1

|Código|Salida|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Ejemplos de transformaciones de fecha por ejemplo

>[!NOTE] 
>Los valores que aparecen en **negrita** representan los ejemplos proporcionados para poder llevar a cabo la transformación en el conjunto de datos mostrado.


### <a name="d1-extracting-date-parts"></a>D1. Extracción de partes de fecha

Estas partes de fecha se han extraído siguiendo distintas transformaciones según el ejemplo en el mismo conjunto de datos. Las cadenas que aparecen en negrita representan los ejemplos indicados en la transformación correspondiente.

|DateTime|Día de la semana|Date|Mes|Year|Hora|Minuto|Segundo|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Ene-2031 05:54:18|**Vi.**|**31**|**Ene**|**2031**|**5**|**54**|**18**|
|17-Ene-1990 13:32:01|Mi.|17|Ene|1990|13|32|01|
|14-Feb-2034 05:36:07|Ma.|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Ju.|14|Mar|2002|13|16|16|
|21-Ene-1985 05:44:43|Lu.|21|Ene|1985|5|44|**43**|
|16-Ago-1985 01:11:56|Vi.|16|Ago|1985|1|11|56|
|20-Dic-2033 18:36:29|Ma.|20 ||Dic|2033|18|36|29|
|16-Jul-1984 10:21:59|Lu.|16|Jul|1984|10|21|59|
|13-Ene-2038 10:59:36|Mi.|13|Ene|2038|10|59|36|
|14-Ago-1982 15:13:54|Sá.|14|Ago|1982|15|13|54|
|22-Nov-2030 08:18:08|Vi.|22|Nov|2030|8|18|08|
|21-Oct-1997 08:42:58|Ma.|21|Oct|1997|8|42|58|
|28-Nov-2006 14:19:15|Ma.|28|Nov|2006|14|19|15|
|29-Abr-2031 04:59:45|Ma.|29|Abr|2031|4|59|45|
|29-Ene-2032 02:38:36|Ju.|29|Ene|2032|2|38|36|
|11-May-2028 15:31:52|Ju.|11|May|2028|15|31|52|
|15-Jul-1977 12:45:39|Vi.|15|Jul|1977|12|45|39|
|27-Ene-2029 05:55:41|Sá.|27|Ene|2029|5|55|41|
|03-Mar-2024 10:17:49|Do.|3|Mar|2024|10|17|49|
|14-Abr-2010 00:23:13|Mi.|14|Abr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Aplicar formato a las fechas

Estos formatos de fecha se han aplicado siguiendo distintas transformaciones según el ejemplo en el mismo conjunto de datos. Las cadenas que aparecen en negrita representan los ejemplos indicados en la transformación correspondiente.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Ene-2031 05:54:18|**1/31/2031**|**Viernes, 31 de enero de 2031**|**01312031 5:54**|**31/1/2031 5:54 a. m.**|**T1 2031**|
|17-Ene-1990 13:32:01|1/17/1990|Miércoles, 17 de enero de 1990|01171990 13:32|17/1/1990 1:32 p. m.|T1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Martes, 14 de febrero de 2034|02142034 5:36|14/2/2034 5:36 a. m.|T1 2034
|14-Mar-2002 13:16:16|3/14/2002|Jueves, 14 de marzo de 2002|03142002 13:16|14/3/2002 1:16 p. m.|T1 2002
|21-Ene-1985 05:44:43|1/21/1985|Lunes, 21 de enero de 1985|01211985 5:44|21/1/1985 5:44 a. m.|T1 1985
|16-Ago-1985 01:11:56|8/16/1985|Viernes, 16 de agosto de 1985|08161985 1:11|16/8/1985 1:11 a. m.|T3 1985
|20-Dic-2033 18:36:29|12/20/2033|Martes, 20 de diciembre de 2033|12202033 18:36|20/12/2033 6:36 p. m.|T4 2033
|16-Jul-1984 10:21:59|7/16/1984|Lunes, 16 de julio de 1984|07161984 10:21|16/7/1984 10:21 a. m.|T3 1984
|13-Ene-2038 10:59:36|1/13/2038|Miércoles, 13 de enero de 2038|01132038 10:59|13/1/2038 10:59 a. m.|T1 2038
|14-Ago-1982 15:13:54|8/14/1982|Sábado, 14 de agosto de 1982|08141982 15:13|14/8/1982 3:13 p. m.|T3 1982
|22-Nov-2030 08:18:08|11/22/2030|Viernes, 22 de noviembre de 2030|11222030 8:18|22/11/2030 8:18 a. m.|T4 2030
|21-Oct-1997 08:42:58|10/21/1997|Martes, 21 de octubre de 1997|10211997 8:42|21/10/1997 8:42 a. m.|T4 1997
|28-Nov-2006 14:19:15|11/28/2006|Martes, 28 de noviembre de 2006|11282006 14:19|28/11/2006 2:19 p. m.|T4 2006
|29-Abr-2031 04:59:45|4/29/2031|Martes, 29 de abril de 2031|04292031 4:59|29/4/2031 4:59 a. m.|T2 2031
|29-Ene-2032 02:38:36|1/29/2032|Jueves, 29 de enero de 2032|01292032 2:38|29/1/2032 2:38 a. m.|T1 2032
|11-May-2028 15:31:52|5/11/2028|Jueves, 11 de mayo de 2028|05112028 15:31|11/5/2028 3:31 p. m.|T2 2028
|15-Jul-1977 12:45:39|7/15/1977|Viernes, 15 de julio de 1977|07151977 12:45|15/7/1977 12:45 p. m.|T3 1977
|27-Ene-2029 05:55:41|1/27/2029|Sábado, 27 de enero de 2029|01272029 5:55|27/1/2029 5:55 a. m.|T1 2029
|03-Mar-2024 10:17:49|3/3/2024|Domingo, 3 de marzo de 2024|03032024 10:17|3/3/2024 10:17 a. m.|T1 2024
|14-Abr-2010 00:23:13|4/14/2010|Miércoles, 14 de abril de 2010|04142010 0:23|14/4/2010 12:23 a. m.|T2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Asignación de fechas y horas a períodos de tiempo

Estas asignaciones de fechas y horas a períodos se han efectuado siguiendo distintas transformaciones según el ejemplo en el mismo conjunto de datos. Las cadenas que aparecen en negrita representan los ejemplos indicados en la transformación correspondiente.

|DateTime|Período (segundos)|Período (minutos)|Período (dos horas)|Período (30 minutos)|
|-----:|-----:|-----:|-----:|-----:|
|31-Ene-2031 05:54:18|**0-20**|**45-60**|**5 a. m.-7 a. m.**|**5:30-6:00**|
|17-Ene-1990 13:32:01|**0-20**|30-45|1 p. m.-3 p. m.|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|5 a. m.-7 a. m.|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1 p. m.-3 p. m.|13:00-13:30|
|21-Ene-1985 05:44:43|40-60|30-45|5 a. m.-7 a. m.|5:30-6:00|
|16-Ago-1985 01:11:56|40-60|0-15|1 a. m.-3 a. m.|1:00-1:30|
|20-Dic-2033 18:36:29|20-40|30-45|5 p. m.-7 p. m.|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9 a. m.-11 a. m.|10:00-10:30|
|13-Ene-2038 10:59:36|20-40|45-60|9 a. m.-11 a. m.|10:30-11:00|
|14-Ago-1982 15:13:54|40-60|0-15|3 p. m.-5 p. m.|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7 a. m.-9 a. m.|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7 a. m.-9 a. m.|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1 p. m.-3 p. m.|14:00-14:30|
|29-Abr-2031 04:59:45|40-60|45-60|3 a. m.-5 a. m.|4:30-5:00|
|29-Ene-2032 02:38:36|20-40|30-45|1 a. m.-3 a. m.|2:30-3:00|
|11-May-2028 15:31:52|40-60|30-45|3 p. m.-5 p. m.|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11 a. m.-1 p. m.|12:30-13:00|
|27-Ene-2029 05:55:41|40-60|45-60|5 a. m.-7 a. m.|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9 a. m.-11 a. m.|10:00-10:30|
|14-Abr-2010 00:23:13|0-20|15-30|11 p. m.-1 a. m.|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Ejemplos de transformaciones compuestas por ejemplo

|tripduration|starttime|Id. de estación de partida|Latitud de la estación de partida|Longitud de la estación de partida|Tipo de usuario|Columna|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Suscriptor|**Un suscriptor tomó una bicicleta de la estación 107, lat/long (42.363,-71.088), el 8 de enero de 2016 cerca de las 4 p. m. El viaje duró 61 minutos**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Cliente|Un cliente tomó una bicicleta de la estación 74, lat/long (42.373,-71.119), el 17 de enero de 2016 cerca de las 9 a. m. El viaje duró 61 minutos|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Suscriptor|Un suscriptor tomó una bicicleta de la estación 176, lat/long (42.387,-71.119), el 25 de enero de 2016 cerca de las 8 a. m. El viaje duró 62 minutos|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Suscriptor|Un suscriptor tomó una bicicleta de la estación 107, lat/long (42.363,-71.088), el 8 de enero de 2016 cerca de las 10 a. m. El viaje duró 63 minutos|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Suscriptor|Un suscriptor tomó una bicicleta de la estación 68, lat/long (42.365,-71.103), el 15 de enero de 2016 cerca de las 7 p. m. El viaje duró 64 minutos|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Suscriptor|Un suscriptor tomó una bicicleta de la estación 115, lat/long (42.388,-71.119), el 22 de enero de 2016 cerca de las 6 p. m. El viaje duró 64 minutos|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Suscriptor|Un suscriptor tomó una bicicleta de la estación 178, lat/long (42.360,-71.101), el 18 de enero de 2016 cerca de las 9 a. m. El viaje duró 68 minutos|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Suscriptor|Un suscriptor tomó una bicicleta de la estación 176, lat/long (42.387,-71.119), el 14 de enero de 2016 cerca de las 8 a. m. El viaje duró 69 minutos|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Suscriptor|Un suscriptor tomó una bicicleta de la estación 141, lat/long (42.364,-71.082), el 13 de enero de 2016 cerca de las 10 p. m. El viaje duró 69 minutos|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Suscriptor|Un suscriptor tomó una bicicleta de la estación 176, lat/long (42.387,-71.119), el 15 de enero de 2016 cerca de las 8 a. m. El viaje duró 69 minutos|


## <a name="technical-notes"></a>Notas técnicas

### <a name="conditional-transformations"></a>Transformaciones condicionales
En algunos casos no se puede encontrar una transformación que se ajuste a los ejemplos indicados. En tales casos, la transformación Derivar columna por ejemplos intenta agrupar las entradas en función de algunos patrones e intenta aprender a separar la transformación para cada grupo. Esto se denomina **transformación condicional**. La **transformación condicional** se trata de efectuar únicamente para las transformaciones que tienen una sola columna de entrada. 

### <a name="reference"></a>Referencia
En [esta publicación](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/) encontrará más información sobre la tecnología Transformación de cadena por ejemplo.

