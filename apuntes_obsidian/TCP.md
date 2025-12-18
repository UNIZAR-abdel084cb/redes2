## ACK 
* El tamaño es literalmente cabecera TCP (20B) + el resto de cabeceras
* Se envía una cada trama o una cada dos tramas
* Se tienen en cuenta en half-duplex y WIFI
	* En wifi no se le suma el backoff pero si el resto
* No se tiene en cuenta en full-duplex y ATM
* Tener en cuenta el envío mínimo en Ethernet
	* minimo de payload de 46B
* En wifi se trata como otro tdata pero sin tiempo de backoff