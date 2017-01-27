> No se puede usar una Red virtual de Azure v1 (clásica) con HDInsight basado en Linux. La Red virtual debe ser v2 (Administrador de recursos de Azure) para se muestre como opción durante el proceso de creación de un clúster de HDInsight en el Portal de vista previa de Azure o para que se pueda usar para crear un clúster en la CLI de Azure o Azure PowerShell.
> 
> Si tiene recursos en una red v1 y desea que dichos recursos puedan acceder directamente a HDInsight a través de una red virtual, consulte [Connect virtual networks from different deployment models using the portal](../articles/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) (Conexión de redes virtuales clásicas de distintos modelos de implementación mediante el portal) para obtener información acerca de cómo conectar una red virtual v2 a una red virtual v1. Una vez establecida la conexión, puede crear el clúster de HDInsight en la Red Virtual v2.
> 
> 



<!--HONumber=Jan17_HO3-->


