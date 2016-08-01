<properties
   pageTitle="Actualización de certificados para un clúster de Azure | Microsoft Azure"
   description="Se describe cómo cargar un nuevo certificado en el Almacén de claves de Azure y actualizar o quitar un certificado para un clúster de Service Fabric existente."
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
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Agregar o quitar certificados para un clúster de Service Fabric de Azure

Al configurar la seguridad mediante certificados durante la creación del clúster, Service Fabric le permite especificar dos certificados, uno principal y uno secundario. De forma predeterminada, el que especifique en el momento de la creación es el certificado principal. Después de la creación del clúster, puede agregar un nuevo certificado como elemento secundario o quitar un certificado existente. Para más información sobre cómo Service Fabric usa certificados X.509, lea [Protección de un clúster de Service Fabric](service-fabric-cluster-security.md).

>[AZURE.NOTE] Para que un clúster sea seguro, siempre deberá tener implementado al menos un certificado válido, principal o secundario, no revocado ni caducado, o no podrá tener acceso al clúster.

## Agregar un certificado secundario
Para agregar otro certificado como secundario, debe cargar el certificado en un Almacén de claves de Azure y luego implementarlo en las máquinas virtuales del clúster. Para más información, consulte [Deploy certificates to VMs from a customer-managed Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Implementación de certificados en máquinas virtuales desde un Almacén de claves administrado por el cliente).

1. [Cargue un certificado X.509 en el Almacén de claves.](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y vaya al recurso del clúster al que quiere agregar este certificado.
3. En **Configuración**, haga clic en la configuración de certificado y especifique la huella digital del certificado secundario.
4. Haga clic en **Guardar**. Se iniciará una implementación y, cuando se complete, podrá usar tanto el certificado principal como el secundario para realizar operaciones de administración en el clúster.

![Screen shot of certificate thumbprints in the portal][SecurityConfigurations\_02]

## Quitar un certificado
Este es el proceso para quitar un certificado antiguo de modo que el clúster no lo utilice:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y vaya a la configuración de seguridad del clúster.
2. Quite uno de los certificados.
3. Haga clic en **Guardar**; se iniciará una nueva implementación. Finalizada la nueva implementación, el certificado que quitó ya no se podrá usar para conectarse al clúster.

## Pasos siguientes
Lea estos artículos para más información sobre la administración de clúster:

- [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](service-fabric-cluster-upgrade.md)
- [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0720_2016-->