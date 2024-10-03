# Política de contraseñas

## Contexto
Por defecto, la mayoría de sistemas operativos, vienene con una política de contraseña muy laxa, una contraseña segura presenta una barrera más al atacante, que en su intento de acceder al sistema, indudablemete dejará más rastro y en algunos casos a provocar el abandondo de ataque.

## Objetivos
* Entender el sistema de contraseñas del sistema operativo linux
* Entender el PAM (Plugable authentication Module)
* Instalar y configurar la libreria _pwquality_
* Comprobar y verificar que se ha llevado la práctica de forma correcta
* Documentar la práctica.


### SISTEMA DE CONMTRASEÑAS DEL SISTEMA LINUX
En los sistemas de linux, el sistema de contraseñas se compoene de varios ficheros, como /etc/shadow y /etc/passwd.

En /etc/passwd se almacenan la configuracion de los usuarios, como nombre, direcotrio home, gmail...
En /etc/shadow se almacenena las contraseñas encriptadas

### PAM (Plugable authentication Module)
Son un conjunto de bibliotecas que permiten que un administrador de sistemas Linux configure métodos para autenticar usuarios . Proporciona una forma flexible y centralizada de cambiar los métodos de autenticación para aplicaciones seguras mediante el uso de archivos de configuración en lugar de cambiar el código de la aplicación.


### INSTALACION PWQUALITY y CONFIGURACION
Ejecutamos los comandos siguientes para instalar las herramientas necesarias:

``` shell
sudo apt-get install libpam-pwquality 

sudo apt install libpwquality-tools

```
Podemos comprovar que la isntalcion se ha relaizado si en el archvo */etc/pam.d/common-password* se encuentra la linea *password        [success=1 default=ignore]      **pam_unix.so** obscure use_authtok*

3 politicas de contraseñas para configurar:
- Politica debil (min 6, *)
- Politica media (min 8, [0-9], [A-Z], *)
- Politica externa (min 12, [0-9], [A-Z], [a-z], [#$_@])


El archivo de configiguracion para la politica de contraseñas es */etc/security/pwquality.conf*, donde modificaremos los parametros de las contraseñas.
Ejemplos:

**Politica debil (min 6, \*)**
``` shell
# Minimum acceptable size for the new password (plus one if
# credits are not disabled which is the default). (See pam_cracklib manual.)
# Cannot be set to lower value than 6.
 minlen = 6

```
Para comprobar la seguridad de las contraseñas (mas bien en relatividad a las politicas) introcucimos el comando *pwscore* y la contraseña a provar, al darle a enter nos dara si la contraseña cumple con la politica de contraseñas y el valor de su seguridad.

Ejemplos de pwscore:
``` 
vagrant@generic-sad:~$ pwscore
juan
Password quality check failed:
 The password is shorter than 6 characters


vagrant@generic-sad:~$ pwscore
adkfajdic
46

```


**Politica media (min 8, [0-9], [A-Z], \*)**
El archivo donde modificaremos los parametros de las contraseñas es */etc/security/pwquality.conf*

``` shell
# Minimum acceptable size for the new password (plus one if
# credits are not disabled which is the default). (See pam_cracklib manual.)
# Cannot be set to lower value than 6.
 minlen = 8
#
# The maximum credit for having digits in the new password. If less than 0
# it is the minimum number of digits in the new password.
 dcredit = -1
#
# The maximum credit for having uppercase characters in the new password.
# If less than 0 it is the minimum number of uppercase characters in the new
# password.
 ucredit = -1
```

Ejemplos de pwscore con la nueva politica:
```vagrant@generic-sad:~$ pwscore
ksu&h78DjY12_a.
72

vagrant@generic-sad:~$ pwscore 
meñasd
Password quality check failed:
 The password contains less than 1 digits
vagrant@generic-sad:~$ pwscore 
klña1   
Password quality check failed:
 The password contains less than 1 uppercase letters
vagrant@generic-sad:~$ pwscore 
asdkK1
Password quality check failed:
 The password is shorter than 8 characters
vagrant@generic-sad:~$ pwscore 
lakdsfiuA8
65
vagrant@generic-sad:~$ 
```

**Politica externa (min 12, [0-9], [A-Z], [a-z], [#$_@])**


``` shellsitios donde sentarse
# Minimum acceptable size for the new password (plus one if
# credits are not disabled which is the default). (See pam_cracklib manual.)
# Cannot be set to lower value than 6.
 minlen = 12
#
# The maximum credit for having digits in the new password. If less than 0
# it is the minimum number of digits in the new password.
 dcredit = -1
#
# The maximum credit for having uppercase characters in the new password.
# If less than 0 it is the minimum number of uppercase characters in the new
# password.
 ucredit = -1
#
# The maximum credit for having lowercase characters in the new password.
# If less than 0 it is the minimum number of lowercase characters in the new
# password.
 lcredit = -1
#
# The maximum credit for having other characters in the new password.
# If less than 0 it is the minimum number of other characters in the new
# password.
 ocredit = -1

```

Ejemplos con pwscore:
``` 
vagrant@generic-sad:~$ pwscore
asd
Password quality check failed:
 The password contains less than 1 digits
vagrant@generic-sad:~$ pwscore
asd1
Password quality check failed:
 The password contains less than 1 uppercase letters
vagrant@generic-sad:~$ pwscore
asd1D
Password quality check failed:
 The password contains less than 1 non-alphanumeric characters
vagrant@generic-sad:~$ pwscore
asd1D@
Password quality check failed:
 The password is shorter than 12 characters
vagrant@generic-sad:~$ pwscore
ksu&h78DjY12_a.
72
```

|    | score | long min | requisitos | ejemplos |
|----|-------|----------|------------|----------|
|  No valido | <40 | - | - | hola22 | 
| Devil | 40-50 | 6 | ucredit = -1 , dcredit = -1| jkm76Hg |
| Media | 50-80 | 8 | | jkm76HgP |
| Extrema | >80 | 10 | | @*kJsS3_1.0p |


El sistema de puntos funcionja como si por cada punto cuenta como un digito, ejemplo, si ponemos que un caracter especial suma un punto, podremos tenmr una contraseña de 9 caracters aun que el minimo sea 10.