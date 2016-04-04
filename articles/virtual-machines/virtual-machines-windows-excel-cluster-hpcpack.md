<properties
 pageTitle="Clúster de HPC Pack para Excel y SOA | Microsoft Azure"
 description="Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA a gran escala"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/19/2016"
 ms.author="danlep"/>

# Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA

En este artículo se muestra cómo implementar un clúster de Microsoft HPC Pack en los servicios de infraestructura de Azure (IaaS) con una plantilla de inicio rápido de Azure, o bien, de manera opcional, un script de implementación de Azure PowerShell. Usará imágenes de VM de Azure Marketplace diseñadas para ejecutar cargas de trabajo Microsoft Excel o de Arquitectura orientada a servicios (SOA) con HPC Pack. Puede usar el clúster para ejecutar servicios de SOA y Excel HPC sencillos desde un equipo cliente local. Los servicios de Excel HPC incluyen la descarga de libros de Excel y las funciones definidas por el usuario de Excel o UDF.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En un nivel alto el diagrama siguiente muestra el clúster de HPC Pack que creará.

![Clúster HPC con nodos que ejecutan cargas de trabajo de Excel][scenario]

## Requisitos previos

*   **Equipo cliente**: necesitará un equipo cliente basado en Windows para ejecutar el script de implementación del clúster de Azure PowerShell (si elige ese método de implementación) y para enviar trabajos de Excel y SOA de ejemplo al clúster.

*   **Suscripción de Azure**: si no tiene ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en un par de minutos.

