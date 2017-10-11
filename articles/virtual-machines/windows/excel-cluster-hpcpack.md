---
title: "Clúster de HPC Pack para Excel y SOA | Microsoft Docs"
description: "Introducción a la ejecución de cargas de trabajo de Excel y SOA a gran escala en un clúster de HPC Pack en Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 63babd94fdab15217cfb0757e4cd6efe458a628d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Introducción a la ejecución de cargas de trabajo de Excel y SOA en un clúster de HPC Pack en Azure
En este artículo se muestra cómo implementar un clúster de Microsoft HPC Pack 2012 R2 en máquinas virtuales de Azure con una plantilla de inicio rápido de Azure, o bien, de manera opcional, un script de implementación de Azure PowerShell. El clúster usa imágenes de VM de Azure Marketplace diseñadas para ejecutar cargas de trabajo de Microsoft Excel o de Arquitectura orientada a servicios (SOA) con HPC Pack. Puede usar el clúster para ejecutar servicios de SOA y Excel HPC desde un equipo cliente local. Los servicios de Excel HPC incluyen la descarga de libros de Excel y las funciones definidas por el usuario de Excel o UDF.

> [!IMPORTANT] 
> Este artículo se basa en características, plantillas y scripts de HPC Pack 2012 R2. Este escenario no se admite actualmente en HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

En un nivel alto, el diagrama siguiente muestra el clúster de HPC Pack que crea.

![Clúster HPC con nodos que ejecutan cargas de trabajo de Excel][scenario]

