<properties 
	pageTitle="Servicios en la nube de Azure - Definición de servicio y configuración de servicio - Certificado XML" 
	description="Aprenda a establecer un certificado en los archivos de definición y configuración de servicio" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015"
	ms.author="adegeo"/>



# Establecimiento de la definición y configuración del servicio para un certificado

Las máquinas virtuales que ejecutan los roles web o de trabajo puede tener certificados asociados. Tras cargar el certificado en el portal, tendrá que establecer los archivos de definición de servicio (.csdef) y de configuración de servicio (.cscfg) para el certificado.

La máquina virtual puede tener acceso a la clave privada del certificado una vez instalado. Por este motivo, tal vez quiera restringir el acceso a los procesos con permisos elevados.

## Ejemplo de definición de servicio

El siguiente es un ejemplo de certificado que se establece en la definición de servicio.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### Permisos
Se pueden establecer permisos (atributo `permisionLevel`) en uno de los siguientes casos:

| Valor del permiso | Descripción |
| ----------------  | ----------- |
| limitedOrElevated | **(Predeterminado)** todos los procesos de rol pueden tener acceso a la clave privada. |
| elevated | Solo los procesos elevados pueden tener acceso a la clave privada.|

## Ejemplo de configuración de servicio

El siguiente es un ejemplo de certificado que se define en la configuración de servicio.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**Observe** los atributos `name` coincidentes.

## Pasos siguientes
Revise el esquema [XML de definición de servicio](https://msdn.microsoft.com/library/azure/ee758711.aspx) y el esquema [XML de configuración de servicio](https://msdn.microsoft.com/library/azure/ee758710.aspx).

<!---HONumber=Oct15_HO4-->