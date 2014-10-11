<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Motor de recomendación simple con Apache Mahout

Apache Mahout™ es una biblioteca de aprendizaje automático creada para su uso en aplicaciones de aprendizaje automático escalables. Los motores de recomendación como Mahout son uno de los tipos de aplicaciones de aprendizaje automático más populares en uso hoy en día y tienen muchas aplicaciones de marketing evidentes.

Apache Mahout proporciona una implementación integrada para el filtrado colaborativo basado en artículos. Este enfoque se usa ampliamente para llevar a cabo la minería de datos basada en sugerencias. El filtrado colaborativo basado en artículos lo desarrolló Amazon.com. La idea es que los datos de las preferencias del usuario que muestran correlaciones entre las preferencias de artículos se puedan usar para deducir los gustos de los futuros usuarios de un grupo similar.

En este tutorial puede usar el sitio [Million Song Dataset][] y descargar el [conjunto de datos][] para crear recomendaciones de canciones para los usuarios basadas en sus últimos hábitos de escucha.

Aprenderá a:

-   Usar los motores de recomendación

Este tutorial se compone de los siguientes segmentos:

1.  [Instalación y configuración][]
2.  [Análisis y formato de los datos][]
3.  [Instalación de Mahout][]
4.  [Ejecución del trabajo de Mahout][]

## <a name="setup"></a>Instalación y configuración

Este tutorial asume que ha realizado la configuración con Azure y la vista previa de HDinsight y que ha creado un clúster de HDInsight en el que puede ejecutar una muestra. Si no lo ha hecho, consulte el tutorial de [Introducción a HDInsight de Azure][] para obtener instrucciones sobre cómo satisfacer estos requisitos previos.

## <a name="segment1"></a>Análisis y formato de los datos

Este ejemplo se refiere a la forma en que los usuarios expresan una preferencia por ciertas canciones. La suposición es que el número de veces que un usuario escucha una canción proporciona una medida de la preferencia de ese usuario por esa canción. Los patrones detectados en los datos de preferencia se pueden usar para predecir futuras preferencias del usuario basadas ​​en algunas de sus preferencias musicales expresadas. Puede ver una muestra de este conjunto de datos en la sección **Description** de la página web [Echo Nest Taste Profile Subset][Million Song Dataset]:

![El subconjunto de perfiles de gustos de Echo Nest][]

### Datos de la muestra del subconjunto de un millón de canciones

Para usar este conjunto de datos con Mahout, necesita realizar dos cosas:

1.  Convertir los identificadores de las canciones y los usuarios en valores enteros.
2.  Guardar los valores nuevos con sus clasificaciones en un archivo separado por comas.

Si no tiene instalado Visual Studio 2010, omita este paso y vaya a la sección Ejecución del trabajo de Mahout para obtener una versión generada previamente.

En primer lugar, inicie Visual Studio 2010. En Visual Studio, seleccione **Archivo -\> Nuevo -\> Proyecto**. En el panel **Plantillas instaladas**, dentro del nodo **Visual C \#**, seleccione la categoría **Ventana** y, a continuación, seleccione **Aplicación de consola** en la lista. Asigne al proyecto el nombre "ConvertToMahoutInput" y haga clic en el botón **Aceptar**.

![creación de una aplicación de consola][]

### Creación de una aplicación de consola

1.  Tras haber creado la aplicación, abra el archivo **Program.cs** y agregue los siguientes miembros estáticos a la clase **Program**:

        const char tab = '\u0009';
        static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
        static Dictionary<string, int> songMapping = new Dictionary<string, int>(); 

2.  A continuación, agregue la declaración `using System.IO;` y rellene el método **Main** con el siguiente código:

        var inputStream = File.Open(args[0], FileMode.Open);
        var reader = new StreamReader(inputStream);

        var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
        var writer = new StreamWriter(outStream);

        var i = 1;

        var line = reader.ReadLine();
        while (!string.IsNullOrWhiteSpace(line))
        {
            i++;
            if (i > 5000)
            break;
            var outLine = line.Split(tab);

            int user = GetUser(outLine[0]);
            int song = GetSong(outLine[1]);

            writer.Write(user);
            writer.Write(',');
            writer.Write(song);
            writer.Write(',');
            writer.WriteLine(outLine[2]);

            line = reader.ReadLine();
        }

        Console.WriteLine("saved {0} lines to {1}", i, args[0]);

        reader.Close();
        writer.Close();

        SaveMapping(usersMapping, "users.csv");
        SaveMapping(songMapping, "mInput.csv");

        Console.WriteLine("Mapping saved");
        Console.ReadKey();

