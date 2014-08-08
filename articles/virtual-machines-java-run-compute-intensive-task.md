<properties linkid="dev-java-compute-load" urlDisplayName="TSP on Virtual Machine" pageTitle="Compute-intensive Java application on a VM - Azure" metaKeywords="Azure virtual machine Java, Azure Java app, Azure Java application" description="Learn how to create an Azure virtual machine that runs a compute-intensive Java application that can be monitored by another Java application." metaCanonical="" services="virtual-machines" documentationCenter="Java" title="How to run a compute-intensive task in Java on a virtual machine" authors="waltpo" videoId="" scriptId="" solutions="" manager="bjsmith" editor="mollybos" />

<!-- saved from url=(0022)http://internet.e-mail -->

Ejecución de una tarea de Java de proceso intensivo en una máquina virtual
==========================================================================

Con Azure puede utilizar una máquina virtual para controlar tareas de proceso intensivo. Por ejemplo, una máquina virtual puede controlar tareas y ofrecer los resultados a equipos cliente o a aplicaciones móviles. Al completar esta guía, habrá aprendido a crear una máquina virtual que ejecuta una aplicación Java de proceso intensivo y que otra aplicación Java puede supervisar.

Este tutorial asume que sabe cómo crear aplicaciones de consola Java, importar bibliotecas a su aplicación Java y generar un archivo Java (JAR). No se precisa ningún conocimiento de Azure.

Aprenderá a:

-   Crear una máquina virtual que tenga ya instalado un JDK.
-   Iniciar sesión de manera remota en la máquina virtual.
-   Crear un espacio de nombres del bus de servicio.
-   Crear una aplicación Java que realiza una tarea de proceso intensivo.
-   Crear una aplicación Java que supervisa el progreso de la tarea de proceso intensivo.
-   Ejecutar las aplicaciones Java.
-   Detener las aplicaciones Java.

En este tutorial se utilizará el problema del viajante para explicar la tarea de proceso intensivo. A continuación se mostrará un ejemplo de la aplicación Java que ejecuta la tarea de proceso intensivo:

![Solucionador del problema del viajante (TSP)](./media/virtual-machines-java-run-compute-intensive-task/WA_JavaTSPSolver.png)

A continuación se muestra un ejemplo de la aplicación Java que supervisa la tarea de proceso intensivo:

![Cliente del problema del viajante](./media/virtual-machines-java-run-compute-intensive-task/WA_JavaTSPClient.png)

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Para crear una máquina virtual
------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.  Haga clic sucesivamente en **New**, **Proceso**, **Máquina virtual** y, a continuación, en **From Gallery**.
3.  En el cuadro de diálogo **Virtual machine image select**, seleccione **JDK 7 Windows Server 2012**. Observe que **JDK 6 Windows Server 2012** está disponible si tiene aplicaciones heredadas que aún no están preparadas para ejecutarse en JDK 7.
4.  Haga clic en **Next**.
5.  En el cuadro de diálogo **Virtual machine configuration**:
    1.  Especifique un nombre para la máquina virtual.
    2.  Especifique el tamaño que se va a utilizar para la máquina virtual.
    3.  Escriba un nombre para el administrador en el campo **User Name**. Recuerde este nombre y la contraseña que va a escribir a continuación ya que los utilizará cuando inicie sesión de forma remota en la máquina virtual.
    4.  Escriba una contraseña en el campo **New password** y confírmela en el campo **Confirm**. Esta es la contraseña de la cuenta de administrador.
    5.  Haga clic en **Next**.

6.  En el cuadro de diálogo siguiente **Virtual machine configuration**:
    1.  En **Servicio en la nube**, utilice la opción predeterminada **Create a new cloud service**.
    2.  El valor de **Cloud service DNS name** debe ser único en cloudapp.net. Si es necesario, modifique este valor para que Azure indique que es único.
    3.  Especifique una región, un grupo de afinidad o una red virtual. En este tutorial, especifique una región como **Oeste de EE. UU**.
    4.  En **Cuenta de almacenamiento**, seleccione **Use an Automatically Generated Storage Account**.
    5.  En **Conjunto de disponibilidad**, seleccione **(None)**.
    6.  Haga clic en **Next**.

