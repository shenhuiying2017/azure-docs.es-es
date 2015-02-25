<properties urlDisplayName="Autoscaling" pageTitle="Uso del bloque de autoescala de la aplicación (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Aprenda a utilizar la autoescala de la aplicación en Azure. Los ejemplos de código están escritos en C# y utilizan la API .NET." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="How to Use the Autoscaling Application Block" authors="rasquill" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/14/2014" ms.author="rasquill" />







# Uso del bloque de autoescala de la aplicación

En esta guía se demuestra cómo llevar a cabo supuestos comunes usando el bloque de autoescala de la aplicación desde el [Paquete de integración de Microsoft Enterprise Library 5.0 para Azure][]. Los ejemplos están escritos en C\# y utilizan la API .NET. Los supuestos abordados incluyen **hospedaje del bloque**, **uso de reglas de restricción** y **uso de reglas reactivas**. Para obtener más información sobre el bloque de autoescala de la aplicación, consulte la sección [Pasos siguientes][].

## Tabla de contenido

[¿Qué es el bloque de autoescala de la aplicación?][]   
 [Conceptos][]   
 [Recopilación de datos del contador de rendimiento desde su aplicación de Azure de destino][]   
 [Instalación de una aplicación host para el bloque de autoescala de la aplicación][]   
 [Creación de una instancia y ejecución de la autoescala][] [Definición de su modelo de servicio][]   
 [Definición de sus reglas de autoescala][]   
 [Configuración del bloque de autoescala de la aplicación][]   
 [Pasos siguientes][]

## <a id="WhatIs"> </a>¿Qué es el bloque de autoescala de la aplicación?

El bloque de autoescala de la aplicación puede escalar automáticamente su aplicación de Azure según las reglas que define específicamente para su aplicación. Puede usar estas reglas para ayudar a su aplicación de Azure a mantener su rendimiento en respuesta a los cambios en su carga de trabajo y, al mismo tiempo, controlar los costes asociados al hospedaje de su aplicación en Azure. Además del escalado mediante el aumento o la disminución de la cantidad de instancias de rol en su aplicación, el bloque le permite también usar otras acciones de escalado, como limitar cierta funcionalidad dentro de su aplicación o usar acciones definidas por el cliente.

Puede optar por hospedar el bloque en un rol de Azure o en una aplicación local. 

El bloque de autoescala de la aplicación forma parte del [Paquete de integración de Microsoft Enterprise Library 5.0 para Azure][].

## <a id="Concepts"> </a>Conceptos

En el siguiente diagrama, la línea verde muestra un gráfico de la cantidad de instancias en ejecución de un rol de Azure en dos días. La cantidad de instancias cambia automáticamente con el paso del tiempo en respuesta a un conjunto de reglas de autoescala.

