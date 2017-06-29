## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Creación de una red virtual mediante un archivo de configuración de red desde PowerShell
Azure utiliza un archivo xml para definir todas las redes virtuales disponibles para una suscripción. Dicho archivo se puede descargar y editarlo para modificar o eliminar las redes virtuales existentes, y crear otras nuevas. En este tutorial, aprenderá a descargar dicho archivo, denominado archivo de configuración de red (o netcfg) y a editarlo para crear una red virtual nueva. Para más información acerca del archivo de configuración de red, consulte el artículo [Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Esquema de configuración de Azure Virtual Network).

Para crear una red virtual con un archivo netcfg mediante PowerShell, realice los siguientes pasos:

1. Si es la primera vez que usa Azure PowerShell, siga los pasos del artículo [Overview of Azure PowerShell](/powershell/azureps-cmdlets-docs) (Introducción a Azure PowerShell), inicie sesión en Azure y seleccione su suscripción.
2. En la consola de Azure PowerShell, use el cmdlet **AzureVnetConfig Get** para descargar el archivo de configuración de red a un directorio del equipo, para lo que debe ejecutar el siguiente comando: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Resultado esperado:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Abra el archivo que guardó en el paso 2 con cualquier aplicación de edición de texto o XML, y busque el elemento **<VirtualNetworkSites>**. Si ya tiene otras redes creadas, cada una de ellas se muestra como su propio elemento **<VirtualNetworkSite>**.
4. Para crear la red virtual que se describe en este escenario, agregue el siguiente código XML justo debajo del elemento **<VirtualNetworkSites>** :

   ```xml
        <VirtualNetworkSite name="TestVNet" Location="East US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
   ```
   
5. Guarde el archivo de configuración de red.
6. En la consola de Azure PowerShell, use el cmdlet **Set-AzureVnetConfig** para cargar el archivo de configuración de red, para lo que se debe ejecutar el siguiente comando: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Salida que se devuelve:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Si el valor de **OperationStatus** no es *Correcto* en la salida devuelta, compruebe si el archivo xml contiene errores y vuelva a realizar el paso 6.

7. En la consola de Azure PowerShell, use el cmdlet **Get-AzureVnetSite** para comprobar que se ha agregado la nueva red, para lo que debe ejecutar el siguiente comando: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   La salida devuelta (abreviada) incluye el texto siguiente:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