7.  En el cuadro de diálogo final **Virtual machine configuration**:
    1.  Acepte las entradas de extremo predeterminadas.
    2.  Haga clic en **Complete**.

Para iniciar sesión de manera remota en la máquina virtual
----------------------------------------------------------

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).
2.  Haga clic en **Máquinas virtuales**.
3.  Haga clic en el nombre de la máquina virtual en la que desea iniciar sesión.
4.  Haga clic en **Connect**.
5.  Siga las indicaciones, según sea necesario, para conectarse a la máquina virtual. Cuando se le pida el nombre y la contraseña del administrador, utilice los valores que proporcionó cuando creó la máquina virtual.

Tenga en cuenta que la funcionalidad del bus de servicio de Azure requiere que se instale el certificado Baltimore CyberTrust Root como parte de su almacén **cacerts** de JRE. Este certificado se incluye automáticamente en el JRE usado para este tutorial. Si no tiene este certificado en su almacén **cacerts** de JRE, consulte [Incorporación de un certificado al almacén de certificados CA de Java](../java-add-certificate-ca-store) para obtener más información acerca de cómo agregarlo (así como información acerca de la visualización de certificados en el almacén cacerts).

Creación de un espacio de nombres del bus de servicio
-----------------------------------------------------

Para comenzar a usar colas del bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un nombre de espacio de servicio:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.  En el panel de navegación izquierdo del Portal de administración, haga clic en **Bus de servicio, Access Control y Caché**.
3.  En el panel superior izquierdo del Portal de administración, haga clic en el nodo **Service Bus** y, a continuación, en el botón **New**.  
     ![Captura de pantalla del nodo Service Bus](./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg)
4.  En el cuadro de diálogo **Create a new Service Namespace**, escriba un **espacio de nombres** y, a continuación, para asegurarse de que es único, haga clic en el botón **Check Availability**.  
     ![Captura de pantalla de la creación de un nuevo espacio de nombres](./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg)
5.  Después de asegurarse de que el nombre del espacio de nombres está disponible, elija el país o región en el que debería alojarse el espacio de nombres y, a continuación, haga clic en el botón **Create Namespace**.

    El espacio de nombres que creó aparecerá a continuación en el Portal de administración y tardará un poco en activarse. Espere hasta que el estado sea **Active** antes de continuar con el paso siguiente.

Obtención de credenciales de administración predeterminadas para el espacio de nombres
--------------------------------------------------------------------------------------

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles:  
     ![Captura de pantalla de los espacios de nombres disponibles](./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg)
2.  Seleccione el espacio de nombres que acaba de crear en la lista desplegable:  
     ![Captura de pantalla de la lista de espacios de nombres](./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg)
3.  El panel **Properties**, situado a la derecha, mostrará las propiedades del nuevo espacio de nombres:
     ![Captura de pantalla del panel de propiedades](./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg)
4.  El valor de **Default Key** está oculto. Haga clic en el botón **View** para mostrar las credenciales de seguridad:
     ![Captura de pantalla de la clave predeterminada](./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg)
5.  Tome nota del valor de **Emisor predeterminado** y de **Clave predeterminada** cuando utilice la información siguiente para realizar las operaciones con el espacio de nombres.

Creación de una aplicación Java que realiza una tarea de proceso intensivo
--------------------------------------------------------------------------

1.  En nuestro equipo de desarrollo (que no tiene que ser la máquina virtual que ha creado), descargue el [SDK de Azure para Java](http://www.windowsazure.com/en-us/develop/java/).
2.  Cree una aplicación de consola Java utilizando el código de ejemplo que se encuentra al final de esta sección. Para este tutorial, utilizaremos **TSPSolver.java** como nombre de archivo de Java. Modifique los marcadores de posición **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** y **your\_service\_bus\_key** que utilizan respectivamente los valores **Espacio de nombres**, **Emisor predeterminado** y **Clave predeterminada** del bus de servicio.
3.  Después de la codificación, exporte la aplicación a un archivo Java (JAR) ejecutable y empaquete las bibliotecas requeridas en el JAR generado. Para este tutorial, utilizaremos **TSPSolver.java** como el nombre JAR generado.

    	// TSPSolver.java

    	import com.microsoft.windowsazure.services.core.Configuration;
    	import com.microsoft.windowsazure.services.core.ServiceException;
    	import com.microsoft.windowsazure.services.serviceBus.*;
    	import com.microsoft.windowsazure.services.serviceBus.models.*;
    	import java.io.*;
    	import java.text.DateFormat;
    	import java.text.SimpleDateFormat;
    	import java.util.ArrayList;
    	import java.util.Date;
    	import java.util.List;

    	public class TSPSolver {

        //  Valor que especifica con qué frecuencia se proporciona una actualización a la consola.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);               
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }
        
                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try 
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            } 
            catch (ServiceException se) 
            {
                throw se;
            }
            catch (Exception e) 
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Utilícelo como valor predeterminado si no se especifica ningún valor en la línea de comandos. 
                if (args.length != 0) 
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No tiene lugar ningún procesamiento salvo la creación de la cola.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No tiene lugar ningún procesamiento salvo la eliminación de la cola.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // No se crea ni se elimina una cola.
                    // Se asume que el valor pasado es el número de ciudades que hay que resolver.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\TSP\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            } 
            catch (ServiceException se) 
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }        
            catch (Exception e) 
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
          }

        }

