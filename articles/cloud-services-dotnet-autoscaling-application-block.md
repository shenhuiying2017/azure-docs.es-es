<properties 
	pageTitle="Uso del bloque de autoescala de la aplicación (.NET) - Azure" 
	description="Aprenda a utilizar la autoescala de la aplicación en Azure. Los ejemplos de código están escritos en C# y utilizan la API .NET." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="rasquill"/>







# Uso del bloque de autoescala de la aplicación

En esta guía se demuestra cómo realizar tareas comunes con el
bloque de autoescala de la aplicación desde el [Paquete de integración de
Microsoft Enterprise Library 5.0 para Azure][]. Los ejemplos están escritos en C\#
y usan la API de .NET. Entre los escenarios que se abordan se incluye el **hospedaje del
bloque**, el **uso de reglas de restricción** y el **uso de reglas reactivas**. Para
obtener más información sobre el bloque de autoescala de la aplicación, consulte la sección [Pasos siguientes][].

## Tabla de contenido

[¿Qué es el bloque de autoescala de la aplicación?][]   
 [Conceptos][]   
 [Recopilación de datos del contador de rendimiento desde la aplicación de Azure de destino][]   
 [Instalación de una aplicación host para el bloque de autoescala de la aplicación][]   
 [Creación de instancias y ejecución de la autoescala][] [Definición del modelo de servicio][]   
 [Definición de las reglas de autoescala][]   
 [Configuración del bloque de autoescala de la aplicación][]   
 [Pasos siguientes][]

## <a id="WhatIs"> </a>¿Qué es el bloque de autoescala de la aplicación?

El bloque de autoescala de la aplicación puede escalar automáticamente la aplicación de Microsoft
Azure en función de reglas definidas específicamente para dicha
aplicación. Puede usar estas reglas para ayudar a la aplicación de Azure
a mantener su rendimiento en respuesta a los cambios en su
carga de trabajo y, al mismo tiempo, controlar los costes asociados al
hospedaje de la aplicación en Azure. Además del escalado
mediante el aumento o la disminución de la cantidad de instancias de rol en la
aplicación, el bloque permite también usar otras acciones de escalado,
como limitar cierta funcionalidad dentro de la aplicación o
usar acciones definidas por el cliente.

Puede elegir entre hospedar el bloque en un rol de Azure o en una
aplicación local.

El bloque de autoescala de la aplicación forma parte del [Paquete de integración de Microsoft Enterprise Library 5.0 para Azure][].

## <a id="Concepts"> </a>Conceptos

En el diagrama siguiente, la línea verde muestra un trazado de la cantidad de
instancias en ejecución de un rol de Azure durante dos días. La cantidad de
instancias cambia automáticamente con el paso del tiempo en respuesta a un conjunto de
reglas de autoescala.

