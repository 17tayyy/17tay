---
title: "Como ser 100% anónimo en internet"
date: 2025-04-21
tags: ["privacidad", "tor", "tutorial", "vpn", "anonimización"]
categories: ["Anonimato"]
draft: false
cover:
  image: "/images/Post_anonimo/portada.png"
  alt: "Imagen del post"
  relative: false
---

## Índice

1. [Introducción](#introducción)
2. [Explicación](#explicación)  
   2.1. [¿Qué es una VPN?](#qué-es-una-vpn)  
   2.2. [Tor](#tor)  
   2.3. [¿Qué es el fingerprinting?](#qué-es-el-fingerprinting)  
   2.4. [Diferencia entre anonimato y privacidad](#qué-diferencia-hay-entre-anonimato-y-privacidad)  
   2.5. [¿Qué es una máquina virtual y por qué es útil?](#qué-es-una-máquina-virtual-y-por-qué-es-útil-en-este-contexto)
3. [Mi idea: "Anonimato en capas"](#mi-idea-anonimato-en-capas)


## Introducción

Hoy en dia, vivimos en una época donde cada **clic**, cada **búsqueda** y cada **conexión** que hacemos deja un **rastro**. Redes publicitarias, **corporaciones**, **mafias** y hasta **Gobiernos** pelean por tus **datos**, por saber quién eres, qué haces y por qué. La privacidad se ha convertido en un privilegio, y el **anonimato**... en una **lucha**

Muchos piensan que basta con activar la primera **VPN** que patrocina cualquier **youtuber** para **"ser anónimos"**. Pero la realidad es que el **anonimato online** no es una función que activas o desactivas, sino que es una **arquitectura** que tu mismo construyes. Y cuanto más consciente seas de cómo te rastrean, más **capas** podras aplicar para **protegerte**

En este post no solo voy a explicarte los **conceptos básicos** de **anonimato** y como funcionan herramientas como **VPNs**, **máquinas virtuales** o la **red Tor**. También voy a mostrarte cómo combinar todo esto en una estructura pensada para maximizar el **anonimato online**, utilizando una maquina host con **Mullvad**, una máquina virtual con **ProtonVPN**, y enrutar todo el tráfico a través de **Tor**.

Además he desarrollado una **herramienta en Bash** que automatiza una parte del proceso, y te la voy a **compartir**

## Explicación

Primero de todo voy a explicar un poco los **conceptos básicos** dentro del mundo de el **anonimato online** para que nadie se pierda.

### ¿Qué es una VPN?

Una **VPN (Red Privada Virtual)** es una **conexión de red privada** que crea un **"túnel" seguro y cifrado** entre tu dispositivo y un servidor remoto.

Básicamente, si hablamos de las **VPN comerciales** (es decir, las empresas que ofrecen este servicio), la explicación sencilla es que te permiten tener una primera capa de **seguridad** y **anonimato** utilizando sus servidores como **intermediarios** entre las **conexiones** que hagas.

Imagínate que estás de vacaciones en Estados Unidos y quieres ver **Netflix**, pero cuando entras ves que no es el mismo catálogo que en España. Pues uno de los usos más famosos que se les da a estas **VPNs** es justo ese. La **VPN** te permitiría hacer un **túnel seguro** entre tu **móvil** y, por ejemplo, un servidor de **ProtonVPN** que esté en **España**. Con lo cual, para **Netflix** tu conexión parecería venir desde **España**, no desde **Estados Unidos**.

Este sería un esquema básico del funcionamiento:

![esquema_vpn](/images/Post_anonimo/esquema_vpn_basico.png)

---

Ahora viene un tema muy hablado en este mundo: **"¿realmente esto es suficiente?"**

El problema aquí es que tú, como usuario, debes depositar tu **confianza** en la VPN que estés usando. Porque por mucho que la uses, si estas VPNs **guardan datos y registros** para luego **venderlos**, usarlas **no tiene ningún sentido**.

Aquí entran en juego las **VPNs no-logs**. Estas son VPNs que dicen que **no guardan ningún registro** sobre el uso que los usuarios les dan. Si esto es cierto, estas VPNs son muy buenas. Pero seguimos en el mismo dilema: **depositar tu confianza** en ellas. No sería la primera vez que descubren una supuesta **VPN no-logs** comercializando con los **datos de los usuarios**...

---

Yo voy a hacer mis recomendaciones en el mundo de las **VPNs**. Desde mi punto de vista, **solo te puedes fiar de dos VPNs en el mercado actual**:

- **ProtonVPN** (no-logs, **gratis**)  
- **Mullvad VPN** (no-logs, **de pago**, pero aceptan **criptomonedas** como **Monero**)

---

Para empezar, **Proton** no solo es una VPN: tienen la suite entera —**Proton Mail**, **Proton Drive**, **Proton Pass**, **Proton Wallet**...

**Proton** es una empresa **muy reconocida** en el mundo de la **ciberseguridad**, ya que se acoge a la **estricta ley de privacidad suiza**. Esto les permite ofrecer **muy buenos servicios** a sus usuarios.

La **VPN de Proton** es **muy buena** y **gratuita** (aunque también tienen planes de pago). Te permite crear una cuenta **sin dar ningún dato** y usar la VPN **sin límite**. Es mi **VPN favorita**. Además, se le han hecho **muchas auditorías** para comprobar si lo que dicen es verdad, y **todas han dado muy buenos resultados**.

---

**Mullvad**, por otro lado, es una empresa más enfocada únicamente a su **VPN** y a su **navegador**. Es **muy buena también**. La única pega es que es de **pago**, pero eso se compensa con que puedes **pagar en criptomonedas** como **Monero** (que está orientada a la **privacidad**).

Esta VPN no la he usado tanto como Proton, pero también está **muy bien**.

---

### Tor

**Tor**, la conocida **red cebolla** que te permite entrar a la **Deep Web**, no solo sirve para eso como la mayoría de la gente piensa. Esta red también se puede usar en tu dispositivo para **enrutar todo tu tráfico** a través de ella y salir a **Internet normal**, no al **Internet oscuro**.

Sería algo parecido a una **VPN**, pero dependiendo de cómo lo mires, **más segura y privada**. El único problema es que es **más difícil de usar** y **mucho menos compatible** con algunos servicios web.

El funcionamiento de la **red Tor** es bastante fácil de entender. Es una **red descentralizada** compuesta por **muchos nodos** que permiten la **navegación segura y privada**.

Básicamente, cuando haces una petición a través de **Tor**, primero viaja desde tu ordenador hasta un **primer nodo**, luego pasa a un **segundo nodo**, y después a un **tercer nodo**. Desde este tercer nodo, la petición ya se envía al **servidor web de destino**. La **respuesta** luego recorre el mismo camino pero **a la inversa**.

Esto permite que **nadie pueda ver el camino completo** de la comunicación. Ni el primer nodo sabe cuál es el destino final, ni el último nodo sabe de dónde vino la petición original.

---

**Esquema del funcionamiento:**

![esquema_tor](/images/Post_anonimo/esquema_red_tor.png)

Por este tipo de estructura, **Tor** es conocida como **la red cebolla**.
 
---

### ¿Qué es el fingerprinting?

El **fingerprinting** es una técnica que usan muchas páginas web y servicios online para **identificarte de forma única**, incluso si no estás logueado, no usas cookies y estás detrás de una VPN o Tor.

¿Cómo lo hacen? Pues básicamente recogen un **montón de detalles técnicos** sobre tu navegador, tu sistema operativo, resolución de pantalla, idioma, zona horaria, plugins, fuentes instaladas, etc. Todo eso forma un **perfil único** (o casi único) que pueden usar para **seguirte por Internet**.

Por ejemplo: si usas **Firefox en Linux**, con una resolución concreta, idioma en español, sin cookies, y con ciertos plugins activos… es probable que haya **muy poca gente** con esa misma combinación. Y eso te hace **rastreable**.

Por eso muchas veces **no basta con usar Tor o VPNs** si no configuras bien tu sistema y navegador. De hecho, el **navegador Tor** viene ya preparado para **bloquear la mayoría de técnicas de fingerprinting**, y por eso siempre se recomienda usarlo en lugar de otros navegadores "modificados".

---

### ¿Qué diferencia hay entre anonimato y privacidad?

Este punto es clave, porque mucha gente los confunde.

- **Privacidad** es cuando **controlas quién ve tu información**. Es como cerrar las cortinas en tu casa: no significa que nadie sepa dónde vives, pero **eliges no mostrar lo que haces dentro**.

- **Anonimato**, en cambio, es cuando **nadie sabe quién eres**. No es que escondas tu información, es que directamente **no hay forma de vincularla a ti**.

Por ejemplo:
- Usar **WhatsApp con tu número real** pero con mensajes cifrados de extremo a extremo = tienes **privacidad**, pero no **anonimato**.
- Postear en un foro de Tor sin cuenta, a través de un navegador Tor, desde una red pública = puedes tener **anonimato**, aunque no tengas tanta privacidad sobre lo que dices (si no lo cifras o proteges).

Lo ideal, en muchos casos, es **combinar ambos conceptos**: tener **privacidad** en tus datos y **anonimato** en tu identidad.

---

### ¿Qué es una máquina virtual y por qué es útil en este contexto?

Una **máquina virtual (VM)** es como un **ordenador dentro de tu ordenador**. Funciona como un sistema operativo completo, pero **encapsulado**, aislado del resto del sistema.

¿Para qué sirve esto en el mundo del anonimato?

Pues para aplicar una **capa extra de separación** entre tú (como persona física) y lo que estás haciendo online.

Por ejemplo: imagina que estás usando **tu sistema principal** con **Mullvad VPN**, y dentro de una **VM** tienes otro sistema con **ProtonVPN** + **Tor**. Todo lo que hagas en esa VM estará separado de tu sistema principal, y pasará por **dos capas de VPN** + la red **Tor**. Eso te da un nivel de **anonimato muy alto**, y además te protege de errores o fugas que puedan venir del sistema anfitrión, esta es la estructura que te voy a enseñar a desarrollar en este post.

Además, si algo va mal o se filtra dentro de la VM, puedes **borrarla, resetearla o crear otra nueva**, sin tocar tu sistema base. Es como tener una **identidad digital desechable**.

---

## Mi idea: "Anonimato en capas"

Ahora voy a plantear la **estructura** que yo considero la mejor teniendo en cuenta el **anonimato** y sin que sea demasiado complicada de usar.

La idea es básicamente tener tu **PC principal** conectado a una **VPN (en este caso, Mullvad)**. Dentro de tu PC, crearás una máquina virtual con la configuración de red en modo **NAT**, para que por defecto todo el tráfico de la VM pase primero por el host. Después, dentro de la **VM**, te conectarás a otra **VPN (en este caso, Proton)**, y finalmente enrutarás todo el tráfico a través de la **red Tor**.

---

Esquema de la estructura:

![alt text](/images/Post_anonimo/esquema_estructura.png)

---

###  Ventajas

- **Anonimato en capas reales**  
  Al usar **dos VPNs diferentes** (una en el host y otra en la VM), cada proveedor solo ve una parte del tráfico. Mullvad solo ve que te conectas a Proton, y Proton solo ve que te conectas a Tor. Ninguno puede ver el panorama completo.

- **Separación de entornos**  
  Al tener todo dentro de una **máquina virtual**, puedes separar tu actividad anónima del uso diario de tu PC. Cualquier cosa que hagas dentro de la VM **no afecta directamente** a tu máquina real. Esto es especialmente útil si algún día descargas algo raro o visitas webs chungas.

- **Aislamiento de IPs**  
  Tu **IP real nunca se expone**. Mullvad oculta tu IP real al mundo exterior, Proton oculta la IP de Mullvad, y Tor oculta la de Proton. Tres capas de ocultación antes de tocar Internet.

- **Más difícil de rastrear**  
  Aunque un adversario pueda ver alguna parte del tráfico, es casi **imposible correlarlo** con tu identidad sin tener acceso a **todas las capas a la vez** (y eso es muy complicado incluso para Gobiernos).

- **Ideal para tareas sensibles**  
  Si alguna vez necesitas hacer algo realmente privado o anónimo (consultar algo delicado, comunicarte de forma segura, etc.), este sistema te da una base muy sólida.

---

###  Desventajas

- **Rendimiento**  
  No te voy a mentir, esto **consume recursos**. Una VPN ya baja un poco la velocidad, pero si le sumas una segunda VPN, una VM y encima Tor... pues todo se vuelve más lento. Navegar irá bien, pero no esperes ver vídeos en 4K o jugar online.

- **Más complejo de configurar**  
  No es plug and play. Tienes que **entender cómo funciona una máquina virtual**, cómo se configura el adaptador NAT, cómo instalar y conectar las VPNs, y cómo enrutar el tráfico por Tor. No es para cualquiera, pero tampoco es un infierno si sabes un poco, ademas mas adelante en el post va a haber un tutorial.

- **No es infalible**  
  Aunque esta estructura es muy sólida, **no hay anonimato 100% garantizado**. Si cometes errores dentro de la VM (por ejemplo, loguearte en tu cuenta de Gmail real o abrir archivos personales), te delatas tú mismo. **El factor humano es el eslabón más débil**.

- **Incompatibilidades**  
  Algunos servicios online pueden **bloquear Tor o las VPNs**, pedir captchas todo el rato o directamente no funcionar. Y si necesitas instalar software dentro de la VM, puede que no todo funcione bien.


## Tutorial

Ahora, después de toda la explicación y de haber entendido cómo funciona todo por detrás, vamos a ponernos manos a la obra para montar este entorno y que sea funcional.

---

### Paso 1: **MullvadVPN**

> **Disclaimer:** Si no quieres pagar puedes saltarte esta parte, pero perderás una capa de seguridad. Si no quieres prescindir de ella, también puedes usar otra VPN gratuita.

Primero, debemos **crear una cuenta en Mullvad** y recargar saldo para poder utilizarla. En este [link oficial](https://mullvad.net/es/account/create) podéis hacerlo todo, y es muy fácil de seguir.

Se generará un **número de cuenta**, que será lo único que Mullvad tendrá para identificarnos. **Nada de correos, usuarios ni números de teléfono.**

![Mullvadaccount](/images/Post_anonimo/mullvadaccount.png)

Después seguiremos los pasos que nos indica Mullvad, recargaremos saldo y descargaremos la app. Una vez descargada, la interfaz es súper intuitiva y fácil de usar.

---

### Paso 2: **Máquina Virtual**

Ahora necesitaremos tener una **máquina virtual Linux** con la **configuración de red en NAT**. No voy a enseñar cómo crear una VM, hay miles de tutoriales en YouTube.

Captura de una Kali Linux en VMware con adaptador de red en NAT:

![kali](/images/Post_anonimo/kali.png)

---

### Paso 3: **ProtonVPN y mi herramienta *hideMe***

Para crear la cuenta de ProtonVPN voy a aprovechar y presentar ya mi herramienta llamada **hideMe**. Es un **script en Bash** que automatiza el enrutamiento de todo tu sistema a través de la red **Tor**. Además, tiene otras funcionalidades como conectarse a **ProtonVPN**, etc.

Primero, debemos clonarla:

```bash
git clone https://github.com/17tayyy/hideMe
```

Una vez clonada, la ejecutamos con:

```bash
./hideMe.sh
```

Deberíamos ver esto:

![hideme](/images/Post_anonimo/hideme.png)

Ahora escogeremos la **opción 5** para ver nuestra IP actual y la información que estamos dando a Internet. Si tienes **Mullvad activado en la máquina host** y todo está bien configurado, **no debería salirte la IP pública de tu casa**, sino una IP de Mullvad. También nos indicará que no estamos usando Tor.

![hideme1](/images/Post_anonimo/hideme1.png)

Después, seleccionamos la **opción 1 para activar Tor**:

![hideme2](/images/Post_anonimo/hideme2.png)

Volvemos a seleccionar la **opción 5** para comprobar si ha cambiado algo:

![hideme3](/images/Post_anonimo/hideme3.png)

Efectivamente, ahora **estamos usando Tor** y nuestra IP ha cambiado.

---

Con Tor activado, abrimos **Firefox** en la máquina y **creamos la cuenta de ProtonVPN** de manera más segura: [Crear cuenta Proton](https://account.proton.me/signup)

![createproton](/images/Post_anonimo/createproton.png)

Aquí puede aparecer un posible problema: nos piden un **correo de verificación**. Como no vamos a poner el nuestro (porque nos estaríamos vinculando), usaremos **un email temporal**.

Mi favorito es [Guerrilla Mail](https://www.guerrillamail.com), que te da un correo electrónico temporal. Si Proton lo bloquea, cambias el dominio desde la misma web y listo.

![guerrilla](/images/Post_anonimo/guerrilla.png)

Ponemos el código de verificación en Proton y ya estaremos dentro. Luego nos pedirá un método de recuperación, pero le damos a **"Maybe later"**. **No pongáis ningún método de recuperación.**

Tras acabar, llegamos al panel de Proton y pulsamos directamente en **ProtonVPN**, que es lo que nos interesa:

![proton2](/images/Post_anonimo/proton2.png)

---

### Paso 4: **Configurar ProtonVPN en hideMe**

En el panel de ProtonVPN necesitaremos dos cosas:

1. **Credenciales para conectarnos por OpenVPN**
2. **Archivos de configuración de OpenVPN**

Vamos al apartado de **OpenVPN**, y copiamos el **usuario y contraseña** en un archivo de texto llamado `creds.txt`, con el usuario en la primera línea y la contraseña en la segunda:

![proton3](/images/Post_anonimo/proton3.png)

Después, descargamos un archivo de configuración OpenVPN (más abajo en ese mismo apartado). Elegimos:

- **Plataforma:** GNU/Linux  
- **Protocolo:** UDP  
- Y el servidor más cercano a tu región.

Una vez tengas el archivo `.ovpn` y `creds.txt`, **asegúrate de que están en el mismo directorio que hideMe**:

![proton4](/images/Post_anonimo/proton4.png)

**Estructura del directorio:**

![estructura](/images/Post_anonimo/estructura.png)

---

### Paso 5: **Conectar a ProtonVPN y Tor**

Primero, **desconectamos Tor** con la **opción 3** de hideMe:

![tordisabled](/images/Post_anonimo/tordisabled.png)

Luego, seleccionamos la **opción 2: Connect to VPN**. El script chequeará si tenemos todo lo necesario instalado. Si lo hicimos bien, debería salir esto:

![successfull](/images/Post_anonimo/successfull.png)

Chequeamos la IP de nuevo:

![ipproton](/images/Post_anonimo/ipproton.png)

Vemos que tenemos la **IP de ProtonVPN** correctamente, y solo falta **volver a conectar a Tor**. Lo hacemos como antes y volvemos a comprobar la IP:

![porfin](/images/Post_anonimo/porfin.png)

---

### Ahora ya tendríamos todo el set-up montado para ser totalmente **anónimos**.

---

## Conclusión

El anonimato en Internet no es fácil, pero con la combinación adecuada de herramientas y hábitos, puedes acercarte mucho. Esta estructura que te he mostrado no es infalible, pero sí te da una **ventaja brutal** frente a la mayoría de usuarios.

Lo importante es que entiendas **cómo funciona cada capa**, y que seas consciente de **dónde puedes fallar**. No se trata de esconderse por paranoia, sino de **proteger tu identidad en un mundo donde todo está conectado**.

Si te mola este tipo de contenido, no dudes en seguirme, y si pruebas mi herramienta, dime qué te ha parecido **:)** (seguramente la mejorare en un futuro)



