# Práctica 1: Cifrado Simétrico y Asimétrico en Linux

En esta práctica vamos a poner en práctica el cifrado simétrico y asimétrico mediante diferentes comandos de Linux.

##  ✅ Entrega y evaluación

Para la entrega deberás generar un documento PDF donde incluyas capturas de todos los comandos realizados, así como del contenido de los ficheros generados, añadiendo explicaciones breves cuando lo creas conveniente.

## 🗝️ 1. Cifrado Simétrico

El **cifrado simétrico** es un método de criptografía en el que se utiliza la misma clave para cifrar y descifrar los datos. Es decir, tanto el emisor como el receptor deben tener acceso a la misma clave secreta para que el mensaje sea protegido y posteriormente interpretado.

Hay muchas herramientas y utilidades disponibles en relación al cifrado simétrico, pero en esta práctica usaremos el comando `openssl`. El comando ``openssl`` sirve para realizar tareas relacionadas con la criptografía y la gestión de certificados digitales. Permite generar claves, crear y firmar certificados, cifrar y descifrar datos, así como gestionar solicitudes de certificados (CSR). Para cifrar un mensaje usándolo, sigue los siguientes pasos (no olvides ir comprobando el contenido de todos los ficheros generados):

1. Crea un archivo con un mensaje secreto:

```
echo "Este es un mensaje secreto" > mensaje.txt
```

2. Cifra el archivo utilizando una clave compartida:

```
openssl enc -aes-256-cbc -in mensaje.txt -out mensaje_cifrado.txt -k "miclave123"
```

3. Descifra el archivo utilizando la misma clave:

```
openssl enc -d -aes-256-cbc -in mensaje_cifrado.txt -out mensaje_descifrado.txt -k "miclave123"
```

📬 Comparte el archivo cifrado con un compañero, junto con la clave, y verifica si puede descifrarlo.

## 🔐 2. Cifrado Asimétrico

El **cifrado asimétrico**, a diferencia del simétrico, utiliza un par de claves diferentes: una clave pública para cifrar la información y una clave privada para descifrarla. La clave pública se puede compartir libremente, mientras que la clave privada debe mantenerse en secreto, lo que permite establecer comunicaciones seguras sin necesidad de intercambiar una clave secreta previamente. Este tipo de cifrado es más lento que el simétrico.

En el caso de gpg, instalaremos una versión mínima las utilidades básicas:
```
sudo apt-get install gnupg
```

Para ponerlo en práctica usaremos el comando `gpg`. ``gpg`` permite cifrar y firmar datos y comunicaciones usando el cifrado asimétrico. Para ello, sigue los siguientes pasos:

1. Genera un par de claves:

```
gpg --full-generate-key
```

2. Exporta tu clave pública para compartirla:

```
gpg --export -a "tu_nombre" > clave_publica.asc
```

3. Importa la clave pública de un compañero:

```
gpg --import clave_publica_compañero.asc
```

4. Cifra un mensaje para tu compañero utilizando su clave pública:

```
echo "Hola, este es un mensaje secreto para ti" | gpg --encrypt --armor --recipient "nombre_compañero" > mensaje_para_ti.asc
```

5. Descifra el mensaje que recibas de tu compañero:

```
gpg --decrypt mensaje_para_ti.asc
```

##  💡 3. Explorando el Salt y las Iteraciones

### 🧂 3.1. El uso del Salt

El "salt" es un valor aleatorio que se añade a una contraseña antes de aplicar un algoritmo de hash para dificultar los ataques de diccionario y de fuerza bruta. El propósito del salt es hacer que las contraseñas cifradas sean únicas incluso si dos usuarios tienen la misma contraseña, ya que el valor aleatorio cambia el resultado del hash. Esto hace que sea mucho más difícil para un atacante obtener la contraseña original a partir de su hash, ya que necesitaría conocer el salt utilizado además de la contraseña. El salt se almacena junto con el hash de la contraseña para que pueda ser utilizado al momento de verificar la autenticidad de la misma.

Para aplicar el salt usaremos nuevamente `openssl`:

1. Cifra el mismo archivo dos veces con Salt activado:

```
openssl enc -aes-256-cbc -salt -in mensaje.txt -out mensaje_cifrado1.bin -k "miclave123"

openssl enc -aes-256-cbc -salt -in mensaje.txt -out mensaje_cifrado2.bin -k "miclave123"
```

2. Compara los archivos cifrados:

```
diff mensaje_cifrado1.bin mensaje_cifrado2.bin
```

3. Repite el proceso sin salt:

```
openssl enc -aes-256-cbc -nosalt -in mensaje.txt -out mensaje_cifrado3.bin -k "miclave123"

openssl enc -aes-256-cbc -nosalt -in mensaje.txt -out mensaje_cifrado4.bin -k "miclave123"

diff mensaje_cifrado3.bin mensaje_cifrado4.bin
```

### ⌛ 3.2. Iteraciones en el Cifrado

Las iteraciones en el contexto de la criptografía se refieren a la cantidad de veces que se aplica un algoritmo de hash o cifrado sobre los datos. Al aumentar el número de iteraciones, el proceso se vuelve más lento, lo que dificulta los ataques de fuerza bruta o de búsqueda de contraseñas, ya que el atacante necesita realizar más operaciones para descifrar la información. Por ejemplo:

1. Cifra el archivo con un número alto de iteraciones:

```
openssl enc -aes-256-cbc -salt -in mensaje.txt -out mensaje_iteraciones1.bin -k "miclave123" -iter 100000
```

2. Cifra el archivo con un número bajo de iteraciones:

```
openssl enc -aes-256-cbc -salt -in mensaje.txt -out mensaje_iteraciones2.bin -k "miclave123" -iter 1000
```

3. Mide el tiempo para descifrar cada archivo:

- Archivo con muchas iteraciones:

```
time openssl enc -d -aes-256-cbc -in mensaje_iteraciones1.bin -out mensaje_descifrado1.txt -k "miclave123" -iter 100000
```

- Archivo con menos iteraciones:

```
time openssl enc -d -aes-256-cbc -in mensaje_iteraciones2.bin -out mensaje_descifrado2.txt -k "miclave123" -iter 1000
```
