<properties
   pageTitle="Cómo proteger un clúster de Service Fabric | Microsoft Azure"
   description="Cómo hacer que un clúster de Service Fabric sea seguro. ¿Cuáles son las opciones?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# Cómo proteger un clúster de Service Fabric mediante certificados.

Para configurar un clúster de Service Fabric seguro, necesitará al menos un certificado de servidor/X509 que después se carga en el almacén de claves de Azure y se usa en el [proceso de creación de un clúster de Service Fabric](service-fabric-cluster-creation-via-portal.md).

Hay tres pasos diferentes

1. Adquirir el certificado X509.
2. Cargar el certificado en el almacén de claves de Azure.
3. Especifique la ubicación y los detalles del certificado para el proceso de creación del clúster de Service Fabric.

Antes de entrar en demasiados detalles, vamos a ver los aspectos básicos de los escenarios.

##  ¿Qué escenarios se incluyen?

Service Fabric proporciona seguridad para los escenarios siguientes:

1. Protección de un clúster para la comunicación de nodo a nodo.
2. Protección de la comunicación de un cliente de Service Fabric con un nodo determinado del clúster
3. Control de acceso basado en roles (RBAC): capacidad para restringir las operaciones de administración de las operaciones de solo lectura en el clúster a un conjunto de certificados.   

Service Fabric usa los certificados X509 que especifique como parte de las configuraciones de tipo de nodo cuando se crea un clúster. Para ver una descripción rápida de qué son estos certificados y cómo se pueden adquirir o crear, vaya hasta el final de la página.

 
## Adquisición de certificados X509

1. Para los clústeres que ejecutan cargas de trabajo de producción, debe usar un certificado X509 firmado por una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) para proteger el clúster. Para obtener más información sobre cómo obtener estos certificados, visite [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx).
2. Para los clústeres que se usan solo con fines de prueba, puede usar un certificado autofirmado.


## Creación de un certificado autofirmado con fines de prueba

Encontrará información detallada sobre la creación de un certificado autofirmado en [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx).
    