3.  Ahora, cree las funciones **GetUser** y **GetSong**, que convierten los identificadores en enteros:

        static int GetUser(string user)
        {
            if (!usersMapping.ContainsKey(user))
                usersMapping.Add(user, usersMapping.Count + 1);

            return usersMapping[user];
        }

        static int GetSong(string song)
        {
            if (!songMapping.ContainsKey(song))
                songMapping.Add(song, songMapping.Count + 1);

            return songMapping[song];
        }

4.  Por último, cree la utilidad que implementa el método SaveMapping que guarda los diccionarios de asignación de bots en los archivos .csv.

        static void SaveMapping(Dictionary<string, int> mapping, string fileName)
        {
            var stream = File.Open(fileName, FileMode.Create);
            var writer = new StreamWriter(stream);

            foreach (var key in mapping.Keys)
            {
                writer.Write(key);
                writer.Write(',');
                writer.WriteLine(mapping[key]);
            }

            writer.Close();
        }

5.  Descargue los datos de muestra en [este vínculo][conjunto de datos]. Después de descargarlos, abra **train\_triplets.txt.zip** y extraiga **train\_triplets.txt**.

    Cuando se ejecuta la utilidad, incluya un argumento de línea de comandos con la ubicación de **train\_triplets.txt**. Para ello, haga clic con el botón secundario en el nodo de proyecto **ConvertToMahoutInput** en el **Explorador de soluciones** y seleccione **Propiedades**. En la página de propiedades del proyecto, seleccione la pestaña **Depurar** en el lado izquierdo y agregue la ruta de acceso de \<localpath\>train\_triplets.txt al cuadro de texto **Argumentos de la línea de comandos**:

    ![Establecimiento de argumentos de la línea de comandos][]

### Establecimiento del argumento de la línea de comandos

-   Presione **F5** para ejecutar el programa. Una vez ejecutado, abra la carpeta **bin\\Debug** en la ubicación donde se guardó el proyecto y vea la salida de la utilidad. Debe buscar users.txt y mInput.txt

## <a name="segment2"></a>Instalación de Mahout

-   Abra el portal de clústeres HDInsight y haga clic en el icono **Remote Desktop**.

    ![El icono de administración de clústeres][]

### El icono de escritorio remoto

HDInsight no incluye Mahout de manera predeterminada. Sin embargo, debido a que forma parte del ecosistema de Hadoop, se puede descargar desde el sitio web de [Mahout][]. La versión más reciente es la 0.7, pero este conjunto de instrucciones es compatible para la versión 0.5 o 0.7.

1.  En primer lugar, descargue [Mahout versión 0.7][] en su equipo local.

2.  A continuación, cópielo en el clúster seleccionando el archivo zip local y presione control-v para copiar; a continuación, péguelo en el clúster de Hadoop.

    ![Carga de Mahout][]

### Copia de Mahout en el nodo principal

1.  Por último, haga clic con el botón secundario en el archivo zip después de finalizado el proceso de copia y extraiga la distribución de Mahout en C:\\apps\\dist. Ha instalado Mahout en C:\\apps\\dist\\mahout-distribution-0.7.

2.  Cambie el nombre de la carpeta a c:\\apps\\dist\\mahout-0.7 para fines de simplicidad.

### <a name="segment3"></a>Ejecución del trabajo de Mahout

1.  Copie el archivo **mInput.txt** de la carpeta **bin\\Debug** en **c:\\** en el clúster remoto. Después de que se haya copiado el archivo, extráigalo. Como se mencionó en la sección anterior, la copia de un archivo a una sesión de RDP remota se realiza al presionar control-C en el equipo local después de seleccionar los archivos y, a continuación, control-v en la ventana de sesión de RDP.

