<properties
	pageTitle="Asistente para copia de Data Factory | Microsoft Azure"
	description="Obtenga información sobre cómo utilizar el Asistente para copia de Data Factory para copiar datos de orígenes de datos admitidos en receptores."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="spelluru"/>

# Asistente para copia de Data Factory
El Asistente para copia de Data Factory de Azure permite crear una canalización para copiar datos de orígenes compatibles en destinos sin escribir definiciones de JSON para los servicios vinculados, los conjuntos de datos y las canalizaciones. Si quiere iniciar el Asistente para copia, haga clic en el icono **Copiar datos** de la página principal de Data Factory.

![Asistente para copia](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Características

### Un asistente intuitivo y sin interrupciones para copiar datos
Gracias a este asistente, podrá trasladar datos de un origen a un destino en cuestión de minutos con los siguientes sencillos pasos:

1.	Seleccione el origen.
2.	Seleccione el destino.
3.	Configure las opciones.

![Selección de origen de datos](./media/data-factory-copy-wizard/select-data-source-page.png)

### Exploración de datos enriquecidos y asignaciones de esquemas
Puede examinar tablas y carpetas, obtener una vista previa de los datos, asignar esquemas, validar expresiones y realizar transformaciones de datos simples en el propio asistente.

**Exploración de tablas o carpetas** ![Examinación de tablas y carpetas](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Experiencia escalable para distintos tipos de objetos y datos
Experiencia diseñada específicamente para los macrodatos desde el primer momento. Permite crear, de manera simple y eficiente, canalizaciones de Data Factory que trasladen cientos de carpetas, archivos o tablas.

**Obtener una vista previa de los datos** ![Configuración del formato de archivo](./media/data-factory-copy-wizard/file-format-settings.png)

**Asignar un esquema** ![Asignación de esquemas](./media/data-factory-copy-wizard/schema-mapping.png)

**Realizar transformaciones simples** ![Validación de expresiones](./media/data-factory-copy-wizard/validate-expressions.png)

### Experiencia escalable para distintos tipos de objetos y datos
Gracias al Asistente para copia puede mover de manera eficaz y sencilla cientos de tablas, archivos o carpetas.

![Selección de tablas desde las que se van a copiar datos](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### Opciones de programación más exhaustivas
Puede ejecutar la operación de copia una vez o de forma periódica (cada hora, día, etc.). Estas opciones se pueden utilizar para los distintos conectores locales y en la nube, así como para la copia del escritorio local.

Una operación de copia única permite el movimiento de datos desde un origen a un destino solamente una vez. Se aplica a los datos de cualquier tamaño y en cualquier formato admitido. La copia programada permite copiar datos con una frecuencia prescrita. Puede utilizar las opciones avanzadas (como el reintento, el tiempo de espera y las alertas) para configurar la copia programada.

![Propiedades de programación](./media/data-factory-copy-wizard/scheduling-properties.png)

## Variables de la ruta de la carpeta de blobs de Azure
Puede usar variables en la ruta de la carpeta para copiar datos desde una carpeta que se determina en tiempo de ejecución con arreglo a la [variable del sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Estas son las variables admitidas: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** y **{custom}**. Ejemplo: carpetaDeEntrada/{year}/{month}/{day}.

Supongamos que tiene carpetas de entrada con el siguiente formato:

	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Haga clic en el botón **Examinar** del **archivo o carpeta**, vaya a una de estas carpetas (por ejemplo, 2016->03->01->02) y haga clic en **Seleccionar**. En el cuadro de texto, debería aparecer **2016/03/01/02**. Ahora, sustituya **2016** por **{year}**, **03** por **{month}**, **01** por **{day}** y **02** por **{hour}** y presione la tecla Tabulación. Aparecerán listas desplegables en las que podrá seleccionar el formato de estas cuatro variables:

![Uso de variables del sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

Como se muestra en la siguiente captura de pantalla, también puede usar una variable **personalizada** y cualquier [cadena de formato admitida](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Para seleccionar una carpeta con esa estructura, utilice primero el botón **Examinar**. A continuación, reemplace un valor por **{custom}** y presione la tecla Tabulación para ver el cuadro de texto donde puede escribir la cadena de formato.

![Uso de la variable personalizada](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## Pasos siguientes
Si quiere ver un tutorial rápido sobre el uso del Asistente para copia de Data Factory a fin de crear una canalización con una actividad de copia, consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md).

<!---HONumber=AcomDC_0824_2016-->