![diagrama de ejemplo de autoescala](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

El bloque usa dos tipos de reglas para definir el comportamiento de autoescala
de la aplicación:

-   **Reglas de restricción:** para establecer los límites superior e inferior del número de
    instancias; por ejemplo, digamos que cada mañana entre las 8:00 y las 10:00
    desea un mínimo de cuatro y un máximo de seis instancias,
    por lo que usará una **regla de restricción**. En el diagrama, las líneas roja y azul
    representan reglas de restricción. Por ejemplo, en el punto **A** del
    diagrama, la cantidad mínima de instancias de rol asciende de dos a
    cuatro, a fin de acomodar el aumento anticipado en la
    carga de trabajo de la aplicación en este momento. En el punto **B** del diagrama,
    se evita que la cantidad de instancias de rol aumente por encima de cinco
    a fin de controlar los costes de ejecución de la aplicación.

-   **Reglas reactivas:** para permitir que la cantidad de instancias de rol cambie
    en respuesta a cambios impredecibles en la demanda, use **reglas
    reactivas**. En el punto **C** del diagrama, el bloque reduce automáticamente
    la cantidad de instancias de rol, de cuatro a tres, en
    respuesta a una reducción en la carga de trabajo. En el punto **D**, el bloque
    detecta un aumento en la carga de trabajo y aumenta automáticamente la
    cantidad de instancias de rol que se ejecutan de tres a cuatro.

El bloque almacena su configuración en dos almacenes:

-   **Almacén de reglas:** el almacén de reglas conserva la configuración de su negocio.
    Es una lista de todas las reglas de autoescala que ha definido para la
    aplicación de Azure. Este almacen generalmente es un archivo XML que
    se ubica donde el bloque de autoescala de la aplicación pueda leerlo, por ejemplo,
    en el almacenamiento de blobs de Azure o en un archivo local.

-   **Almacén de información de servicio:** almacena
    la configuración operativa, que es el modelo de servicio de la
    aplicación de Azure. Este modelo de servicio incluye toda la
    información acerca de la aplicación de Azure (como los nombres de rol
    y los detalles de la cuenta de almacenamiento) que el bloque necesita para poder
    recopilar puntos de datos de la aplicación de Azure de destino y
    realizar operaciones de escalado.

## <a id="PerfCounter"> </a>Recopilación de datos del contador de rendimiento desde la aplicación de Azure de destino

Las reglas reactivas pueden usar los datos del contador de rendimiento de los roles como parte de
la definición de la regla. Por ejemplo, una regla reactiva puede supervisar el uso de la CPU
de un rol de Azure para determinar si el bloque
debe iniciar una operación de escalado. El bloque lee los datos del contador de
rendimiento de la tabla de diagnósticos de Azure llamada
**WADPerformanceCountersTable** en el almacenamiento de Azure.

De forma predeterminada, Azure no escribe datos del contador de rendimiento en la
tabla de diagnósticos de Azure en el almacenamiento de Azure. Por lo tanto, debe
modificar los roles a partir de los cuales necesita recopilar datos del contador
de rendimiento para guardar estos datos. Para obtener detalles sobre cómo habilitar
los contadores de rendimiento en la aplicación, vea [Uso de contadores de rendimiento en Azure][].

## <a id="CreateHost"> </a>Instalación de una aplicación host para el bloque de autoescala de la aplicación

El bloque de autoescala de la aplicación se puede hospedar en un rol
de Azure o en una aplicación local. Dicho bloque
se hospeda generalmente en una aplicación independiente de la aplicación
de destino que desea escalar automáticamente. En esta sección se proporcionan
instrucciones sobre cómo configurar la aplicación host.

### Obtener el paquete NuGet del bloque de autoescala de la aplicación

Antes de usar el bloque de autoescala de la aplicación en el proyecto de
Visual Studio, deberá obtener los archivos binarios de dicho bloque
y agregar referencias a estos en el proyecto. La extensión NuGet
de Visual Studio facilita la instalación y actualización de las bibliotecas
y herramientas en Visual Studio y Visual Web Developer. El paquete
NuGet del bloque de autoescala de la aplicación es la manera más sencilla de obtener
las API de dicho bloque. Para obtener más información sobre **NuGet** y sobre cómo
instalar y usar la extensión **NuGet** de Visual Studio, consulte el sitio web de [NuGet][].
 

Una vez que haya instalado el Administrador de paquetes NuGet, haga lo siguiente
para instalar el paquete de autoescala de NuGet en la aplicación:

1.  Abra la ventana **Consola del administrador de paquetes NuGet**. En el menú **Herramientas**,
    seleccione **Administrador de paquetes de la biblioteca** y, a continuación, **Consola del
    Administrador de paquetes**.

2.  Escriba el siguiente comando en la ventana de la consola del administrador de paquetes
    NuGet:

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

La instalación del paquete NuGet actualiza el proyecto con todos los ensamblados
y las referencias que necesita para usar el bloque de autoescala
de la aplicación. Ahora el proyecto incluye los archivos de esquema XML para
las definiciones de la regla de autoescala y la información del servicio de autoescala.
Asimismo, incluye también un archivo léame que contiene información
importante sobre el bloque de autoescala de la aplicación:

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### Establecer el marco de destino en .NET Framework 4

El proyecto debe dirigirse a .NET Framework 4. Para cambiar o comprobar el
marco de trabajo de destino:

1.  En el Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto y seleccione
    **Propiedades**.

2.  En la pestaña **Aplicación** de la ventana Propiedades, asegúrese de que el marco de trabajo destino esté establecido en **.NET Framework 4**.

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### Agregar referencias de espacio de nombres

Agregue las siguientes declaraciones de espacio de nombres en la parte superior de cualquier archivo de C\#
en el que desee obtener acceso al bloque de autoescala de la aplicación
mediante programación:

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>Creación de instancias y ejecución de la autoescala

Use el método **IServiceLocator.GetInstance** para crear una instancia de
autoescala y, después, llame al método **Autoscaler.Start** para ejecutar la
**autoescala**.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>Definición del modelo de servicio

Por lo general, el modelo de servicio (una descripción del entorno
de Microsoft Azure que incluye información sobre suscripciones, servicios
hospedados, roles y cuentas de almacenamiento) se almacena en un archivo XML. Puede encontrar una
copia del esquema de este archivo XML en el archivo
**AutoscalingServiceModel.xsd** del proyecto. En Visual Studio,
este esquema ofrece Intellisense y validación cuando se edita el archivo
XML del modelo de servicio.

Cree un archivo XML nuevo llamado **services.xml** en el proyecto.

En Visual Studio, debe asegurarse de copiar el archivo del modelo de servicio
en la carpeta de salida. Para ello, siga estos pasos:

1.  Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.

2.  En el panel Propiedades, establezca el valor **Copiar en el directorio de salida**
    en **Copiar siempre**.

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	El ejemplo de código siguiente muestra un modelo de servicio de ejemplo en un archivo **services.xml**:

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
deberá iniciar sesión en el [Portal de administración de Azure][].

Inicie sesión en el Portal de administración.

-   **[subscriptionname]:** elija un nombre descriptivo para hacer referencia a la
    suscripción de Azure que contiene la aplicación en la cual
    desea usar la autoescala.

-   **[subscriptionid]:** el identificador único de la
    suscripción de Azure que contiene la aplicación en la cual
    desea usar la autoescala.

    1.  En el Portal de administración de Azure, haga clic en
        **Servicios en la nube**.

    2.  En la lista de Servicios en la nube, haga clic en el servicio que hospeda la
        aplicación en la cual desea usar la autoescala. El
        panel Vista rápida de la derecha mostrará el
        **Id. de suscripción**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]:** el prefijo DNS del servicio hospedado en el que desea usar la autoescala.

    1.  En el Portal de administración de Azure, haga clic en
        **Servicios en la nube**.

    2.  En la lista de Servicios en la nube, busque el servicio que hospeda la
        aplicación en la cual desea usar la autoescala. El nombre del
        servicio en la nube es el **Prefijo de DNS**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]:** el nombre del rol de destino de las reglas de autoescala.

    1.  En el Portal de administración de Azure, haga clic en
        **Servicios en la nube**.

    2.  En la lista de Servicios en la nube, haga clic en el servicio que hospeda la
        aplicación en la cual desea usar la autoescala y, a continuación, haga clic en
        **Instancias**. La columna **Rol* muestra el nombre del rol
        de destino.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** y **[storageaccountkey]:** el nombre de la cuenta de almacenamiento de Azure que se usa para la aplicación de Azure de destino.

    1.  En el Portal de administración de Azure, haga clic en
        **Almacenamiento**.

    2.  En la lista de cuentas de almacenamiento, seleccione  aquella que está usando. La columna
        **Nombre** mostrará el **Nombre**.

    3.  Haga clic en el botón **Administrar claves** de la parte inferior de la pantalla
        para obtener la clave de acceso primaria.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** la **huella digital** del certificado de administración que el bloque usará para proteger las solicitudes de escalado de la aplicación de destino.

    1.  En el Portal de administración de Azure, haga clic en
        **Configuración**.

    2.  La columna **Huella digital** mostrará la **Huella digital**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

