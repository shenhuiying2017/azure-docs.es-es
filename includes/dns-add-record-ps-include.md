### Creación de un conjunto de registros AAAA con un único registro

	$rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros CNAME con un único registro

	$rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros MX con un único registro

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear el registro MX en el vértice de la zona (por ejemplo, "contoso.com"). Esto es común para los registros MX.

	$rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros NS con un único registro

	$rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros PTR con un único registro
En este caso 'my-arpa-zone.com' representa la zona ARPA que representa el intervalo de IP. Cada registro PTR establecido en esta zona se corresponde con una dirección IP dentro de este intervalo IP.

	$rs = New-AzureRmDnsRecordSet -Name "10" -RecordType PTR -Ttl 3600 -ZoneName my-arpa-zone.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ptrdname "myservice.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros SRV con un único registro

Si va a crear un registro SRV en la raíz de la zona, basta con especificar *\_service* y *\_protocol* en el nombre del registro. No es necesario incluir "@" en el nombre del registro.

	$rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Crear un conjunto de registros TXT con un único registro

	$rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

<!---HONumber=AcomDC_0928_2016-->