![diagram of sample autoscaling](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

El bloque usa dos tipos de reglas para definir el comportamiento de autoescala para su aplicación:

-   **Reglas de restricción:** Para establecer los límites superior e inferior en la cantidad de instancias, por ejemplo, entre 8:00 y 10:00 cada mañana, deseará como mínimo cuatro y como máximo seis instancias,así que utiliza una **regla de restricción**. En el diagrama, las líneas roja y azul representan reglas de restricción. Por ejemplo, en el punto **A** del diagrama, la cantidad mínima de instancias de rol asciende de dos a cuatro, a fin de acomodar el aumento anticipado en la carga de trabajo de la aplicación en este momento. En el punto **B** del diagrama, se evita que la cantidad de instancias de rol aumente por encima de cinco a fin de controlar los costes de ejecución de la aplicación.

-   **Reglas reactivas:** Para permitir que la cantidad de instancias de rol cambie en respuesta a cambios impredecibles en la demanda, puede usar las **reglas reactivas**. En el punto **C** del diagrama, el bloque reduce automáticamente la cantidad de instancias de rol, de cuatro a tres, en respuesta a una reducción en la carga de trabajo. En el punto **D**, el bloque detecta un aumento en la carga de trabajo y aumenta automáticamente la cantidad de instancias de rol que se ejecutan de tres a cuatro.

El bloque almacena su configuración en dos almacenes:

-   **Almacén de reglas:** El almacén de reglas mantiene la configuración de su negocio;una lista de todas las reglas de autoescala que ha definido para su aplicación de Azure. Este almacén es generalmente un archivo XML que se ubica donde el bloque de autoescala de la aplicación puede leerlo, por ejemplo, en el almacenamiento de blobs de Azure o en un archivo local.

-   **Almacén de información de servicio:** El almacén de información de servicio almacenasu configuración funcional, que es el modelo de servicio de su aplicación de Azure. Este modelo de servicio incluye toda la información acerca de su aplicación de Azure (como los nombres de rol y los detalles de la cuenta de almacenamiento) que el bloque necesita para poder recopilar puntos de datos desde la aplicación de Azure de destino y realizar operaciones de escalado.

## <a id="PerfCounter"> </a>Recopilación de datos del contador de rendimiento desde su aplicación de Azure de destino

Las reglas reactivas pueden usar los datos del contador de rendimiento de los roles como parte de la definición de la regla. Por ejemplo, una regla reactiva puede supervisar el uso de la CPU de un rol de Azure para determinar si el bloque debe iniciar una operación de escalado. El bloque lee los datos del contador de rendimiento de la tabla de Diagnósticos de Azure llamada **WADPerformanceCountersTable** en el almacenamiento de Azure.

De manera predeterminada, Azure no escribe datos del contador de rendimiento en la tabla de Diagnósticos de Azure en el almacenamiento de Azure. Por lo tanto, debe modificar los roles a partir de los cuales necesita recopilar datos del contador de rendimiento para guardar estos datos. Para obtener detalles sobre la habilitación de los contadores de rendimiento en su aplicación, vea [Uso de contadores de rendimiento en Azure][].

## <a id="CreateHost"> </a>Instalación de una aplicación host para el bloque de autoescala de la aplicación

Puede hospedar el bloque de autoescala de la aplicación en un rol Azure o en una aplicación local. El bloque de autoescala de la aplicación se hospeda generalmente en una aplicación separada de la aplicación destino que desea escalar automáticamente. Esta sección proporciona instrucciones para configurar su aplicación host.

### Obtener el paquete NuGet del bloque de autoescala de la aplicación

Antes de usar el bloque de autoescala de la aplicación en su proyecto de Visual Studio, necesitará obtener los archivos binarios del bloque de autoescala de la aplicación y agregarles referencias en su proyecto. La extensión NuGet Visual Studio facilita la instalación y actualización de las bibliotecas y herramientas en Visual Studio y Visual Web Developer. El paquete NuGet del bloque de autoescala de la aplicación es la manera más sencilla de obtener las API del bloque. Para obtener más información sobre **NuGet**, junto con la instalación y el uso de la extensión **NuGet** de Visual Studio, vea el sitio web de [NuGet][].

Después de instalar el Administrador de paquetes NuGet, para instalar el paquete de autoescala de NuGet en su aplicación, realice lo siguiente:

1.  Abra la ventana **Consola del administrador de paquetes NuGet**. En el menú **Herramientas**, seleccione **Administrador de paquetes de la biblioteca** y, a continuación, **Consola del administrador de paquetes**.

2.  Escriba el siguiente comando en la ventana Consola del administrador de paquetes NuGet:

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

La instalación del paquete NuGet actualiza su proyecto con todos los ensamblados y las referencias que necesita para usar el bloque de autoescala de la aplicación. Su proyecto incluye ahora los archivos de esquema XML para las definiciones de la regla de autoescala y la información del servicio de autoescala. El proyecto incluye también ahora un archivo léame que contiene información importante sobre el bloque de autoescala de la aplicación:

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### Establecer el marco de destino en .NET Framework 4

El proyecto debe dirigirse a .NET Framework 4. Para cambiar o comprobar el marco de trabajo de destino:

1.  En el Explorador de soluciones, haga clic con el botón secundario en el nombre del proyecto y seleccione **Propiedades**.

2.  En la pestaña **Aplicación** de la ventana Propiedades, asegúrese de que el marco de destino esté establecido en **.NET Framework 4**.

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### Agregar referencias de espacio de nombres

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de cualquier archivo de C# en el que desee obtener acceso al bloque de autoescala de la aplicación mediante programación:

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>Creación de instancias y ejecución de la autoescala

Use el método **IServiceLocator.GetInstance** para crear una instancia de
autoescala y después llame al método **Autoscaler.Start** para que ejecute la
**autoescala**.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>Definición de su modelo de servicio

Por lo general, el modelo de servicio (una descripción de su entorno de Azure que incluye información sobre suscripciones, servicios hospedados, roles y cuentas de almacenamiento) se almacena en un archivo XML. Puede encontrar una copia del esquema de este archivo XML en el archivo **AutoscalingServiceModel.xsd** de su proyecto. En Visual Studio, este esquema ofrece Intellisense y validación cuando se edita el archivo XML del modelo de servicio.

Cree un archivo XML nuevo llamado **services.xml** en su proyecto.

En Visual Studio, debe asegurarse de copiar el archivo del modelo de servicio
en la carpeta de salida. Para ello, siga estos pasos:

1.  Haga clic con el botón secundario en el archivo y seleccione **Propiedades**.

2.  En el panel Propiedades, establezca el valor **Copiar en el directorio de salida**
    en **Copiar siempre**.

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	El siguiente ejemplo de código muestra un modelo de servicio de ejemplo en un archivo **services.xml**:

    	<?xml version="1.0" encoding="utf-8" ?>
    	<serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
      	<subscriptions>
        	<subscription name="[subscriptionname]"
                      	certificateThumbprint="[managementcertificatethumbprint]"
                      	subscriptionId="[subscriptionid]"
                      	certificateStoreLocation="CurrentUser"
                      	certificateStoreName="My">
          	<services>
            	<service dnsPrefix="[hostedservicednsprefix]" slot="Staging">
              	<roles>
                	<role alias="AutoscalingApplicationRole"
                      	roleName="[targetrolename]"
                      	wadStorageAccountName="targetstorage"/>
              	</roles>
            	</service>
          	</services>
          	<storageAccounts>
            	<storageAccount alias="targetstorage"
              	connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
            	</storageAccount>
          	</storageAccounts>
        	</subscription>
      	</subscriptions>
    	</serviceModel>

Debe reemplazar los valores de los corchetes por valores específicos para
su entorno y aplicación de destino. Para encontrar esos valores,
tiene que iniciar sesión en el [Portal de administración de Azure][].

Inicie sesión en el Portal de administración.

-   **[subscriptionname]:** Elija un nombre descriptivo para referirse a la suscripción de Azure que contiene la aplicación en la cual desea usar la autoescala.

-   **[subscriptionid]:** El ID único de la suscripción de Azure que contiene la aplicación en la cual desea usar la autoescala.

    1.  En el Portal de administración de Azure, haga clic en
        **Servicios en la nube**.

    2.  En la lista de Servicios en la nube, haga clic en el servicio que hospeda la aplicación en la cual desea usar la autoescala. El panel Vista rápida de la derecha mostrará el **Id. de suscripción**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]:** El prefijo DNS del servicio hospedado en el que desea usar la autoescala.

    1.  En el Portal de administración de Azure, haga clic en **Servicios en la nube**. 
    2.  En la lista de Servicios en la nube, busque el servicio que hospeda la aplicación en la cual desea usar la autoescala. El nombre del servicio en la nube es el **Prefijo DNS**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]:** El nombre del rol que es el destino de sus reglas de autoescala.

    1.  En el Portal de administración de Azure, haga clic en **Servicios en la nube**.

    2.  En la lista de Servicios en la nube, haga clic en el servicio que hospeda la aplicación en la cual desea usar la autoescala y, a continuación, haga clic en **Instancias**. La columna **Rol* muestra el nombre del rol de destino.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** y **[storageaccountkey]:** El nombre de la cuenta de almacenamiento de Azure que está usando para su aplicación de Azure de destino.

    1.  En el Portal de administración de Azure, haga clic en **Almacenamiento**.

    2.  En la lista de cuentas de almacenamiento, seleccione la cuenta de almacenamiento que está usando. La columna **Nombre** muestra el **nombre**.

    3.  Haga clic en el botón **Administrar claves** en la parte inferior de la pantalla para obtener la clave de acceso primaria.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** La **huella digital** del certificado de administración que el bloque usa para proteger las solicitudes de escalado de la aplicación de destino.

    1.  En el Portal de administración de Azure, haga clic en **Configuración**.

    2.  La columna **Huella digital** muestra la **huella digital**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