Para obtener más información sobre el contenido del archivo del modelo de servicio, consulte
[Almacenamiento de sus datos de información del servicio][].

## <a id="DefineAutoscalingRules"> </a>Definición de las reglas de autoescala

Generalmente, las reglas de autoescala que controlan la cantidad de instancias
de rol en su aplicación de destino se almacenan en un archivo XML. Puede encontrar una
copia del esquema de este archivo XML en el archivo **AutoscalingRules.xsd**
del proyecto. En Visual Studio, este esquema ofrece
Intellisense y validación cuando se edita el archivo XML.

Cree un archivo XML nuevo llamado **rules.xml** en el proyecto.

En Visual Studio, debe asegurarse de copiar el archivo de reglas en la
carpeta de salida. Para ello, siga estos pasos:

1.  Haga clic con el botón derecho en el archivo y seleccione **Propiedades**.

2.  En el panel Propiedades, establezca el valor **Copiar en el directorio de salida**
    en **Copiar siempre**.

El ejemplo de código siguiente muestra un conjunto de reglas de ejemplo en un archivo **rules.xml**:
 

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

En este ejemplo hay tres reglas de autoescala (una **regla
de restricción** y dos **reglas reactivas**) que operan en un destino llamado
**AutoscalingApplicationRole**, que es el alias de un rol definido en
el **modelo de servicio**:

-   La regla de restricción siempre está activa y establece la cantidad mínima de
    instancias de rol en dos y la máxima en
    seis.

-   Ambas reglas reactivas usan un **operando** llamado
    **WebRoleA\_CPU\_Avg\_5m** que calcula el uso promedio de la CPU
    en los últimos cinco minutos para un rol de Azure llamado
    **AutoscalingApplicationRole**. Este rol se define en el
    **modelo de servicio**.

-   La regla reactiva llamada **ScaleUpOnHighUtilization** incrementa el
    recuento de instancias del rol de destino en uno si el uso promedio de la CPU
    en los últimos cinco minutos ha sido superior o
    igual al 60 %.

-   La regla reactiva llamada **ScaleDownOnLowUtilization** disminuye el
    recuento de instancias del rol de destino en uno si el uso promedio de la CPU
    en los últimos cinco minutos ha sido inferior al 60 %.

## <a id="Configure"> </a>Configuración del bloque de autoescala de la aplicación

Después de haber definido el modelo de servicio y las reglas de autoescala, debe
configurar el bloque de autoescala de la aplicación para usarlos. Esta
información de configuración operativa se almacena en el archivo de
configuración de la aplicación.

De forma predeterminada, el bloque de autoescala de la aplicación espera que las reglas
de autoescala y el modelo de servicio se almacenen en los blobs de Azure. En este
ejemplo se configurará el bloque para que los cargue desde el sistema de archivos
local.

### Configuración del bloque de autoescala de la aplicación en la aplicación host

1.  Haga clic con el botón derecho en el archivo **App.config** en el Explorador de soluciones y, a continuación,
    haga clic en **Editar archivo de configuración**.

2.  En el menú **Bloques**, haga clic en **Agregar configuración de autoescala**:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  Expanda **Configuración de autoescala** y, a continuación, haga clic en los puntos suspensivos (...)
    que aparecen junto a **Cuenta de almacenamiento del almacén de puntos de datos**, agregue el **Nombre
    de cuenta** y la **Clave de cuenta** de la cuenta de almacenamiento de Azure
    donde el bloque almacenará los puntos de datos que recopila (consulte
    [Definición del modelo de servicio][] si no está seguro sobre dónde
    encontrar estos valores) y, a continuación, haga clic en **Aceptar**:  

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  Expanda la sección **Configuración de autoescala** para mostrar las secciones **Almacén
    de reglas** y **Almacén de información de servicio**. De forma predeterminada, estas están
    configuradas para usar el almacenamiento de blobs de Azure::  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  Haga clic en el signo más (+) que aparece junto a **Almacén de reglas**, seleccione **Establecer
    almacén de reglas**, haga clic en **Usar almacén de reglas de archivo local** y, a continuación, haga
    clic en **Sí**.

6.  En el cuadro **Nombre de archivo**, escriba **rules.xml**. Este es el nombre del
    archivo que contiene las reglas de autoescala:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  Haga clic en el signo más (+) que aparece junto a **Almacén de información de servicio**, seleccione
    **Establecer almacén de información de servicio**, haga clic en **Usar almacén de información
    de servicio de archivo local** y. a continuación, haga clic en **Sí**.

8.  En el cuadro **Nombre de archivo**, escriba **services.xml**. Este es el nombre del
    archivo que contiene las reglas de autoescala:  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  En la ventana de configuración de bibliotecas empresariales, en el menú **Archivo**,
    haga clic en **Guardar** para guardar los cambios realizados en la configuración. A continuación, en
    esa misma ventana, en el menú **Archivo**, haga clic en
    **Salir**.

