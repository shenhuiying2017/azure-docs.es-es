---
title: Grupos de atributos del esquema XML del modelo de servicio de Azure Service Fabric | Microsoft Docs
description: Describe los grupos de atributos del esquema XML del modelo de servicio de Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 97d4c665360ed4ba4ed788b504bad1d73bb8bdfa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34368007"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-attribute-groups"></a>Grupos de atributos del esquema XML del modelo de servicio

## <a name="accountcredentialsgroup-attributegroup"></a>AccountCredentialsGroup attributeGroup
|Atributo|Valor|
|---|---|
|contenido|2 atributos|
|Nombre|AccountCredentialsGroup|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountCredentialsGroup">
        <xs:attribute name="AccountName" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>User name or Service Account Name (i.e., MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
            </xs:annotation>
        </xs:attribute>
        <xs:attribute name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    </xs:attributeGroup>
    
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="accountname"></a>AccountName
Nombre de usuario o nombre de cuenta de servicio (es decir, MyMachine\JohnDoe o John.Doe@department.contoso.com).
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|AccountName|
|use|opcional|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountName" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>User name or Service Account Name (i.e., MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
            </xs:annotation>
        </xs:attribute>
        
```

#### <a name="password"></a>Password
Contraseña para la cuenta de usuario.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|Password|
|use|opcional|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    
```

## <a name="applicationinstanceattrgroup-attributegroup"></a>ApplicationInstanceAttrGroup attributeGroup
Grupo de atributos de la instancia de aplicación.

|Atributo|Valor|
|---|---|
|contenido|2 atributos|
|Nombre|ApplicationInstanceAttrGroup|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationInstanceAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application instance.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:attribute name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="nameuri"></a>NameUri
Nombre completo de la aplicación.
|Atributo|Valor|
|---|---|
|Tipo|FabricUri|
|Nombre|NameUri|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

#### <a name="applicationid"></a>ApplicationId
Identificador de esta aplicación.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|ApplicationId|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="applicationmanifestattrgroup-attributegroup"></a>ApplicationManifestAttrGroup attributeGroup
Grupo de atributos del manifiesto de aplicación.

|Atributo|Valor|
|---|---|
|contenido|3 atributos|
|Nombre|ApplicationManifestAttrGroup|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationManifestAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an un-structured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an un-structured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:anyAttribute processContents="skip"/> <!-- Allow unknown attributes to be used. -->
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="applicationtypename"></a>ApplicationTypeName
El identificador de tipo de esta aplicación.
|Atributo|Valor|
|---|---|
|Nombre|ApplicationTypeName|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="applicationtypeversion"></a>ApplicationTypeVersion
La versión de este tipo de aplicación, una cadena no estructurada.
|Atributo|Valor|
|---|---|
|Nombre|ApplicationTypeVersion|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an un-structured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="manifestid"></a>ManifestId
El identificador de este manifiesto de aplicación, una cadena no estructurada.
|Atributo|Valor|
|---|---|
|default||
|Nombre|ManifestId|
|Tipo|xs:string|
|use|opcional|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an un-structured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

## <a name="configoverridesidentifier-attributegroup"></a>ConfigOverridesIdentifier attributeGroup
Identifica las invalidaciones de configuración de un paquete de servicio.

|Atributo|Valor|
|---|---|
|contenido|2 atributos|
|Nombre|ConfigOverridesIdentifier|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConfigOverridesIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies configuration overrides for a service package.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServicePackageName" type="xs:string" use="required"/>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="servicepackagename"></a>ServicePackageName
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|ServicePackageName|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServicePackageName" type="xs:string" use="required"/>
    
```

#### <a name="rolloutversion"></a>RolloutVersion
Identificador de la implementación en la que se realizaron cambios en el elemento de invalidaciones.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|RolloutVersion|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="connectionstring-attributegroup"></a>ConnectionString attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|ConnectionString|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString">
                <xs:attribute name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>
          Connection string to the Azure storage account. Format:
          DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]
        </xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="connectionstring"></a>ConnectionString

          Connection string to the Azure storage account. Format:
          DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]
        
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|ConnectionString|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>
          Connection string to the Azure storage account. Format:
          DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]
        </xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="containername-attributegroup"></a>ContainerName attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|ContainerName|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName">
    <xs:attribute name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="containername"></a>ContainerName
El nombre del contenedor de Azure Blob Storage donde se cargan los datos.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|ContainerName|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="datadeletionageindays-attributegroup"></a>DataDeletionAgeInDays attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|DataDeletionAgeInDays|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays">
    <xs:attribute name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="datadeletionageindays"></a>DataDeletionAgeInDays
Número de días transcurridos los cuales los datos antiguos se eliminan de esta ubicación.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|DataDeletionAgeInDays|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="isenabled-attributegroup"></a>IsEnabled attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|IsEnabled|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled">
                <xs:attribute name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="isenabled"></a>IsEnabled
Si está habilitada o no la transferencia de datos a este destino. De forma predeterminada, no está habilitada.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|IsEnabled|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="levelfilter-attributegroup"></a>LevelFilter attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|LevelFilter|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter">
    <xs:attribute name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="levelfilter"></a>LevelFilter
El nivel en el que se deben filtrar los eventos de ETW. Se incluyen todos los eventos con un nivel igual o menor que el nivel especificado.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|LevelFilter|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="namevaluepair-attributegroup"></a>NameValuePair attributeGroup
Nombre y valor definidos como un atributo.

|Atributo|Valor|
|---|---|
|contenido|2 atributos|
|Nombre|NameValuePair|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameValuePair">
    <xs:annotation>
      <xs:documentation>Name and Value defined as an attribute.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="name"></a>NOMBRE
El nombre de la opción para invalidar.
|Atributo|Valor|
|---|---|
|Nombre|NOMBRE|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="value"></a>Valor
El nuevo valor de la configuración.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|Valor|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="path-attributegroup"></a>Path attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|Ruta de acceso|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path">
                <xs:attribute name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>
          Path to the file share. Format:
          file:[]
        </xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="path"></a>Ruta de acceso

          Path to the file share. Format:
          file:[]
        
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|Ruta de acceso|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>
          Path to the file share. Format:
          file:[]
        </xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="relativefolderpath-attributegroup"></a>RelativeFolderPath attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|RelativeFolderPath|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath">
                <xs:attribute name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="relativefolderpath"></a>RelativeFolderPath
Ruta de acceso a la carpeta, relativa al directorio de registro de aplicaciones.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|RelativeFolderPath|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="servicemanifestidentifier-attributegroup"></a>ServiceManifestIdentifier attributeGroup
Identifica un manifiesto de servicio.

|Atributo|Valor|
|---|---|
|contenido|2 atributos|
|Nombre|ServiceManifestIdentifier|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies a service manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest being referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest being referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="servicemanifestname"></a>ServiceManifestName
El nombre del manifiesto de servicio al que se hace referencia. El nombre debe coincidir con el nombre declarado en el elemento ServiceManifest del manifiesto de servicio.
|Atributo|Valor|
|---|---|
|Nombre|ServiceManifestName|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest being referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="servicemanifestversion"></a>ServiceManifestVersion
La versión del manifiesto de servicio a la que se hace referencia. La versión debe coincidir con la versión declarada en el manifiesto de servicio.
|Atributo|Valor|
|---|---|
|Nombre|ServiceManifestVersion|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest being referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

## <a name="uploadintervalinminutes-attributegroup"></a>UploadIntervalInMinutes attributeGroup
|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|UploadIntervalInMinutes|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes">
    <xs:attribute name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="uploadintervalinminutes"></a>UploadIntervalInMinutes
Intervalo en minutos en el que se cargan los datos en este destino.
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|UploadIntervalInMinutes|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="versioneditemattrgroup-attributegroup"></a>VersionedItemAttrGroup attributeGroup
Grupo de atributos de las secciones de control de versiones en documentos de ApplicationInstance y ServicePackage.

|Atributo|Valor|
|---|---|
|contenido|1 atributo|
|Nombre|VersionedItemAttrGroup|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedItemAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for versioning sections in ApplicationInstance and ServicePackage documents.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required"/>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="rolloutversion"></a>RolloutVersion
|Atributo|Valor|
|---|---|
|Tipo|xs:string|
|Nombre|RolloutVersion|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required"/>
  
```

## <a name="versionedname-attributegroup"></a>VersionedName attributeGroup
Grupo de atributos que incluye un nombre y una versión.

|Atributo|Valor|
|---|---|
|contenido|2 atributos|
|Nombre|VersionedName|

### <a name="xml-source"></a>Origen XML
```xml
<xs:attributeGroup xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedName">
    <xs:annotation>
      <xs:documentation>Attribute group that includes a Name and a Version.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Detalles de atributos

#### <a name="name"></a>NOMBRE
Nombre del elemento con control de versiones.
|Atributo|Valor|
|---|---|
|Nombre|NOMBRE|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="version"></a>Versión
Versión del elemento con control de versiones, una cadena no estructurada.
|Atributo|Valor|
|---|---|
|Nombre|Versión|
|use|requerido|
##### <a name="xml-source"></a>Origen XML
```xml
<xs:attribute xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