Para obtener más información sobre el contenido del archivo de servicio, consulte [Almacenamiento de sus datos de información del servicio][].

## <a id="DefineAutoscalingRules"> </a>Definición de sus reglas de autoescala

Generalmente, las reglas de autoescala que controlan la cantidad de instancias de rol en su aplicación de destino se almacenan en un archivo XML. Puede encontrar una copia del esquema de este archivo XML en el archivo **AutoscalingRules.xsd** de su proyecto. En Visual Studio, este esquema ofrece Intellisense y validación cuando se edita el archivo XML.

Cree un archivo XML nuevo llamado **rules.xml** en su proyecto.

En Visual Studio, debe asegurarse de copiar el archivo de reglas en la carpeta de salida. Para ello, siga estos pasos:

1.  Haga clic con el botón secundario en el archivo y seleccione **Propiedades**.

2.  En el panel Propiedades, establezca el valor **Copiar en el directorio de salida** en **Copiar siempre**.

El siguiente código de ejemplo muestra un conjunto de reglas de ejemplo en un archivo **rules.xml**
:

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

En este ejemplo hay tres reglas de autoescala (una **regla de restricción** y dos **reglas reactivas**) que operan en un destino llamado **AutoscalingApplicationRole** que es el alias de un rol definido en el **modelo de servicio**:

-   La regla de restricción siempre está activa y establece la cantidad mínima de instancias de rol en dos y la cantidad máxima en seis.

-   Ambas reglas reactivas usan un **operando** llamado **WebRoleA_CPU_Avg_5m** que calcula el uso promedio de la CPU en los últimos cinco minutos para un rol de Azure llamado **AutoscalingApplicationRole**. Este rol se define en el **modelo de servicio**.

-   La regla reactiva llamada **ScaleUpOnHighUtilization** aumenta el recuento de instancias del rol de destino en uno si el uso promedio de CPU en los últimos cinco minutos ha sido superior o igual al 60 %.

-   La regla reactiva llamada **ScaleDownOnLowUtilization** disminuye el recuento de instancias del rol de destino en uno si el uso promedio de CPU en los últimos cinco minutos ha sido inferior al 60 %.

## <a id="Configure"> </a>Configuración del bloque de autoescala de la aplicación

Después de haber definido su modelo de servicio y las reglas de autoescala, debe configurar el bloque de autoescala de la aplicación para usarlas. Esta información de configuración funcional se almacena en el archivo de configuración de la aplicación.

De manera predeterminada, el bloque de autoescala de la aplicación espera que las reglas de autoescala y el modelo de servicio se almacenen en los blobs de Azure. En este ejemplo, podrá configurar el bloque para que las cargue desde el sistema de archivos local.

### Configurar el bloque de autoescala de la aplicación en la aplicación host

1.  Haga clic con el botón secundario en el archivo **App.config** en el Explorador de soluciones y, a continuación, haga clic en **Editar archivo de configuración**.

2.  En el menú **Bloques**, haga clic en **Agrear configuración de autoescala**:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  Expanda **Configuración de autoescala** y después haga clic en los puntos suspensivos (...)     junto a **Cuenta de almacenamiento del almacén de puntos de datos**, agregue el **Nombre de cuenta** y **Clave de cuenta** de la cuenta de almacenamiento de Azure, donde el bloque almacenará los puntos de datos que recopila (consulte [Definición de su modelo de servicio ][]si no está seguro de dónde puede encontrar estos valores) y, a continuación, haga clic en **Aceptar**:  

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  Expanda la sección **Configuración de autoescala** para mostrar las secciones **Almacén de reglas** y **Almacén de información de servicio**. De manera predeterminada, están configuradas para usar el almacenamiento de blobs de Azure:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  Haga clic en el signo más (+) junto a **Almacén de reglas**, seleccione **Establecer almacén de reglas**, haga clic en **Usar almacén de reglas de archivo local** y, a continuación, haga clic en **Sí**.

6.  En el cuadro **Nombre de archivo**, escriba **rules.xml**. Este es el nombre del archivo que contiene sus reglas de autoescala:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  Haga clic en el signo más (+) junto a **Almacén de información de servicio**, seleccione **Establecer almacén de información de servicio**, haga clic en **Usar información de servicio del archivo local** y, a continuación, haga clic en **Sí**.

8.  En el cuadro **Nombre de archivo**, escriba **services.xml**. Este es el nombre del archivo que contiene sus reglas de autoescala:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  En la ventana de configuración de las bibliotecas empresariales, en el menú **Archivo**, haga clic en **Guardar** para guardar los cambios realizados en la configuración. Posteriormente, en la ventana de configuración de las bibliotecas empresariales, en el menú **Archivo**, haga clic en **Salir**.

Para obtener información detallada sobre las acciones que está realizando el bloque de autoescala de la aplicación, necesita capturar los mensajes de registro que escribe. Por ejemplo, si va a hospedar el bloque en una aplicación de consola, puede ver los mensajes de registro en la ventana de salida de Visual Studio. La siguiente sección muestra cómo puede configurar este comportamiento.

### Configurar el registro en la aplicación host del bloque de autoescala de la aplicación

1.  En Visual Studio, haga doble clic en el archivo **App.config** en el Explorador de soluciones para abrirlo en el editor. Posteriormente, agregue la sección **system.diagnostics** como se muestra en el siguiente ejemplo:

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  Guarde los cambios.

