## Dirección IP pública
Un recurso de dirección IP pública proporciona una dirección IP pública reservada o dinámica. Aunque puede crear una dirección IP pública como un objeto independiente, debe asociarlo a otro objeto para usar la dirección realmente. Puede asociar una dirección IP pública a un equilibrador de carga, puerta de enlace de aplicaciones o una NIC para proporcionar acceso a Internet a esos recursos.

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**publicIPAllocationMethod**|Define si la dirección IP es *estática* o *dinámica*.|estática, dinámica|
|**idleTimeoutInMinutes**|Define el tiempo de espera de inactividad.|cualquier valor entre 4 y 30|
|**ipAddress**|Dirección IP asignada al objeto. Se trata de una propiedad de solo lectura.|104\.42.233.77|

### Configuración de DNS
Las direcciones IP públicas tienen un objeto secundario denominado **dnsSettings** que contiene las propiedades siguientes:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**domainNameLabel**|Nombre de host usado para la resolución de nombres.|www, ftp, vm1|
|**reverseFqdn**|Nombre de dominio completo que se resuelve como la dirección IP y está registrado en DNS como un registro PTR.|www.contoso.com.|

Dirección IP pública de ejemplo en formato JSON:

	{
	   "name": "PIP01",
	   "location": "North US",
	   "tags": { "key": "value" },
	   "properties": {
	      "publicIPAllocationMethod": "Static",
	      "idleTimeoutInMinutes": 4,
		  "ipAddress": "104.42.233.77",
	      "dnsSettings": {
	         "domainNameLabel": "mylabel",
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

<!---HONumber=Sept15_HO4-->