Creación de una aplicación Java que supervisa el progreso de la tarea de proceso intensivo
------------------------------------------------------------------------------------------

1.  En el equipo de desarrollo, cree una aplicación de consola Java utilizando el código de ejemplo que se encuentra al final de esta sección. Para este tutorial, utilizaremos **TSPClient.java** como nombre de archivo de Java. Como antes, modifique los marcadores de posición **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** y **your\_service\_bus\_key** que utilizan respectivamente los valores de **Espacio de nombres**, **Emisor predeterminado** y **Clave predeterminada** del bus de servicio.
2.  Exporte la aplicación a un archivo Java (JAR) ejecutable y empaquete las bibliotecas requeridas en el JAR generado. Para este tutorial, utilizaremos **TSPClient.java** como el nombre JAR generado.

    	// TSPClient.java

    	import java.util.Date;
    	import java.text.DateFormat;
    	import java.text.SimpleDateFormat;
    	import com.microsoft.windowsazure.services.serviceBus.*;
    	import com.microsoft.windowsazure.services.serviceBus.models.*;
    	import com.microsoft.windowsazure.services.core.*;

    	public class TSPClient 
    	{

        public static void main(String[] args) 
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Utilícelo como valor predeterminado si no se especifica ningún valor en la línea de comandos. 
                    if (args.length != 0) 
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) 
         "minute." : waitMinutes + " minutes."; 

                    // Esta cola debe haberse creado anteriormente.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {                        

                            // Mostrar el mensaje de la cola.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No tiene lugar más procesamiento.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // La cola está vacía.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    } 

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

          }
        
        }

Ejecución de las aplicaciones Java
----------------------------------

Ejecute la aplicación de proceso intensivo, cree primero la cola y después resuelva el problema del viajante, que agregará la mejor ruta actual a la cola del bus de servicio. Mientras se está ejecutando esta aplicación (o después), ejecute el cliente para mostrar los resultados de la cola del bus de servicio.

### Ejecución de la aplicación de proceso intensivo

1.  Inicie sesión en la máquina virtual.
2.  Cree una carpeta en la que ejecutará la aplicación. Por ejemplo, **c:\\TSP**.
3.  Copie **TSPSolver.jar** en **c:\\TSP**,
4.  Cree un archivo denominado **c:\\TSP\\cities.txt** con el contenido siguiente:

         City_1, 1002.81, -1841.35
         City_2, -953.55, -229.6
         City_3, -1363.11, -1027.72
         City_4, -1884.47, -1616.16
         City_5, 1603.08, -1030.03
         City_6, -1555.58, 218.58
         City_7, 578.8, -12.87
         City_8, 1350.76, 77.79
         City_9, 293.36, -1820.01
         City_10, 1883.14, 1637.28
         City_11, -1271.41, -1670.5
         City_12, 1475.99, 225.35
         City_13, 1250.78, 379.98
         City_14, 1305.77, 569.75
         City_15, 230.77, 231.58
         City_16, -822.63, -544.68
         City_17, -817.54, -81.92
         City_18, 303.99, -1823.43
         City_19, 239.95, 1007.91
         City_20, -1302.92, 150.39
         City_21, -116.11, 1933.01
         City_22, 382.64, 835.09
         City_23, -580.28, 1040.04
         City_24, 205.55, -264.23
         City_25, -238.81, -576.48
         City_26, -1722.9, -909.65
         City_27, 445.22, 1427.28
         City_28, 513.17, 1828.72
         City_29, 1750.68, -1668.1
         City_30, 1705.09, -309.35
         City_31, -167.34, 1003.76
         City_32, -1162.85, -1674.33
         City_33, 1490.32, 821.04
         City_34, 1208.32, 1523.3
         City_35, 18.04, 1857.11
         City_36, 1852.46, 1647.75
         City_37, -167.44, -336.39
         City_38, 115.4, 0.2
         City_39, -66.96, 917.73
         City_40, 915.96, 474.1
         City_41, 140.03, 725.22
         City_42, -1582.68, 1608.88
         City_43, -567.51, 1253.83
         City_44, 1956.36, 830.92
         City_45, -233.38, 909.93
         City_46, -1750.45, 1940.76
         City_47, 405.81, 421.84
         City_48, 363.68, 768.21
         City_49, -120.3, -463.13
         City_50, 588.51, 679.33

