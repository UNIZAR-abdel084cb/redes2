## ACK 
* El tamaño es literalmente cabecera TCP (20B) + el resto de cabeceras
* Se envía una cada trama o una cada dos tramas
* Se tienen en cuenta en half-duplex y WIFI
	* En wifi no se le suma el backoff pero si el resto
* No se tiene en cuenta en full-duplex y ATM
* Tener en cuenta el envío mínimo en Ethernet
	* minimo de payload de 46B
* En wifi se trata como otro tdata pero sin tiempo de backoff

![[Pasted image 20251218202715.png]]
![[Pasted image 20251218203831.png]]

# Hector
![[Pasted image 20251218205016.png]]
![[Pasted image 20251218205027.png]]