Ahora puede ejecutar su aplicación de consola de host del bloque de autoescala de la aplicación y observar cómo las reglas de autoescala funcionan con su aplicación de Azure de destino. Al ejecutar la aplicación de consola de host, debe ver mensajes similares a los siguientes en la ventana de salida de Visual Studio. Estos mensajes de registro le ayudan a comprender el comportamiento del bloque. Por ejemplo, indican las reglas que el bloque está combinando y las acciones que el bloque está realizando.

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## <a id="NextSteps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del uso del bloque de autoescala de la aplicación, siga estos vínculos para obtener información sobre cómo implementar escenarios de autoescala más complejos.

-   [Hospedaje del bloque de autoescala de la aplicación en un rol de trabajo][]
-   [Implementación del comportamiento de limitación][]
-   [Descripción de los rangos de reglas y la reconciliación][]
-   [Extensión y modificación del bloque de autoescala de la aplicación][]
-   [Uso del estabilizador de optimización para evitar una oscilación de alta frecuencia y optimizar los costes][]
-   [Uso de notificaciones y escalado manual][]
-   [Definición de los grupos de escala][]
-   [Uso de WASABiCmdlets para manipular el bloque mediante Windows PowerShell][]
-   [Guía del desarrollador para el paquete de integración de Enterprise Library 5.0 para Azure][]
-   [Reducción de los costes de hospedaje de Azure debido a Sage con autoescala][]
-   [Reducción de los costes de hospedaje de TechNet y MSDN y del impacto medioambiental con la autoescala en Azure][]

  [Paquete de integración de Microsoft Enterprise Library 5.0 para Azure]: http://go.microsoft.com/fwlink/?LinkID=235134
  [Pasos siguientes]: #NextSteps
  [¿Qué es el bloque de autoescala de la aplicación?]: #WhatIs
  [Conceptos]: #Concepts
  [Recopilación de datos del contador de rendimiento desde su aplicación de Azure de destino]: #PerfCounter
  [Instalación de una aplicación host para el bloque de autoescala de la aplicación]: #CreateHost
  [Creación de una instancia y ejecución de la autoescala]: #Instantiate
  [Definición de su modelo de servicio]: #DefineServiceModel
  [Definición de sus reglas de autoescala]: #DefineAutoscalingRules
  [Configuración del bloque de autoescala de la aplicación]: #Configure
  [Uso de contadores de rendimiento en Azure]: http://www.windowsazure.com/en-us/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Almacenamiento de sus datos de información del servicio]: http://msdn.microsoft.com/en-us/library/hh680878(PandP.50).aspx		
  [Hospedaje del bloque de autoescala de la aplicación en un rol de trabajo]: http://msdn.microsoft.com/en-us/library/hh680914(PandP.50).aspx
  [Implementación del comportamiento de limitación]: http://msdn.microsoft.com/en-us/library/hh680896(PandP.50).aspx
  [Descripción de los rangos de reglas y la reconciliación]: http://msdn.microsoft.com/en-us/library/hh680923(PandP.50).aspx
  [Extensión y modificación del bloque de autoescala de la aplicación]: http://msdn.microsoft.com/en-us/library/hh680889(PandP.50).aspx
  [Uso del estabilizador de optimización para evitar una oscilación de alta frecuencia y optimizar los costes]: http://msdn.microsoft.com/en-us/library/hh680951(PandP.50).aspx
  [Uso de notificaciones y escalado manual]: http://msdn.microsoft.com/en-us/library/hh680885(PandP.50).aspx
  [Definición de los grupos de escala]: http://msdn.microsoft.com/en-us/library/hh680902(PandP.50).aspx
  [Uso de WASABiCmdlets para manipular el bloque mediante Windows PowerShell]: http://msdn.microsoft.com/en-us/library/hh680938(PandP.50).aspx
  [Guía del desarrollador para el paquete de integración de Enterprise Library 5.0 para Azure]: http://msdn.microsoft.com/en-us/library/hh680949(PandP.50).aspx
  [Reducción de los costes de hospedaje de Azure debido a Sage con autoescala]: http://msdn.microsoft.com/en-us/library/jj838716(PandP.50).aspx
  [Reducción de los costes de hospedaje de TechNet y MSDN y del impacto medioambiental con la autoescala en Azure]: http://msdn.microsoft.com/en-us/library/jj838718(PandP.50).aspx


<!--HONumber=35.1-->