5.  En el símbolo del sistema, cambie los directorios a c:\\TSP.
6.  Asegúrese de que la carpeta bin de JRE se encuentra en la variable de entorno PATH.
7.  Tendrá que crear la cola del bus de servicio antes de ejecutar las permutaciones del solucionador del TSP. Ejecute el comando siguiente para crear la cola del bus de servicio:

         java -jar TSPSolver.jar createqueue

8.  Ahora que se ha creado la cola, puede ejecutar las permutaciones del solucionador del TSP. Por ejemplo, ejecute el comando siguiente para ejecutar el solucionador para ocho ciudades.

         java -jar TSPSolver.jar 8

Si no especifica un número, se ejecutará en 10 ciudades. Cuando el solucionador encuentra las rutas más cortas actuales, las agregará a la cola.

> [WACOM.NOTE] Cuando mayor sea el número que especifique, más tiempo se ejecutará el solucionador. Por ejemplo, si la ejecución en 14 ciudades puede tardar varios minutos, la ejecución en 15 ciudades podría tardar varias horas. Si se aumenta a 16 o más ciudades, la ejecución podría tardar varios días (posiblemente semanas, meses y años). Esto se debe al rápido aumento del número de permutaciones evaluadas por el solucionador a medida que aumenta el número de ciudades.

### Ejecución de la supervisión de la aplicación cliente

1.  Inicie sesión en el equipo donde se va a ejecutar la aplicación cliente. No tiene que ser el mismo que ejecuta la aplicación **TSPSolver**, aunque podría serlo.
2.  Cree una carpeta en la que ejecutará la aplicación. Por ejemplo, **c:\\TSP**.
3.  Copie **TSPClient.jar** en **c:\\TSP**,
4.  Asegúrese de que la carpeta bin de JRE se encuentra en la variable de entorno PATH.
5.  En el símbolo del sistema, cambie los directorios a c:\\TSP.
6.  Ejecute el siguiente comando:

         java -jar TSPClient.jar

    Opcionalmente, especifique el número de minutos de suspensión entre la comprobación de la cola, pasando un argumento de línea de comandos. El período de suspensión predeterminado para comprobar la cola es de tres minutos, que se utiliza cuando no se pasa ningún argumento de línea de comandos a **TSPClient**. Si desea utilizar un valor diferente para el intervalo de suspensión, por ejemplo, un minuto, ejecute:

         java -jar TSPClient.jar 1

    El cliente se ejecutará hasta que vea un mensaje de cola de "Complete". Tenga en cuenta que si ejecuta varias ocurrencias del solucionador sin ejecutar el cliente, es posible que tenga que ejecutar el cliente varias veces para vaciar la cola por completo. Además, puede eliminar la cola y después volver a crearla. Para eliminar la cola, ejecute el siguiente comando **TSPSolver** (no **TSPClient**):

         java -jar TSPSolver.jar deletequeue

    El solucionador se ejecutará hasta que acabe de examinar todas las rutas.

Detención de las aplicaciones Java
----------------------------------

En ambas aplicaciones, el solucionador y el cliente, presione **Ctrl+C** para salir si desea acabar antes de la finalización normal.


[solver_output]: ./media/virtual-machines-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store


