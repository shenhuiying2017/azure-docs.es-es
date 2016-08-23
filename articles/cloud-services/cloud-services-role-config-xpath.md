<properties 
pageTitle="Hoja de referencia rápida de XPath de configuración del rol de Servicios en la nube | Microsoft Azure" 
description="Distintas configuraciones de XPath que puede usar en la configuración del rol de servicio en la nube para exponer la configuración como una variable de entorno." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="08/10/2016" 
ms.author="adegeo"/>

# Exponer los valores de configuración de rol como una variable de entorno con XPath

En el archivo de definición de servicio de un rol web o trabajo en el servicio en la nube, puede exponer los valores de configuración en tiempo de ejecución como variables de entorno. Se admiten los siguientes valores de XPath (que se corresponden con valores de API).

Estos valores de XPath también están disponibles en la biblioteca [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx).

## Aplicación en ejecución en el emulador

Indica que la aplicación se ejecuta en el emulador.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Código | var x = RoleEnvironment.IsEmulated; |


## Id. de implementación

Recupera el identificador de implementación de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Código | var deploymentId = RoleEnvironment.DeploymentId; |


## Id. de rol 

Recupera el identificador del rol actual de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## Actualizar dominio

Recupera el dominio de actualización de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## Dominio de error

Recupera el dominio de error de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## Nombre de rol

Recupera el nombre de rol de las instancias.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |


## Opción de configuración

Recupera el valor de la opción de configuración especificada.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código | var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## Ruta de acceso de almacenamiento local

Recupera la ruta de acceso de almacenamiento local de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |


## Tamaño del almacenamiento local

Recupera el tamaño del almacenamiento local de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## Protocolo del punto de conexión 

Recupera el protocolo del punto de conexión de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## IP del punto de conexión

Obtiene la dirección IP del punto de conexión especificado.

| Tipo | Ejemplo |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código | var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## Puerto del punto de conexión 

Recupera el puerto del punto de conexión de la instancia.

| Tipo | Ejemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código | var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |





## Ejemplo

Este es un ejemplo de un rol de trabajo que crea una tarea de inicio con una variable de entorno denominada `TestIsEmulated` establecida en el [valor @emulated xpath](#app-running-in-emulator).

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## Pasos siguientes

Obtenga más información sobre el archivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).

Cree un paquete [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg).

Habilite [Escritorio remoto](cloud-services-role-enable-remote-desktop.md) para un rol.

<!---HONumber=AcomDC_0810_2016-->