*   **Cuota de núcleos**: tal vez tenga que aumentar la cuota de núcleos, especialmente si implementa varios nodos de clúster con tamaños de máquina virtual de múltiples núcleos. Si está usando una plantilla de inicio rápido de Azure, tenga en cuenta que la cuota de núcleos en el Administrador de recursos es por región de Azure y tal vez tenga que aumentar la cuota de una región específica. Consulte [Límites, cuotas y restricciones de suscripción de Azure](../azure-subscription-service-limits.md). Para aumentar una cuota, [abra una solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.

*   **Licencia de Microsoft Office**: si implementa nodos de proceso con una imagen de máquina virtual de Marketplace HPC Pack con Microsoft Excel, se instala una versión de evaluación de 30 días de Microsoft Excel Professional Plus 2013 en los nodos de proceso. Una vez que finalice el período de evaluación, deberá proporcionar una licencia de Microsoft Office válida para activar Excel y seguir ejecutando cargas de trabajo. Consulte [Activación de Excel](#excel-activation) que aparece más adelante en este artículo.


## Paso 1. Configuración e un clúster de HPC Pack en Azure

Le mostraremos dos formas de configurar el clúster: en primer lugar, mediante una plantilla de inicio rápido de Azure y el Portal de Azure; en segundo lugar, mediante un script de implementación de Azure PowerShell.


### Uso de una plantilla de inicio rápido
Use una plantilla de inicio rápido de Azure para implementar rápida y fácilmente un clúster de HPC Pack en el Portal de Azure. Al abrir la plantilla en el portal de vista previa, obtendrá una interfaz de usuario simple donde debe especificar la configuración del clúster. A continuación se muestran los pasos que se deben seguir.

>[AZURE.TIP]Si lo desea, use una [plantilla de Azure Markeplace](https://ms.portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que crea un clúster similar específicamente para cargas de trabajo de Excel. Los pasos son ligeramente distintos a los siguientes.

1.  Visite la página [Creación de plantilla de clúster HPC en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si lo desea, revise la información sobre la plantilla y el código de origen.

2.  Haga clic en **Implementar en Azure** para iniciar una implementación con la plantilla en el Portal de Azure.

    ![Implementación de plantillas en Azure][github]

3.  En el portal, siga estos pasos para especificar los parámetros de la plantilla de clúster HPC.

    a. En la página **Parámetros**, escriba los valores de los parámetros de la plantilla (haga clic en el icono junto a cada valor para obtener información de ayuda). En la pantalla siguiente se muestran valores de ejemplo. En este ejemplo se crea un nuevo clúster de HPC Pack denominado *hpc01* en el dominio *hpc.local* que consta de un nodo principal y dos nodos de proceso. Los nodos de proceso se crearán a partir de una imagen de VM de HPC Pack que incluye Microsoft Excel.

    ![Escribir parámetros][parameters]

    >[AZURE.NOTE]La máquina virtual del nodo principal se creará automáticamente a partir de la [imagen de Marketplace más reciente](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 en Windows Server 2012 R2. Actualmente, la imagen se basa en HPC Pack 2012 R2 Update 3.
    >
    >Las VM del nodo de proceso se crearán a partir de la imagen más reciente de la familia de nodos de proceso seleccionada. Seleccione la opción **ComputeNodeWithExcel** para la imagen del nodo de proceso de HPC Pack más reciente que incluye una versión de evaluación de Microsoft Excel Professional Plus 2013. Si quiere implementar un clúster para sesiones generales de SOA o para la descarga de UDF de Excel, elija la opción **ComputeNode** (sin Excel instalado).

    b. Elija la suscripción.

    c. Cree un nuevo grupo de recursos para el clúster, como *hpc01RG*.

    d. Elija una ubicación para el grupo de recursos, como Centro de EE. UU.

    e. En la página **Términos legales**, revise los términos. Si está de acuerdo, haga clic en **Crear**. Luego, cuando termine de establecer los valores de la plantilla, haga clic en **Crear**.

4.  Cuando se completa la implementación (normalmente tarda unos 30 minutos), exporte el archivo de certificado del clúster desde el nodo principal del clúster. En un paso posterior, este certificado público se importará en el equipo cliente para proporcionar la autenticación del lado servidor para el enlace HTTP seguro.

    a. Conéctese al nodo principal mediante el Escritorio remoto desde el Portal de Azure.

     ![Conexión al nodo principal][connect]

    b. Siga los procedimientos estándar en Administrador de certificados para exportar el certificado del nodo principal (que se encuentra en Cert:\\LocalMachine\\My) sin la clave privada. En este ejemplo, exporte *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exportación de certificados][cert]

### Uso del script de implementación de HPC Pack IaaS

El script de implementación de HPC Pack IaaS proporciona otra manera versátil de implementar un clúster de HPC Pack. Crea un clúster en el modelo de implementación clásica, siempre que la plantilla use el modelo de implementación de Azure Resource Manager. Además, el script es compatible con una suscripción en el servicio Azure Global o Azure China.

**Requisitos previos adicionales**

* **Azure PowerShell**: [instale y configure Azure PowerShell](../powershell-install-configure.md) (versión 0.8.10 o posterior) en el equipo cliente.

* **Script de implementación de HPC Pack IaaS**: descargue y desempaquete la versión más reciente del script en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Compruebe la versión del script ejecutando `New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.5.0 (o posterior) del script.

**Creación del archivo de configuración**

 El script de implementación de HPC Pack IaaS usa un archivo de configuración XML como entrada que describe la infraestructura del clúster HPC. Para implementar un clúster que consta de un nodo principal y 18 nodos de proceso creados a partir de la imagen del nodo de proceso que incluye Microsoft Excel, sustituya los valores para el entorno en el siguiente archivo de configuración de ejemplo. Para obtener más información sobre el archivo de configuración, vea el archivo Manual.rtf en la carpeta de script y [Creación de un clúster de HPC de Windows con el script de implementación de IaaS de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas sobre el archivo de configuración**

* El valor **VMName** del nodo principal **DEBE** ser exactamente igual que el de **ServiceName** o los trabajos de SOA no se ejecutarán.

* Asegúrese de especificar **EnableWebPortal** para que se genere y se exporte el certificado del nodo principal.

* El archivo especifica un script de PowerShell posterior a la configuración PostConfig.ps1 para configurar ciertos ajustes del nodo principal, como la cadena de conexión de almacenamiento de Azure, quitar el rol del nodo de proceso del nodo principal y poner en línea todos los nodos cuando se implementan. A continuación se muestra un script de ejemplo.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Ejecute el script**

1.  Abra la consola de PowerShell en el equipo cliente como administrador.

2.  Cambie el directorio a la carpeta de script (E:\\IaaSClusterScript en este ejemplo).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Ejecute el comando siguiente para implementar el clúster de HPC Pack. En este ejemplo se supone que el archivo de configuración se encuentra en E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

El script de implementación de HPC Pack se ejecutará durante algún tiempo. Una de las cosas que hará el script será exportar y descargar el certificado del clúster y guardarlo en la carpeta Documentos del usuario actual en el equipo cliente. El script generará un mensaje similar al siguiente: En un paso posterior podrá importar el certificado en el almacén de certificados adecuado.
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## Paso 2: Descarga de libros de Excel y ejecución de UDF desde un cliente local

### Activación de Excel

Cuando use la imagen de VM ComputeNodeWithExcel para las cargas de trabajo de producción, deberá brindar una clave de licencia de Microsoft Office válida para activar Excel en los nodos de proceso. De lo contrario, la versión de evaluación de Excel expirará en 30 días y la ejecución de libros de Excel presentará frecuentes errores con la excepción COMException (0x800AC472). Si esto sucede, inicie sesión en el nodo principal y ejecute `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` en todos los nodos de proceso de Excel a través del Administrador de clústeres de HPC.
    
Esto rearma a Excel durante otro período de 30 días de evaluación. Puede hacer esto 2 veces como máximo. Después de eso, deberá proporcionar una clave de licencia de Office válida.

La versión de Office Professional Plus 2013 que está instalada en esta imagen de VM es una edición por volumen con una clave de licencia por volumen genérica (GVLK) que se puede activar a través del Servicio de administración de claves (KMS), la activación basada en Active Directory (AD-BA) o la clave de activación múltiple (MAK). Si desea usar KMS/AD-BA, use un servidor de KMS existente o configure uno nuevo (que podría estar en el nodo principal) mediante el uso del paquete de licencias por volumen de Microsoft Office 2013. Luego, active la clave de host de KMS a través de Internet o por teléfono. Luego, ejecute `ospp.vbs` para definir el puerto y el servidor de KMS y activar Office en todos los nodos de proceso de Excel. Si desea usar MAK, primero ejecute `ospp.vbs` para ingresar la clave y, luego, active todos los nodos de proceso de Excel a través de Internet o por teléfono.

>[AZURE.NOTE]Las claves de producto comercial de Office Professional Plus 2013 no se pueden usar con esta imagen de VM. Si tiene claves y medios de instalación válidos para ediciones de Office o Excel distintas de esta edición por volumen de Office Professional Plus 2013, también puede desinstalar esta edición por volumen e instalar la edición que posea. De ese modo, el nodo de proceso de Excel reinstalado se puede capturar como imagen de VM personalizada para así usarlo en una implementación a escala.

### Descarga de libros de Excel

Siga estos pasos para descargar un libro de Excel para que se ejecute en el clúster de HPC Pack en Azure. Para ello, debe tener Excel 2010 o 2013 ya instalado en el equipo cliente.

1. Use uno de los métodos descritos en el paso 1 para implementar un clúster de HPC Pack con la imagen del nodo de proceso de Excel. Obtenga el archivo de certificado del clúster (.cer) y el nombre de usuario y la contraseña del clúster.

2. En el equipo cliente, importe el certificado del clúster que se encuentra en Cert: \\CurrentUser\\Root.

3. Asegúrese de que Excel está instalado. Cree un archivo Excel.exe.config con el siguiente contenido en la misma carpeta con Excel.exe en el equipo cliente. Esto garantiza que el complemento HPC Pack 2012 R2 Excel COM se cargue correctamente.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.	Descargue la [instalación de HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49922) completa e instale el cliente de HPC Pack o descargue e instale las [utilidades del cliente de HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) y el paquete redistribuible de Visual C++ 2010 adecuado para su equipo ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.	En este ejemplo, usamos un libro de Excel de ejemplo denominado ConvertiblePricing\_Complete.xlsb, que se puede descargar [aquí](https://www.microsoft.com/es-ES/download/details.aspx?id=2939).

6.	Copie el libro de Excel en una carpeta de trabajo como D:\\Excel\\Run.

7.	Abra el libro de Excel. En la cinta **Desarrollar**, haga clic en **Complementos COM** y confirme que el complemento COM de HPC Pack Excel se cargó correctamente, tal y como se muestra en la siguiente pantalla.

    ![Complemento de Excel para HPC Pack][addin]

8.	Modifique la macro HPCControlMacros de VBA en Excel cambiando las líneas comentadas, como se muestra en el script siguiente. Sustituya los valores adecuados para su entorno.

    ![Macro de Excel para HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	Copie el libro de Excel en un directorio de carga como D:\\Excel\\Upload, como se especifica en la constante HPC\_DependsFiles de la macro de VBA.

10.	Haga clic en el botón **Clúster** de la hoja de cálculo para ejecutar el libro en el clúster de Azure IaaS.

### Ejecución de UDF de Excel

Para ejecutar UDF de Excel, siga los pasos de 1 a 3 anteriores para configurar el equipo cliente. Para UDF de Excel, no es necesario que la aplicación de Excel esté instalada en los nodos de proceso, por lo que puede elegir una imagen de nodo de proceso normal en el paso 1 en lugar de la imagen del nodo de proceso con Excel.

>[AZURE.NOTE] Hay un límite de 34 caracteres en el cuadro de diálogo del conector de clúster de Excel 2010 y 2013. Si el nombre completo del clúster es más largo, por ejemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com, no cabrá en el cuadro de diálogo. La solución consiste en establecer una variable para todo el equipo; por ejemplo, establezca *CCP\_IAASHN* con el valor del nombre largo de clúster y escriba *% CCP\_IAASHN %* en el cuadro de diálogo como el nombre de nodo principal del clúster.

Una vez que el clúster esté implementado correctamente, siga estos pasos para ejecutar una UDF de Excel integrada de ejemplo. Para UDF personalizadas de Excel, consulte los [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para compilar las funciones XLL e implementarlas en el clúster de IaaS.

1.	Abra un nuevo libro de Excel. En la cinta **Desarrollar**, haga clic en **Complementos**. En el cuadro de diálogo, haga clic en **Examinar**, vaya a la carpeta %CCP\_HOME%Bin\\XLL32 y seleccione el archivo ClusterUDF32.xll de ejemplo. Si ClusterUDF32 no existe en el equipo cliente, puede copiarlo de la carpeta %CCP\_HOME%Bin\\XLL32 en el nodo principal.

    ![Selección de UDF][udf]

2.	Haga clic en **Archivo** > **Opciones** > **Avanzadas**. En **Fórmulas**, active **Permitir ejecución de funciones XLL definidas por el usuario en un clúster de proceso**. A continuación, haga clic en **Opciones** y escriba el nombre completo del clúster en **Nombre del nodo principal del clúster**. (Como se indicó anteriormente, este cuadro de entrada está limitado a 34 caracteres, por lo que un nombre de clúster largo no cabrá. Para nombres de clúster largos, puede usar variables para todo el equipo aquí).

    ![Configuración de UDF][options]

3.	Haga clic en la celda con el valor =XllGetComputerNameC() y presione Entrar para ejecutar el cálculo de UDF en el clúster de IaaS. La función simplemente recuperará el nombre del nodo de proceso en el que se ejecuta la UDF. Para la primera ejecución, un cuadro de diálogo de credenciales solicita el nombre de usuario y la contraseña para conectarse al clúster de IaaS.

    ![Ejecución de UDF][run]

    Cuando haya una gran cantidad de celdas para calcular, presione Alt-Mayús-Ctrl + F9 para ejecutar el cálculo en todas las celdas.

## Paso 3: Ejecución de una carga de trabajo de SOA desde un cliente local

Para ejecutar aplicaciones generales de SOA en el clúster de HPC Pack IaaS, use primero uno de los métodos descritos en el paso 1 para implementar el clúster de IaaS, con una imagen de nodo de proceso genérico (porque no necesitará Excel en los nodos de proceso). A continuación, siga estos pasos.

1. Después de recuperar el certificado del clúster, impórtelo en el equipo cliente en Cert: \\CurrentUser\\Root.

2. Instale el [SDK de HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49921) y las [utilidades de cliente de HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) para que pueda desarrollar y ejecutar aplicaciones de cliente SOA.

3. Descargue el [código de ejemplo](https://www.microsoft.com/download/details.aspx?id=41633) HelloWorldR2. Abra HelloWorldR2.sln en Visual Studio 2010 o 2012.

4. Genere primero el proyecto EchoService e implemente el servicio para el clúster de IaaS de la misma manera que se implementa en un clúster local. Para obtener pasos detallados, consulte el archivo Léame.doc en HelloWordR2. Modifique y compile HellWorldR2 y otros proyectos tal como se describe a continuación para generar las aplicaciones cliente de SOA que se ejecutan en un clúster de IaaS de Azure desde un equipo cliente local.

### Uso del enlace Http con colas de almacenamiento de Azure

Para usar el enlace Http con una cola de almacenamiento de Azure, haga algunos cambios en el código de ejemplo.

* Actualice el nombre del clúster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* O bien, use el valor TransportScheme predeterminado en SessionStartInfo o establézcalo explícitamente en Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Use el enlace predeterminado para BrokerClient

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    o establézcalo explícitamente con basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* De manera opcional, puede establecer la marca UseAzureQueue en true en SessionStartInfo. Si no se establece, se establecerá en true de forma predeterminada cuando el nombre del clúster tenga sufijos de dominio de Azure y el valor de TransportScheme sea Http.

    ```
    info.UseAzureQueue = true;
```

###Uso del enlace Http sin colas de almacenamiento de Azure

Para realizar esto, establezca explícitamente la marca UseAzureQueue en false en SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### Uso del enlace NetTcp

Para usar el enlace NetTcp, la configuración es similar que al conectarse a un clúster local. Deberá abrir algunos extremos en la VM del nodo principal. Por ejemplo, si usó el script de implementación de IaaS de HPC Pack para crear el clúster, establezca los puntos de conexión en el Portal de Azure clásico. Para ello, debe hacer lo siguiente.


1. Pare la VM.

2. Agregue los puertos TCP 9090, 9087, 9091, 9094 para Sesión, Agente, Trabajo de agente y Servicios de datos, respectivamente.

    ![Configuración de extremos][endpoint]

3. Inicie la máquina virtual.

La aplicación cliente de SOA no requiere cambios excepto modificar el nombre principal por el nombre completo del clúster de IaaS.

## Pasos siguientes

* Consulte [estos recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para obtener más información sobre cómo ejecutar las cargas de trabajo de Excel con HPC Pack.

* Consulte [Administración de servicios de SOA en Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para obtener más información sobre la implementación y administración de servicios de SOA con HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png

<!---HONumber=AcomDC_0323_2016-->