Este es el PS que uso para crear mis certificados de prueba, pero asegúrese de leer el documento anterior para comprobar que satisface sus necesidades. ```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**Nota:** DnsName <cadena> especifica uno o más nombres DNS para colocar en la extensión de nombre alternativo del sujeto del certificado cuando no se especifica un certificado para copiar mediante el parámetro CloneCert. El primer nombre DNS también se guarda como nombre del sujeto. Si no se especifica ningún certificado de firma, el primer nombre DNS también se guarda como nombre del emisor.

## Carga del certificado X509 al almacén de claves

Encontrará instrucciones sobre cómo cargar un certificado al almacén de claves aquí [vínculo a la documentación del almacén de claves](https://azure.microsoft.com/documentation/articles/key-vault-get-started/).

Asegúrese de anotar la dirección URL del almacén de origen, la dirección URL del certificado y la huella digital del certificado. Los necesitará para configurar el clúster de Service Fabric seguro. Los datos que necesita tendrán un aspecto similar al siguiente:



1. **Id. de recurso del almacén de claves/URL del almacén de origen**: /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **URL de la ubicación del certificado en el almacén de claves**: https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **Huella digital del certificado**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##Configuración de un clúster seguro 

Los certificados que hay que usar se especifican en el nivel NodeType, en las configuraciones de seguridad. Debe especificarlos para cada NodeType que tenga en el clúster. Aunque este documento muestra cómo hacer esto con el portal, puede hacer lo mismo con una plantilla ARM.

![SecurityConfigurations\_01][SecurityConfigurations_01]

Parámetros obligatorios

- **Modo de seguridad**: asegúrese de seleccionar 'certificado X509'. Indica a Service Fabric que va a configurar un clúster seguro. 
- **Nivel de protección de clúster**: consulte este [documento sobre niveles de protección](https://msdn.microsoft.com/library/aa347692.aspx) para entender lo que significa cada uno de estos valores. Aunque se permiten los tres valores: EncryptAndSign, Sign y None, es mejor mantener el valor predeterminado "EncryptAndSign", a menos que sepa lo que está haciendo.
- **Almacén de origen** hace referencia al identificador de recurso del almacén de claves, y debe tener el formato ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **Dirección URL del certificado** hace referencia a la dirección URL en el almacén de claves donde se cargó el certificado, y deben tener el formato ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **Huella digital del certificado** hace referencia a la huella digital del certificado que se encuentra en la dirección URL que especificó anteriormente.

Parámetros opcionales: opcionalmente puede especificar certificados adicionales que los equipos cliente usarán para realizar operaciones en el clúster. De forma predeterminada, la huella digital especificada en los parámetros obligatorios se agrega a la lista de huellas digitales autorizadas que tienen permiso para realizar operaciones de cliente.

Cliente de administración: esta información se usa para validar que el cliente que se conecta con el punto de conexión de administración de clúster presenta las credenciales correctas para realizar la administración y las acciones de solo lectura en el clúster. Puede especificar más de un certificado para autorizar para operaciones de administración.


- **Autorizar por**: indica a Service Fabric si debe buscar este certificado usando el nombre del sujeto o la huella digital. Aunque usar el nombre del sujeto para autorizar no es un procedimiento de seguridad recomendado, permite más flexibilidad.


- **Nombre del sujeto**: solo es necesario si especificó que la autorización es por nombre del sujeto.
- **Huella digital del emisor**: permite un nivel adicional de comprobación que el servidor puede realizar cuando un cliente presenta sus credenciales al servidor.

Cliente de solo lectura: esta información se usa para validar que el cliente que se conecta con el punto de conexión de administración de clúster presenta las credenciales correctas para realizar las acciones de solo lectura en el clúster. Puede especificar más de un certificado para autorizar para operaciones de administración.


- **Autorizar por**: indica a Service Fabric si debe buscar este certificado usando el nombre del sujeto o la huella digital. Aunque usar el nombre del sujeto para autorizar no es un procedimiento de seguridad recomendado, permite más flexibilidad.

- **Nombre del sujeto**: solo es necesario si especificó que la autorización es por nombre del sujeto.
- **Huella digital del emisor**: permite un nivel adicional de comprobación que el servidor puede realizar cuando un cliente presenta sus credenciales al servidor.


## Cómo actualizar los certificados en el clúster

Service Fabric permite especificar dos certificados, uno principal y otro secundario. De forma predeterminada, el que especificó durante la creación es el principal. Para agregar otro certificado, necesita implementar ese certificado en las máquinas virtuales del clúster. Consulte el documento sobre cómo [implementar certificados en máquinas virtuales desde un almacén de claves administrado por el cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

Una vez finalizada la operación correctamente, vaya al portal o a través de ARM, indique a Service Fabric que tiene un certificado secundario que se puede usar también. Todo lo que necesita es una huella digital.

En el portal, busque el recurso de clúster al que desea agregar este certificado, haga clic en la configuración del certificado, especifique la huella digital del certificado secundario y presione Guardar. Se iniciará una implementación y, cuando se complete correctamente, podrá usar tanto el certificado principal como el secundario para realizar operaciones de administración en el clúster.

![SecurityConfigurations\_02][SecurityConfigurations_02]

Si ahora desea quitar uno de los certificados, puede hacerlo. Asegúrese de presionar Guardar después de quitarlo para que se inicie una nueva implementación. Una vez completada la implementación, el certificado eliminado ya no podrá usarse para conectarse al clúster. Para un clúster seguro, siempre necesitará tener implementado al menos un certificado válido (no revocados ni expirado); de lo contrario, no podrá tener acceso al clúster.

Hay un evento de diagnóstico que permite saber si alguno de los certificados están a punto de expirar.



## ¿Qué son los certificados X509?

Los certificados digitales X509 se usan habitualmente para autenticar clientes y servidores, cifrar mensajes y firmarlos digitalmente. Para obtener más información sobre estos certificados, visite [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx).

**Nota:**

1. Los certificados usados en clústeres que ejecutan cargas de trabajo de producción deberán crearse mediante un servicio de certificados de Windows Server correctamente configurado u obtenerse mediante una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) autorizada.
2. No use nunca en producción certificados temporales o de pruebas creados con herramientas como MakeCert.exe.
3. Para los clústeres que se usan solo con fines de prueba, puede usar un certificado autofirmado. 


##¿Qué son los certificados de servidor y los certificados de cliente?

**Certificados de servidor/X509**

Los certificados de servidor tienen la tarea principal de autenticar el servidor (nodo) a los clientes o el servidor (nodo) al servidor (nodo). Una de las comprobaciones iniciales cuando un cliente o un nodo autentica un nodo consiste en comparar el valor del nombre común en el campo Sujeto para asegurarse de que está presente en la lista de nombres comunes permitidos que se ha configurado. Este nombre común o uno de los nombres alternativos de sujeto del certificado debe estar presente en la lista de nombres comunes permitidos.

En el siguiente artículo se describe cómo generar certificados con nombres alternativos de sujeto (SAN). [http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
**Nota:** el campo de sujeto puede contener varios valores, cada uno con un prefijo de inicialización para indicar el valor. Normalmente, la inicialización es "CN" de nombre común, por ejemplo, "CN = www.contoso.com". El campo Sujeto también puede estar en blanco. Observe también el campo Nombre alternativo de sujeto opcional; si lo rellena, debe contener tanto el nombre común del certificado como una entrada por nombre alternativo de sujeto. Estos se especifican como valores de nombre DNS.

Tenga en cuenta también que el valor del campo Propósitos planteados del certificado debe incluir un valor apropiado, como "Autenticación de servidor" o "Autenticación de cliente".

**Certificados de cliente**

Los certificados de cliente normalmente no los emite una entidad de certificación de terceros. En su lugar, el almacén Personal de la ubicación del usuario actual contiene los certificados colocados ahí por una entidad de certificación raíz, con un propósito planteado de "Autenticación de cliente". El cliente puede usar este tipo de certificado cuando se requiere autenticación mutua. Todas las operaciones de administración en el clúster de Service Fabric requieren certificados de servidor. No deben usarse certificados de cliente.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introducción al modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png

<!---HONumber=Nov15_HO4-->