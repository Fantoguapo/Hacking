IP maquina mia 10.128.96.115
Ip maquina victima 10.128.164.193

Acceso a la web de la pagina HTTP://10.128.164.193

Imagen login pagina web
<img width="924" height="869" alt="image" src="https://github.com/user-attachments/assets/8a0aed79-499f-4b55-ab9d-e0d4f6c04e27" />

Abrimos el Burpsuit y activamos el proxi de nuestro navegador apuntando evidentemente al Burpsuit
<img width="1144" height="623" alt="image" src="https://github.com/user-attachments/assets/f4e700c0-7a11-4d18-aa0f-8b15bdb7e0f9" />

activamos el proxy en el burpuit
<img width="1130" height="850" alt="image" src="https://github.com/user-attachments/assets/fc9750d3-7ff6-49f5-88ee-daa0fb8cd25c" />

probaremos con la contraseña mas basica que en este caso es Admin admin
<img width="1915" height="840" alt="image" src="https://github.com/user-attachments/assets/d1bf5888-f507-4361-922f-ac83dea4f439" />

como vemos hemos ganado acceso a la pagina web como un usuario admin
<img width="1912" height="831" alt="image" src="https://github.com/user-attachments/assets/21729082-c11b-46e0-9e7f-6e1760dd6726" />

podemos ver que ene le panel derecho sale que la flag esta dentro de la ruta /internal/admin.php
<img width="507" height="494" alt="image" src="https://github.com/user-attachments/assets/4b4ae3e1-17ac-42ef-a1ba-2ad90f80c631" />

si intentamos acceder a esta pagina web  http://10.128.164.193/internal/admin.php vemos que nos sale un error indicando qe la pagina web solo es accesible de forma local, es decir desde la IP 127.0.0.1
<img width="1913" height="839" alt="image" src="https://github.com/user-attachments/assets/11b56421-3214-4946-9433-f5deba65183c" />

Investigando un poco la pagina vemos que hay un boton de export pdf
<img width="1909" height="853" alt="image" src="https://github.com/user-attachments/assets/ae0f8330-1c24-49e5-a78e-cf06d8003ad1" />

al darle vemos que nos ha generado un reporte con la IP 127.0.0.1/server-info.php
<img width="1907" height="803" alt="image" src="https://github.com/user-attachments/assets/e6b4b604-f72a-4427-80d5-16074bd7e44d" />

Podemos modificar la ruta a la que nos dirige la pagina cuando le damos al boton de export por eso volveremos a atras y le daremos otra vez al boton de export y entraremos a nuestro burpsuit y como podemos ver tenemos la URL a la que nos redirige
<img width="1915" height="806" alt="image" src="https://github.com/user-attachments/assets/fb56b751-858a-4047-beb7-583d8eaa88b3" />

la modificaremos para que en vez de llevarnos a server-info.php nos lleve a /internal/admin.php
<img width="1013" height="508" alt="image" src="https://github.com/user-attachments/assets/9f053101-fe1c-4bf6-b94a-6d59a39867ce" />

Le damos a Forward 
<img width="1141" height="694" alt="image" src="https://github.com/user-attachments/assets/b5ef6a2d-3050-46df-95aa-b032ec8f49d5" />

Y como podemos ver ya hemos podido acceder a la ruta /internal/admin.php y nos da la Flag
<img width="1913" height="840" alt="image" src="https://github.com/user-attachments/assets/57947fbb-9726-482d-8727-5a5f209c2eda" />