Para obtener información detallada sobre las acciones que está realizando el bloque
de autoescala de la aplicación, debe capturar los mensajes de registro
que escribe. Por ejemplo, si hospeda el bloque en una aplicación
de consola, puede ver los mensajes de registro en la ventana de salida de
Visual Studio. En la sección siguiente se muestra cómo configurar este
comportamiento.

### Configurar el registro en la aplicación host del bloque de autoescala de la aplicación

1.  En Visual Studio, haga doble clic en el archivo **App.config** en
    el Explorador de soluciones para abrirlo en el editor. A continuación, agregue la sección
    **system.diagnostics**, como se muestra en el ejemplo siguiente:

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

Ahora puede ejecutar la aplicación de consola de host del bloque de autoescala
de la aplicación y observar cómo funcionan las reglas de autoescala con la aplicación de Azure
de destino. Al ejecutar la aplicación de consola de host,
debe ver mensajes similares a los siguientes en la ventana de salida de
Visual Studio. Estos mensajes de registro le ayudan a comprender el comportamiento
del bloque. Por ejemplo, indican las reglas que se combinan en el bloque
y las acciones que este realiza.

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

Ahora que está familiarizado con los aspectos básicos del uso del bloque de autoescala
de la aplicación, siga estos vínculos para obtener información sobre cómo implementar escenarios de
autoescala más complejos:

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

  [Paquete de integración de Microsoft Enterprise Library 5.0 para Azure]:
    http://go.microsoft.com/fwlink/?LinkID=235134
  [Pasos siguientes]: #NextSteps
  [¿Qué es el bloque de autoescala de la aplicación?]: #WhatIs
  [Conceptos]: #Concepts
  [Recopilación de datos del contador de rendimiento desde la aplicación de Azure de destino]: #PerfCounter
  [Instalación de una aplicación host para el bloque de autoescala de la aplicación]: #CreateHost
  [Creación de instancias y ejecución de la autoescala]: #Instantiate
  [Definición del modelo de servicio]: #DefineServiceModel
  [Definición de las reglas de autoescala]: #DefineAutoscalingRules
  [Configuración del bloque de autoescala de la aplicación]: #Configure
  [Uso de contadores de rendimiento en Azure]: http://azure.microsoft.com/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Almacenamiento de sus datos de información del servicio]: http://msdn.microsoft.com/library/hh680878(PandP.50).aspx		
  [Hospedaje del bloque de autoescala de la aplicación en un rol de trabajo]: http://msdn.microsoft.com/library/hh680914(PandP.50).aspx
  [Implementación del comportamiento de limitación]: http://msdn.microsoft.com/library/hh680896(PandP.50).aspx
  [Descripción de los rangos de reglas y la reconciliación]: http://msdn.microsoft.com/library/hh680923(PandP.50).aspx
  [Extensión y modificación del bloque de autoescala de la aplicación]: http://msdn.microsoft.com/library/hh680889(PandP.50).aspx
  [Uso del estabilizador de optimización para evitar una oscilación de alta frecuencia y optimizar los costes]: http://msdn.microsoft.com/library/hh680951(PandP.50).aspx
  [Uso de notificaciones y escalado manual]: http://msdn.microsoft.com/library/hh680885(PandP.50).aspx
  [Definición de los grupos de escala]: http://msdn.microsoft.com/library/hh680902(PandP.50).aspx
  [Uso de WASABiCmdlets para manipular el bloque mediante Windows PowerShell]: http://msdn.microsoft.com/library/hh680938(PandP.50).aspx
  [Guía del desarrollador para el paquete de integración de Enterprise Library 5.0 para Azure]: http://msdn.microsoft.com/library/hh680949(PandP.50).aspx
  [Reducción de los costes de hospedaje de Azure debido a Sage con autoescala]: http://msdn.microsoft.com/library/jj838716(PandP.50).aspx
  [Reducción de los costes de hospedaje de TechNet y MSDN y del impacto medioambiental con la autoescala en Azure]: http://msdn.microsoft.com/library/jj838718(PandP.50).aspx

<!--HONumber=45--> 
