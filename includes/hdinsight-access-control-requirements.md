Puede usar una suscripción de Azure de la que no sea el administrador ni el propietario como, por ejemplo, una suscripción de la empresa. Si este es el caso, debe comprobar que se ha obtenido lo siguiente para seguir los pasos descritos en este artículo:

* Acceso de colaborador. Para iniciar sesión en Azure, debe tener al menos acceso de colaborador al grupo de recursos de Azure. Este grupo de recursos se utiliza para crear un clúster de Azure HDInsight y otros recursos de Azure.
* Registro del proveedor. Un usuario con al menos acceso de Colaborador a la suscripción de Azure debe haber registrado previamente el proveedor para el recurso que está utilizando. El registro del proveedor se produce cuando un usuario con acceso de Colaborador a la suscripción crea un recurso por primera vez en la suscripción. También puede realizarse sin crear ningún recurso mediante el [registro de un proveedor con REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para más información sobre cómo trabajar con la administración de acceso, consulte los artículos siguientes:

* [Introducción a la administración de acceso en Azure Portal](../articles/active-directory/role-based-access-control-what-is.md)
* [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../articles/active-directory/role-based-access-control-configure.md)