## <a name="prerequisites"></a>Requisitos previos
* **Equipo cliente** : necesita un equipo cliente basado en Windows para enviar ejemplos de trabajos de Excel y SOA al clúster. También necesitará un equipo Windows para ejecutar el script de implementación de clúster de Azure PowerShell (si elige ese método de pago).
* **Suscripción de Azure** : si no tiene ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en un par de minutos.
* **Cuota de núcleos** : tal vez tenga que aumentar la cuota de núcleos, especialmente si implementa varios nodos de clúster con tamaños de máquina virtual de múltiples núcleos. Si usa una plantilla de inicio rápido de Azure, la cuota de núcleos en Resource Manager es por región de Azure. En ese caso, puede que necesite aumentar la cuota en una región específica. Consulte [Límites, cuotas y restricciones de suscripción de Azure](../../azure-subscription-service-limits.md). Para aumentar una cuota, [abra una solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.
* **Licencia de Microsoft Office**: si implementa nodos de proceso con una imagen de máquina virtual de Marketplace HPC Pack 2012 R2 con Microsoft Excel, se instala una versión de evaluación de 30 días de Microsoft Excel Professional Plus 2013. Después del período de evaluación, debe proporcionar una licencia de Microsoft Office válida para activar Excel y seguir ejecutando cargas de trabajo. Consulte [Activación de Excel](#excel-activation) que aparece más adelante en este artículo. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Paso 1. Configuración e un clúster de HPC Pack en Azure
Mostramos 2 opciones para configurar el clúster de HPC Pack 2012 R2: primero, con una plantilla de inicio rápido de Azure y Azure Portal, y segundo, con un script de implementación de Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Opción 1. Uso de una plantilla de inicio rápido
Use una plantilla de inicio rápido de Azure para implementar rápidamente un clúster de HPC Pack en Azure Portal. Al abrir la plantilla en el portal, obtendrá una interfaz de usuario simple donde debe especifique la configuración del clúster. A continuación se muestran los pasos que se deben seguir. 

> [!TIP]
> Si lo desea, use una [plantilla de Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que crea un clúster similar específicamente para cargas de trabajo de Excel. Los pasos son ligeramente distintos a los siguientes.
> 
> 

1. Visite la página [Creación de plantilla de clúster HPC en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si lo desea, revise la información sobre la plantilla y el código de origen.
2. Haga clic en **Implementar en Azure** para iniciar una implementación con la plantilla en el Portal de Azure.
   
   ![Implementación de plantillas en Azure][github]
3. En el portal, siga estos pasos para especificar los parámetros de la plantilla de clúster HPC.
   
   a. En la página **Parámetros** , escriba o modifique los valores de los parámetros de la plantilla. (haga clic en el icono junto a cada valor para obtener información de ayuda). En la pantalla siguiente se muestran valores de ejemplo. En este ejemplo se crea un clúster denominado *hpc01* en el dominio *hpc.local* que consta de un nodo principal y 2 nodos de proceso. Los nodos de proceso se crean a partir de una imagen de VM de HPC Pack que incluye Microsoft Excel.
   
   ![Escribir parámetros][parameters-new-portal]
   
   > [!NOTE]
   > La VM del nodo principal se crea automáticamente a partir de la [imagen de Marketplace más reciente](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 en Windows Server 2012 R2. Actualmente, la imagen se basa en HPC Pack 2012 R2 Update 3.
   > 
   > Las VM del nodo de proceso se crean a partir de la imagen más reciente de la familia de nodos de proceso seleccionada. Seleccione la opción **ComputeNodeWithExcel** para la imagen del nodo de proceso de HPC Pack más reciente que incluye una versión de evaluación de Microsoft Excel Professional Plus 2013. Para implementar un clúster para sesiones generales de SOA o para la descarga de UDF de Excel, elija la opción **ComputeNode** (sin Excel instalado).
   > 
   > 
   
   b. Elija la suscripción.
   
   c. Cree un grupo de recursos para el clúster, como *hpc01RG*.
   
   d. Elija una ubicación para el grupo de recursos, como Centro de EE. UU.
   
   e. En la página **Términos legales** , revise los términos. Si está de acuerdo, haga clic en **Comprar**. Luego, cuando termine de establecer los valores de la plantilla, haga clic en **Crear**.
4. Cuando se completa la implementación (normalmente tarda unos 30 minutos), exporte el archivo de certificado del clúster desde el nodo principal del clúster. En un paso posterior, importa este certificado público en el equipo cliente para proporcionar la autenticación del lado servidor para el enlace HTTP seguro.
   
   a. En Azure Portal, vaya al panel, seleccione el nodo principal y haga clic en **Conectar** en la parte superior de la página para conectarse mediante Escritorio remoto.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Siga los procedimientos estándar en Administrador de certificados para exportar el certificado del nodo principal (que se encuentra en Cert:\LocalMachine\My) sin la clave privada. En este ejemplo, exporte *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Exportación de certificados][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opción 2. Uso del script de implementación de HPC Pack IaaS
El script de implementación de HPC Pack IaaS proporciona otra manera versátil de implementar un clúster de HPC Pack. Crea un clúster en el modelo de implementación clásica, siempre que la plantilla use el modelo de implementación de Azure Resource Manager. Además, el script es compatible con una suscripción en el servicio Azure Global o Azure China.

**Requisitos previos adicionales**

* **Azure PowerShell** - [instale y configure Azure PowerShell](/powershell/azure/overview) (versión 0.8.10 o posterior) en el equipo cliente.
* **Script de implementación de HPC Pack IaaS** : descargue y desempaquete la versión más reciente del script en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Compruebe la versión del script ejecutando `New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.5.0 (o posterior) del script.

**Creación del archivo de configuración**

 El script de implementación de HPC Pack IaaS usa un archivo de configuración XML como entrada que describe la infraestructura del clúster HPC. Para implementar un clúster que consta de un nodo principal y 18 nodos de proceso creados a partir de la imagen del nodo de proceso que incluye Microsoft Excel, sustituya los valores para el entorno en el siguiente archivo de configuración de ejemplo. Para más información sobre el archivo de configuración, vea el archivo Manual.rtf en la carpeta de script y [Creación de un clúster de HPC de Windows con el script de implementación HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

* El valor **VMName** del nodo principal **DEBE** ser igual que el valor de **ServiceName** o los trabajos de SOA no se ejecutarán.
* Asegúrese de especificar **EnableWebPortal** para que se genere y se exporte el certificado del nodo principal.
* El archivo especifica un script de PowerShell posterior a la configuración, PostConfig.ps1, que se ejecuta en el nodo principal. El ejemplo de script siguiente configura la cadena de conexión de Almacenamiento de Azure, quita el rol del nodo de proceso del nodo principal y pone en línea todos los nodos cuando están implementados. 

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

1. Abra la consola de PowerShell en el equipo cliente como administrador.
2. Cambie el directorio a la carpeta de script (E:\IaaSClusterScript en este ejemplo).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Ejecute el comando siguiente para implementar el clúster de HPC Pack. En este ejemplo se supone que el archivo de configuración se encuentra en E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

El script de implementación de HPC Pack se ejecuta durante algún tiempo. Una de las cosas que hace el script es exportar y descargar el certificado del clúster y guardarlo en la carpeta Documentos del usuario actual en el equipo cliente. El script genera un mensaje similar al siguiente. En un paso posterior, importa el certificado en el almacén de certificados adecuado.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Paso 2: Descarga de libros de Excel y ejecución de UDF desde un cliente local
### <a name="excel-activation"></a>Activación de Excel
Cuando usa la imagen de VM ComputeNodeWithExcel para las cargas de trabajo de producción, debe brindar una clave de licencia de Microsoft Office válida para activar Excel en los nodos de proceso. De lo contrario, la versión de evaluación de Excel expirará después 30 días y la ejecución de libros de Excel presentará errores con la excepción COMException (0x800AC472). 

Puede rearmar Excel para otro período de evaluación de 30 días: inicie sesión en el nodo principal y ejecute `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` en todos los nodos de proceso de Excel a través del Administración de clústeres de HPC. Puede rearmar un máximo de dos veces. Después de eso, debe proporcionar una clave de licencia de Office válida.

La versión de Office Professional Plus 2013 instalada en la imagen de la VM es una edición por volumen con una clave de licencia por volumen genérica (GVLK). Puede activarla mediante el Servicio de administración de claves (KMS)/activación basada en Active Directory (AD-BA) o la clave de activación múltiple (MAK). 

    * Para usar KMS/AD-BA, emplee un servidor de KMS existente o configure uno nuevo mediante el paquete de licencias por volumen de Microsoft Office 2013. (Si lo desea, configure el servidor en el nodo principal). Luego, active la clave de host de KMS a través de Internet o por teléfono. Luego, ejecute `ospp.vbs` para definir el puerto y el servidor de KMS y activar Office en todos los nodos de proceso de Excel. 

    * Para usar MAK, primero ejecute `ospp.vbs` para insertar la clave y, luego, active todos los nodos de proceso de Excel a través de Internet o por teléfono. 

> [!NOTE]
> Las claves de producto comercial de Office Professional Plus 2013 no se pueden usar con esta imagen de VM. Si tiene claves y medios de instalación válidos para ediciones de Office o Excel distintas de esta edición por volumen de Office Professional Plus 2013, puede usarlas. En primer lugar, desinstale esta edición por volumen e instale la edición que tiene. De ese modo, el nodo de proceso de Excel reinstalado se puede capturar como imagen de VM personalizada para así usarlo en una implementación a escala.
> 
> 

### <a name="offload-excel-workbooks"></a>Descarga de libros de Excel
Siga estos pasos para descargar un libro de Excel para que se ejecute en el clúster de HPC Pack en Azure. Para ello, debe tener Excel 2010 o 2013 ya instalado en el equipo cliente.

1. Use una de las opciones descritas en el paso 1 para implementar un clúster de HPC Pack con la imagen del nodo de proceso de Excel. Obtenga el archivo de certificado del clúster (.cer) y el nombre de usuario y la contraseña del clúster.
2. En el equipo cliente, importe el certificado del clúster que se encuentra en Cert: \CurrentUser\Root.
3. Asegúrese de que Excel está instalado. Cree un archivo Excel.exe.config con el siguiente contenido en la misma carpeta que Excel.exe en el equipo cliente. Este paso garantiza que el complemento HPC Pack 2012 R2 Excel COM se cargue correctamente.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Configure el cliente para enviar trabajos al clúster de HPC Pack. Una opción es descargar la [instalación completa de HPC Pack 2012 R2 actualización 3](http://www.microsoft.com/download/details.aspx?id=49922) e instale el cliente de HPC Pack. De manera alternativa, descargue e instale las [utilidades de cliente de HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) y el paquete redistribuible de Visual C++ 2010 adecuado para su equipo ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).
5. En este ejemplo, usamos un libro de Excel de ejemplo denominado ConvertiblePricing_Complete.xlsb. Puede descargarlas [aquí](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Copie el libro de Excel en una carpeta de trabajo como D:\Excel\Run.
7. Abra el libro de Excel. En la cinta **Desarrollar**, haga clic en **Complementos COM** y confirme que el complemento COM de HPC Pack Excel se cargó correctamente.
   
   ![Complemento de Excel para HPC Pack][addin]
8. Modifique la macro HPCControlMacros de VBA en Excel cambiando las líneas comentadas, como se muestra en el script siguiente. Sustituya los valores adecuados para su entorno.
   
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
9. Copie el libro de Excel para en un directorio de carga, como D:\Excel\Upload. Este directorio se especifica en la constante HPC_DependsFiles de la macro de VBA.
10. Para ejecutar el libro en el clúster en Azure, haga clic en el botón **Clúster** que aparece en la hoja de cálculo.

### <a name="run-excel-udfs"></a>Ejecución de UDF de Excel
Para ejecutar UDF de Excel, siga los pasos de 1 a 3 anteriores para configurar el equipo cliente. En el caso de los UDF de Excel, no necesita tener instalada la aplicación de Excel en los nodos de proceso. Por lo tanto, cuando crea los nodos de proceso del clúster, puede elegir una imagen de nodo de proceso normal en lugar de la imagen de nodo de proceso con Excel.

> [!NOTE]
> Hay un límite de 34 caracteres en el cuadro de diálogo del conector de clúster de Excel 2010 y 2013. Este cuadro de diálogo se usa para especificar el clúster que ejecuta los UDF. Si el nombre completo del clúster es más largo (por ejemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com), no cabe en el cuadro de diálogo. La solución alternativa es establecer una variable a nivel de máquina, como *CCP_IAASHN*, con el valor del nombre largo del clúster. Luego, escriba *%CCP_IAASHN%* en el cuadro de diálogo como el nombre del nodo principal del clúster. 
> 
> 

Una vez que el clúster esté implementado correctamente, siga estos pasos para ejecutar una UDF de Excel integrada de ejemplo. Para UDF personalizadas de Excel, consulte los [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para compilar las funciones XLL e implementarlas en el clúster de IaaS.

1. Abra un nuevo libro de Excel. En la cinta **Desarrollar**, haga clic en **Complementos**. En el cuadro de diálogo, haga clic en **Examinar**, vaya a la carpeta %CCP_HOME%Bin\XLL32 y seleccione el archivo ClusterUDF32.xll de ejemplo. Si ClusterUDF32 no existe en el equipo cliente, cópielo de la carpeta %CCP_HOME%Bin\XLL32 en el nodo principal.
   
   ![Selección de UDF][udf]
2. Haga clic en **Archivo** > **Opciones** > **Avanzadas**. En **Fórmulas**, active **Allow user-defined XLL functions to run a compute cluster** (Permitir ejecución de funciones XLL definidas por el usuario en un clúster de proceso). A continuación, haga clic en **Opciones** y escriba el nombre completo del clúster en **Cluster head node name** (Nombre del nodo principal del clúster). (Como se indicó anteriormente, este cuadro de entrada está limitado a 34 caracteres, por lo que un nombre de clúster largo no cabrá. Para un nombre de clúster largo, aquí puede usar una variable a nivel de la máquina).
   
   ![Configuración de UDF][options]
3. Para ejecutar el cálculo de UDF en el clúster, haga clic en la celda con el valor =XllGetComputerNameC() y presione Entrar. La función simplemente recupera el nombre del nodo de proceso en el que se ejecuta el UDF. Para la primera ejecución, un cuadro de diálogo de credenciales solicita el nombre de usuario y la contraseña para conectarse al clúster de IaaS.
   
   ![Ejecución de UDF][run]
   
   Cuando haya una gran cantidad de celdas para calcular, presione Alt+Mayús+Ctrl+F9 para ejecutar el cálculo en todas las celdas.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Paso 3: Ejecución de una carga de trabajo de SOA desde un cliente local
Para ejecutar aplicaciones generales de SOA en el clúster de HPC Pack IaaS, use primero uno de los métodos que aparecen en el paso 1 para implementar el clúster. En este caso, especifique una imagen de nodo de proceso genérica porque no necesita tener instalado Excel en los nodos de proceso. A continuación, siga estos pasos.

1. Después de recuperar el certificado del clúster, impórtelo en el equipo cliente en Cert: \CurrentUser\Root.
2. Instale el [SDK de HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49921) y las utilidades de cliente de [HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923). Estas herramientas le permiten desarrollar y ejecutar aplicaciones cliente de SOA.
3. Descargue el [código de ejemplo](https://www.microsoft.com/download/details.aspx?id=41633)de HelloWorldR2. Abra HelloWorldR2.sln en Visual Studio 2010 o 2012. (Este ejemplo no es compatible actualmente con las versiones más recientes de Visual Studio).
4. Primero compile el proyecto EchoService. Luego, implemente el servicio en el clúster de IaaS de la misma manera que se implementa en un clúster local. Para obtener pasos detallados, consulte el archivo Léame.doc en HelloWordR2. Modifique y compile HelloWorldR2 y otros proyectos tal como se describe en la sección siguiente para generar las aplicaciones cliente de SOA que se ejecutan en un clúster de IaaS de Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Uso del enlace Http con colas de almacenamiento de Azure
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

### <a name="use-http-binding-without-azure-storage-queue"></a>Uso del enlace Http sin colas de almacenamiento de Azure
Para usar el enlace Http sin una cola de Almacenamiento de Azure, establezca de manera explícita la marca UseAzureQueue en false en SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Uso del enlace NetTcp
Para usar el enlace NetTcp, la configuración es similar que al conectarse a un clúster local. Debe abrir algunos puntos de conexión en la VM del nodo principal. Por ejemplo, si usó el script de implementación de HPC Pack IaaS para crear el clúster, establezca los puntos de conexión en Azure Portal como se indica a continuación.

1. Pare la VM.
2. Agregue los puertos TCP 9090, 9087, 9091, 9094 para Sesión, Agente, Trabajo de agente y Servicios de datos, respectivamente.
   
    ![Configuración de extremos][endpoint-new-portal]
3. Inicie la máquina virtual.

La aplicación cliente de SOA no requiere cambios excepto modificar el nombre principal por el nombre completo del clúster de IaaS.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [estos recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para obtener más información sobre cómo ejecutar las cargas de trabajo de Excel con HPC Pack.
* Consulte [Administración de servicios de SOA en Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para obtener más información sobre la implementación y administración de servicios de SOA con HPC Pack.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
