IP mi maquina 10.129.123.29
IP maquina victima 10.129.162.26

Para comenzar con el reconocimiento entraremos a la URL http://10.129.162.26 y veremos su contenido
<img width="1915" height="872" alt="image" src="https://github.com/user-attachments/assets/ed361e9f-1f85-4485-8737-fe389a13ff79" />

Vamos a hacer un reconocimiento de la pagina web y vemos que tenemos un apartado de Login
<img width="1915" height="905" alt="image" src="https://github.com/user-attachments/assets/80a3c9f8-8e85-4c00-bb96-a80e9a32f680" />

Dentro de este vemos que tenemos un panel donde tenemos que poner un user y una password http://10.129.162.26/login.html
<img width="1913" height="838" alt="image" src="https://github.com/user-attachments/assets/2920afb8-efcd-49ca-997e-4840a877ab70" />

Probamos con el login mas basico admin admin y este nos da error
<img width="1535" height="734" alt="image" src="https://github.com/user-attachments/assets/9685159b-db33-4a3f-adc6-6ac0a376d2dd" />

A continuacion abriremos burpsuit y lo activaremos en la web 
<img width="476" height="507" alt="image" src="https://github.com/user-attachments/assets/c8faad3d-f696-4f93-bfdb-1e372bf4ee84" />

Activaremos el interceptor
<img width="1916" height="851" alt="image" src="https://github.com/user-attachments/assets/349f9504-264f-41ab-a77c-20d0a45407a9" />

Le damos a login de nuevo y no encontramos nada
<img width="880" height="615" alt="image" src="https://github.com/user-attachments/assets/3c462ecf-1537-48d0-a301-7257dee324f6" />

Analizaremos ahora el cofigo de la pagina para comprobar que no haya nbada escondido para estro haremos un Ctrl + U 
<img width="1908" height="877" alt="image" src="https://github.com/user-attachments/assets/d133e135-b3b0-408a-9faf-023a8ac51de6" />

haremos un ctrl + f y buscaremos palabras claves como password, user, flag... vemos que username y password tiene asignado el id uanme = username y pass  = password
<img width="1066" height="456" alt="image" src="https://github.com/user-attachments/assets/80afec68-d356-4270-8601-c121b249d8c4" />

Filtramos por Uname con el Ctrl+F y vemos que nos lleva a un script que tiene la funcion de autentificacion y vemos que dentro de este script hay loq ue parece un usuario y una contraseña h3ck3rBoi 54321@terceSrepuS
<img width="1141" height="417" alt="image" src="https://github.com/user-attachments/assets/8e6c0949-47fd-46c3-9b6c-fa434007658f" />

Como la contraseña tiene una funcion llamada reverseString hace que la contraseña este dada la vuelta es por eos que utilizaremos el comadno *echo "54321@terceSrepuS" | rev*
<img width="928" height="99" alt="image" src="https://github.com/user-attachments/assets/b56f8723-65dc-4544-99a7-33fb331b3ae0" />

Ahora con la contraseña corecta SuperSecret@12345 prbaremos a iniciar sesion
<img width="968" height="652" alt="image" src="https://github.com/user-attachments/assets/0bbe51d1-9987-497e-a148-a9e64bcdf2df" />

Como podemos ver hemos ganado acceso y nos muestra la flag
<img width="1895" height="746" alt="image" src="https://github.com/user-attachments/assets/315a8387-c344-409d-af34-9fae64c8faa0" />