2.  Cree un archivo que contiene el identificador del usuario para el que va a generar las recomendaciones. Para ello, cree un archivo de texto llamado **users.txt** en **c:\\**, que contiene el identificador de un solo usuario.

<div class="dev-callout"> 
<b>Nota:</b> 
<p>Puede generar recomendaciones para m&aacute;s usuarios al poner sus identificadores en l&iacute;neas independientes. Si tiene problemas para generar mInput.txt y users.txt, puede descargar una versi&oacute;n previamente generada en este <a href="https://github.com/wenming/BigDataSamples/tree/master/mahout">repositorio</a> de github. 

Es m&aacute;s pr&aacute;ctico descargar todo como un <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">archivo zip</a>. Busque users.txt y mInput.txt y c&oacute;pielos en el cl&uacute;ster remoto en la carpeta c:\</p> 
</div>

En este punto, debe abrir una ventana de terminal de Hadoop y dirigirse a la carpeta que contiene users.txt y mInput.txt.

![Ventana de comandos de Mahout][]

### Ventana de comandos de Hadoop

1.  A continuación, copie **mInput.txt** y **users.txt** en HDFS. Para ello, abra el **shell de comandos de Hadoop** y ejecute los siguientes comandos:

        hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
        hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2.  Compruebe que los archivos se hayan copiado en HDFS:

        hadoop fs -ls input/

    Esto debería mostrar:

        Found 2 items
        -rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
        -rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3.  Ahora podemos ejecutar el trabajo de Mahout con el siguiente comando:

        c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    Hay muchas otras funciones a "distancia" que el motor de recomendación podría usar para comparar el vector de característica para diferentes usuarios, es posible experimentar y cambiar la clase Similarity por SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION, SIMILARITY\_EUCLIDEAN\_DISTANCE. Para el propósito de este tutorial, no vamos a entrar en el aspecto científico de los datos en detalle de Mahout.

4.  El trabajo de Mahout debe ejecutarse durante varios minutos, después de lo cual se creará un archivo de salida. Ejecute el comando siguiente para crear una copia local del archivo de salida:

        hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5.  Abra el archivo **output.txt** desde la carpeta raíz **c:\\** e inspeccione su contenido. La estructura del archivo es la siguiente:

        user    [song:rating,song:rating, ...]

6.  Si desea usar otras partes de Mahout en el clúster, debe guardar una copia de Mahout.cmd en el directorio bin de la distribución de Mahout.

## <a name="summary"></a>Resumen

Los motores de recomendación proporcionan una funcionalidad importante para muchos sitios modernos de redes sociales, compras en línea, medios de transmisión y otros sitios de Internet. Mahout proporciona un motor de recomendaciones de uso inmediato que es fácil de usar, contiene muchas características útiles y es escalable en Hadoop.

## Pasos siguientes

Si bien este artículo muestra cómo usar la línea de comandos de Hadoop, también puede realizar tareas con la consola interactiva de HDInsight. Para obtener más información, consulte [Guidance: HDInsight Interactive JavaScript and Hive Consoles][interactive-console].

  [Million Song Dataset]: http://labrosa.ee.columbia.edu/millionsong/tasteprofile
  [conjunto de datos]: http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip
  [Instalación y configuración]: #setup
  [Análisis y formato de los datos]: #segment1
  [Instalación de Mahout]: #Segment2
  [Ejecución del trabajo de Mahout]: #segment2
  [Introducción a HDInsight de Azure]: /en-us/manage/services/hdinsight/get-started-hdinsight/
  [El subconjunto de perfiles de gustos de Echo Nest]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
  [creación de una aplicación de consola]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
  [Establecimiento de argumentos de la línea de comandos]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
  [El icono de administración de clústeres]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
  [Mahout]: http://mahout.apache.org/
  [Mahout versión 0.7]: http://www.apache.org/dyn/closer.cgi/mahout/
  [Carga de Mahout]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
  [repositorio]: https://github.com/wenming/BigDataSamples/tree/master/mahout
  [archivo zip]: https://github.com/wenming/BigDataSamples/archive/master.zip
  [Ventana de comandos de Mahout]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG
