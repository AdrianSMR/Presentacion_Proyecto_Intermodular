# Guión de Presentación · MFA y Hardening SSH (TecnoServicios S.L.)

Este guión está diseñado para una defensa de **40 minutos en total**, distribuido de la siguiente manera:
- **Exposición hablada + Demostración VM**: ~33 - 35 minutos.
- **Turno de preguntas del tribunal (Buffer)**: ~5 - 7 minutos.

---

## ⏱️ Tabla de Tiempos y Roles

| Diapositiva | Título de la Diapositiva | Ponente | Tiempo Estimado |
|:---:|:---|:---:|:---:|
| **1** | Portada / Presentación del proyecto | **ADRIÁN** | 1.5 min |
| **2** | El Problema y la Solución Planteada | **ADRIÁN** | 2.5 min |
| **3** | Soluciones Evaluadas | **ADRIÁN** | 2.5 min |
| **4** | La Solución Adoptada: Tres Capas de Defensa | **IVÁN** | 2.5 min |
| **5** | Tecnología Clave: Módulos PAM | **ADRIÁN** | 3.5 min |
| **6** | Tecnología Clave: Fail2Ban | **FRAN** | 3.0 min |
| **7** | Stack Tecnológico Completo | **FRAN** | 2.0 min |
| **8** | Interacción de Tecnologías: El Flujo de Acceso | **FRAN** | 3.0 min |
| **9** | Escenario de Demostración | **IVÁN** | 2.5 min |
| **10** | Demo 1 · El Ataque y la Respuesta de Fail2Ban | **IVÁN** | 4.0 min |
| **11** | Demo 2 · Acceso Legítimo con MFA | **IVÁN** | 4.0 min |
| **12** | Presupuesto Económico | **FRAN** | 2.5 min |
| **13** | Metodología de Trabajo | **FRAN** | 2.5 min |
| **14** | Identificación de Riesgos y Plan de Prevención | **IVÁN** | 2.0 min |
| **15** | Conclusiones y Recomendaciones | **FRAN (Conclusiones) / ADRIÁN (Recom.)** | 3.0 min (1.5 c/u) |
| **16** | Fuentes de Información | **ADRIÁN** | 1.5 min |
| **17** | Turno de Preguntas | **TODOS** | ~5-7 min (Buffer) |

---

## 🎙️ Guión Detallado Paso a Paso

---

### Diapositiva 1 · Portada / Presentación del proyecto
* **Ponente**: ADRIÁN
* **Tiempo asignado**: 1.5 minutos
* **Qué mostrar en pantalla**: El logo de TecnoServicios, el título del proyecto "MFA y Hardening SSH", y los nombres de los tres integrantes.
* **Guión hablado**:
  > "Buenos días a todos, miembros del tribunal y público asistente. Bienvenidos a la presentación de nuestro Proyecto Intermodular del segundo año de Sistemas Microinformáticos y Redes. Mi nombre es **Adrián Sánchez Martínez** y, junto a mis compañeros **Iván Sánchez Martínez** y **Francisco Zamora Abellán**, vamos a exponer el proyecto que hemos desarrollado para la empresa ficticia **TecnoServicios S.L.**, centrado en la implementación de un sistema de Doble Factor de Autenticación (MFA) y la fortificación, o hardening, de su acceso remoto a través del protocolo SSH.
  >
  > A lo largo de esta exposición, detallaremos cómo identificamos una vulnerabilidad crítica en la infraestructura de la empresa, las soluciones alternativas que evaluamos, la implementación técnica de la solución final, una demostración práctica en vivo de su funcionamiento y, por último, analizaremos la metodología de trabajo empleada, el control de riesgos y las conclusiones finales a las que hemos llegado.
  >
  > Comencemos analizando el problema de partida de la empresa..."
* **Transición**: *(Pasa a la diapositiva 2)*

---

### Diapositiva 2 · El Problema y la Solución Planteada
* **Ponente**: ADRIÁN
* **Tiempo asignado**: 2.5 minutos
* **Qué mostrar en pantalla**: Los diagramas que muestran los ataques de fuerza bruta/diccionario/botnets hacia el puerto por defecto 22 del servidor de TecnoServicios, y la restricción de diseño de no bloquear al equipo técnico.
* **Guión hablado**:
  > "En **TecnoServicios S.L.**, el servidor Linux central de la empresa, que aloja bases de datos operativas y configuraciones críticas, estaba expuesto directamente a internet utilizando la configuración de fábrica de SSH. Es decir, escuchaba en el puerto estándar TCP/22 y dependía exclusivamente del clásico sistema de usuario y contraseña.
  >
  > Al auditar los registros de seguridad, detectamos ataques de fuerza bruta constantes e incesantes dirigidos al usuario **root** y a otras cuentas de administración. Estos ataques, lanzados por botnets automatizadas desde todas partes del mundo, prueban miles de combinaciones de contraseñas por minuto usando diccionarios de palabras comunes. Si un atacante lograba dar con la contraseña de alguna cuenta mediante este método por fuerza bruta, obtendría control absoluto del servidor, lo que pondría en grave riesgo la integridad de los datos y los servicios de la empresa.
  >
  > Ante esta situación, la dirección técnica nos planteó el reto de implementar una capa de seguridad robusta para proteger el acceso de administración. Sin embargo, nos impusieron una condición innegociable: **la solución no debía bloquear ni entorpecer el flujo de trabajo diario de los administradores y técnicos**. Debía ser altamente seguro, pero ágil y fácil de usar en la operativa diaria."
* **Transición**: *(Pasa a la diapositiva 3)*

---

### Diapositiva 3 · Soluciones Evaluadas
* **Ponente**: ADRIÁN
* **Tiempo asignado**: 2.5 minutos
* **Qué mostrar en pantalla**: La comparativa entre las claves SSH (descartada), Port Knocking (descartada) y la solución elegida (2FA + Fail2Ban + Cambio de puerto).
* **Guión hablado**:
  > "Para resolver el problema, analizamos y evaluamos tres alternativas tecnológicas principales:
  >
  > 1. La primera opción fue el uso de **Claves SSH** mediante criptografía asimétrica. Aunque ofrece una alta seguridad en el canal remoto al eliminar el tránsito de contraseñas por la red, la descartamos. La gestión y distribución de pares de claves públicas y privadas añade mucha fricción operativa para un equipo técnico pequeño que puede experimentar rotación de personal. Además, si un técnico pierde su llave privada o se la roban de su equipo físico local, el servidor queda comprometido.
  >
  > 2. La segunda opción fue el **Port Knocking** o 'golpeo de puertos'. Consiste en mantener el puerto SSH cerrado en el cortafuegos hasta que el cliente envíe una secuencia específica de paquetes a otros puertos para 'tocar a la puerta' y abrirlo temporalmente. Aunque oculta eficazmente el servicio SSH frente a escáneres, es una técnica de seguridad por ocultación que requiere un software de cliente especial y una gestión muy compleja de reglas dinámicas en el cortafuegos. Si un técnico comete un error en la secuencia o la red experimenta latencia, se le deniega el acceso, lo que ralentiza el trabajo.
  >
  > 3. Por ello, optamos por la tercera solución: **Doble Factor de Autenticación (MFA) basado en TOTP, complementado con Fail2Ban y un cambio de puerto**. Esta combinación nos proporciona una seguridad real de dos factores (algo que sé, que es la contraseña; y algo que tengo, que es mi móvil), bloqueo automatizado ante ataques y un cambio de puerto que evade escaneos oportunistas. Todo esto con un coste cero en licencias de software al ser soluciones open-source.
  >
  > Le cedo la palabra a mi compañero **Iván** para que nos detalle la arquitectura de la solución adoptada."
* **Transición**: "Muchas gracias, Adrián. Buenos días a todos. A continuación, les explicaré..." *(Pasa a la diapositiva 4)*

---

### Diapositiva 4 · La Solución Adoptada: Tres Capas de Defensa
* **Ponente**: IVÁN
* **Tiempo asignado**: 2.5 minutos
* **Qué mostrar en pantalla**: La caja con las tres capas (Evasión / Prevención Activa / Autenticación Fuerte) y el sistema base Ubuntu Server 24.04 LTS.
* **Guión hablado**:
  > "Gracias, Adrián. Buenos días a todos. Como ha comentado mi compañero, estructuramos nuestra defensa en torno a tres capas que actúan de manera consecutiva:
  >
  > - **La Capa 1 es la de Evasión**: La primera medida, y la más rápida, fue trasladar el servicio SSH fuera del puerto estándar 22 al puerto personalizado **52222**. Con esto evitamos el 99% de los escaneos automatizados de botnets y herramientas de descubrimiento de hosts como Shodan o Masscan, reduciendo drásticamente el ruido en los logs del servidor.
  >
  > - **La Capa 2 es la de Prevención Activa**: Implementada mediante el cortafuegos UFW y el servicio Fail2Ban. Fail2Ban actúa como un vigilante de logs en tiempo real. Si detecta 3 intentos fallidos de conexión desde una misma IP en un lapso de 10 minutos, inyecta de forma inmediata una regla restrictiva en UFW que bloquea (hace un DROP) a esa IP durante 24 horas.
  >
  > - **La Capa 3 es la de Autenticación Fuerte**: Es nuestra barrera definitiva. Aunque un atacante consiga averiguar la contraseña de un usuario mediante ingeniería social u otros métodos, necesitará introducir un código temporal de 6 dígitos que expira cada 30 segundos (TOTP) generado en la aplicación móvil de código abierto Proton Authenticator de los administradores.
  >
  > Toda esta arquitectura de seguridad ha sido configurada y probada sobre la distribución oficial **Ubuntu Server 24.04 LTS**. Al ser una versión con soporte a largo plazo, nos asegura estabilidad y parches de seguridad constantes por un período de 5 años.
  >
  > Le devuelvo la palabra a Adrián para explicar los detalles técnicos del funcionamiento de los módulos PAM."
* **Transición**: "Gracias, Iván. Pasemos a analizar el corazón del sistema de autenticación..." *(Pasa a la diapositiva 5)*

---

### Diapositiva 5 · Tecnología Clave: Módulos PAM
* **Ponente**: ADRIÁN
* **Tiempo asignado**: 3.5 minutos
* **Qué mostrar en pantalla**: La definición de módulos PAM, su estructura de configuración, y el flujo visual donde se pide la contraseña y posteriormente el código de verificación al usuario.
* **Guión hablado**:
  > "Gracias, Iván. Para poder implementar este sistema de doble factor en Ubuntu de una forma robusta y nativa, recurrimos a los **módulos PAM**, que significan *Pluggable Authentication Modules* (Módulos de Autenticación Enchufables).
  >
  > PAM es un marco de trabajo flexible presente en la mayoría de sistemas de tipo Unix que centraliza las funciones de autenticación. Actúa como un intermediario entre los programas que piden autenticación (como SSH, sudo o el login del sistema) y los propios mecanismos de comprobación de credenciales. La gran ventaja es que permite añadir reglas, controles o pasos de verificación (como el doble factor) editando simples ficheros de configuración de texto plano en `/etc/pam.d/` sin necesidad de recompilar o modificar el código fuente de los programas.
  >
  > En nuestro proyecto, editamos el archivo `/etc/pam.d/sshd` para inyectar el módulo `pam_google_authenticator.so`. 
  >
  > El flujo de autenticación funciona de la siguiente manera:
  > 1. El usuario inicia su cliente SSH y solicita conectarse al servidor. El demonio de SSH delega la tarea de verificar la identidad en la biblioteca de PAM.
  > 2. PAM lee el archivo de configuración de SSH y ejecuta secuencialmente los módulos indicados. Primero, solicita y valida la **contraseña del usuario** contrastándola con la base de datos del sistema.
  > 3. Si la contraseña es válida, PAM pasa a la siguiente regla de la pila, que corresponde a Google Authenticator. Este genera un desafío y solicita en pantalla un código de verificación de 6 dígitos.
  > 4. El técnico consulta la aplicación **Proton Authenticator** en su teléfono móvil y escribe el código TOTP activo.
  > 5. El módulo PAM compara el código recibido con los datos internos del archivo local `~/.google_authenticator` del usuario en el servidor. Si coincide, PAM valida el paso y le devuelve una respuesta de éxito a SSHD.
  > 6. Finalmente, se le concede acceso a la consola de comandos al técnico de forma segura.
  >
  > A continuación, Francisco nos explicará la otra herramienta clave de nuestra infraestructura: Fail2Ban."
* **Transición**: "Muchas gracias, Adrián. A continuación, analizaremos el papel de Fail2Ban..." *(Pasa a la diapositiva 6)*

---

### Diapositiva 6 · Tecnología Clave: Fail2Ban
* **Ponente**: FRAN
* **Tiempo asignado**: 3.0 minutos
* **Qué mostrar en pantalla**: Definición de Fail2Ban, el fragmento de código de la jaula configurada para SSHD y el diagrama del flujo que culmina en el bloqueo de UFW.
* **Guión hablado**:
  > "Gracias, Adrián. Buenos días a los miembros del tribunal. Yo les voy a explicar cómo protegemos activamente la red de nuestro servidor frente a intentos repetitivos de acceso no autorizado. Para ello, implementamos la herramienta **Fail2Ban**.
  >
  > Fail2Ban es un framework de prevención de intrusiones de código abierto que actúa como un centinela perimetral. Su trabajo consiste en escanear constantemente los archivos de registro (logs) del sistema operativo en busca de patrones sospechosos, como inicios de sesión fallidos repetitivos.
  >
  > Funciona mediante **jaulas o 'jails'**. Para este proyecto, configuramos una jaula personalizada llamada `[sshd]` dentro del archivo `/etc/fail2ban/jail.local`, especificando los siguientes parámetros de seguridad:
  > - Le indicamos a la herramienta que vigile el puerto personalizado **52222**.
  > - Establecimos el parámetro `findtime` en 10 minutos, que es la ventana de tiempo en la que se evalúan los fallos.
  > - Definimos `maxretry` en 3, que es el número máximo de intentos fallidos permitidos antes de sancionar.
  > - Y configuramos un `bantime` de 24 horas, que es la penalización temporal de bloqueo.
  >
  > Cuando un atacante intenta adivinar una credencial, cada intento fallido genera una línea en el archivo `/var/log/auth.log` de Ubuntu con el texto: *'Failed password for root from [IP] port 52222'*.
  >
  > Fail2Ban analiza el archivo en tiempo real y, en el momento en que una misma dirección IP acumula 3 fallos en menos de 10 minutos, ejecuta la acción de baneo. Esta acción consiste en llamar al cortafuegos local **UFW** e insertar de forma automática una regla dinámica que bloquea todo el tráfico entrante de esa dirección IP. Al cumplirse las 24 horas, Fail2Ban retira automáticamente la regla de bloqueo del cortafuegos, restableciendo el acceso."
* **Transición**: *(Pasa a la diapositiva 7)*

---

### Diapositiva 7 · Stack Tecnológico Completo
* **Ponente**: FRAN
* **Tiempo asignado**: 2.0 minutos
* **Qué mostrar en pantalla**: La cuadrícula con las 6 herramientas: OpenSSH, Fail2Ban, Google-authenticator/Proton Authenticator, Chrony (NTP), libqrencode4 y UFW.
* **Guión hablado**:
  > "Para llevar a cabo con éxito todo este proyecto, diseñamos e integramos un conjunto de herramientas de software de código abierto que trabajan en perfecta sintonía:
  >
  > 1. En primer lugar, tenemos **OpenSSH-Server**, que es la suite que provee el servicio de conectividad remota a nivel de terminal y que fortificamos mediante las directivas del sistema.
  > 2. En segundo lugar, **Fail2Ban**, que monitoriza las conexiones y mitiga los ataques de fuerza bruta perimetralmente.
  > 3. En tercer lugar, el módulo **Google-Authenticator-libpam** acoplado al servidor y la aplicación **Proton Authenticator** para móviles, encargados de la autenticación de dos pasos.
  > 4. En cuarto lugar, **Chrony (NTP)**: Esta herramienta es fundamental. La tecnología TOTP genera códigos de seguridad basados en el tiempo utilizando el algoritmo estándar RFC 6238. Si los relojes del servidor y del móvil del técnico no están perfectamente sincronizados, los códigos generados diferirán y se denegará el acceso legítimo. Chrony mantiene el reloj del servidor sincronizado con precisión de milisegundos con los servidores de hora mundiales.
  > 5. En quinto lugar, **libqrencode4**: Una librería que traduce la clave secreta generada en un código QR legible directamente en el terminal. Esto facilita que cuando agregamos un nuevo técnico al sistema, este pueda escanear su código QR en segundos con la cámara de su móvil.
  > 6. Y por último, **UFW (Uncomplicated Firewall)**, el cortafuegos perimetral del servidor que controla los puertos y aplica los bloqueos dictados por Fail2Ban."
* **Transición**: *(Pasa a la diapositiva 8)*

---

### Diapositiva 8 · Interacción de Tecnologías: El Flujo de Acceso
* **Ponente**: FRAN
* **Tiempo asignado**: 3.0 minutos
* **Qué mostrar en pantalla**: El flujo paso a paso desde el intento de conexión hasta el acceso concedido o denegado.
* **Guión hablado**:
  > "En esta diapositiva podemos observar cómo interactúan todas las tecnologías del stack de forma unificada cuando se produce una petición de conexión SSH remota:
  >
  > 1. En el primer paso, el usuario inicia la conexión desde su terminal cliente enviando una petición TCP dirigida al puerto personalizado **52222** del servidor.
  > 2. Antes de que el servidor analice las contraseñas, la IP del usuario debe superar el filtro perimetral de **UFW y Fail2Ban**. Si la IP del solicitante se encuentra en la lista negra de baneados, el cortafuegos bloquea el paquete inmediatamente, denegando la comunicación. Si está libre, la petición pasa al servicio SSHD.
  > 3. El servicio SSHD solicita las credenciales básicas de acceso. Se produce la **autenticación con contraseña**. Si el usuario ingresa una contraseña incorrecta, el sistema registra el fallo en `auth.log` (lo que puede provocar un bloqueo de Fail2Ban tras 3 intentos). Si la contraseña es válida, el flujo continúa.
  > 4. En este punto, SSHD delega el siguiente paso de validación en la pila de **PAM (MFA)**, el cual genera en la consola un desafío solicitando el código de autenticación de un solo uso.
  > 5. El usuario consulta su aplicación Proton Authenticator, que le proporciona el token dinámico de 6 dígitos que expira cada 30 segundos, completando la **verificación TOTP**.
  > 6. Si el código ingresado coincide con el del servidor, se completa el acceso, se concede la shell interactiva de comandos al usuario y se registra la sesión exitosa en los logs del sistema para auditoría.
  >
  > Ahora doy paso a Iván para detallar el laboratorio de pruebas y guiarnos en la demostración práctica de este flujo."
* **Transición**: "Muchas gracias, Francisco. A continuación, presentaré el escenario virtual..." *(Pasa a la diapositiva 9)*

---

### Diapositiva 9 · Escenario de Demostración
* **Ponente**: IVÁN
* **Tiempo asignado**: 2.5 minutos
* **Qué mostrar en pantalla**: El esquema de red con el Servidor Ubuntu (IP `.10`), el switch virtual en la red interna, el técnico legítimo Windows (IP `.20`) y el atacante Lubuntu (IP `.30`).
* **Guión hablado**:
  > "Gracias, Francisco. Para comprobar empíricamente la efectividad de las tres capas de defensa que hemos diseñado, creamos un escenario de pruebas en un entorno virtualizado utilizando VirtualBox.
  >
  > Configuramos una red interna privada en el rango IP `192.168.100.0/24` donde conviven tres máquinas virtuales:
  >
  > 1. En primer lugar, tenemos el **Servidor Central de TecnoServicios**, montado sobre Ubuntu Server 24.04 LTS en la dirección IP `192.168.100.10`. Este servidor tiene el puerto SSH modificado al 52222, tiene activado el cortafuegos UFW y los servicios Fail2Ban, Chrony para la sincronización y la autenticación doble factor PAM habilitada para los usuarios.
  > 2. En segundo lugar, contamos con la máquina del **Técnico Legítimo**, un sistema Windows 10 en la IP `192.168.100.20`. Este equipo simula el puesto de trabajo de un administrador de la empresa que posee las credenciales válidas y tiene el token TOTP configurado en su móvil con la aplicación Proton Authenticator.
  > 3. En tercer lugar, la máquina del **Atacante Hostil**, que funciona sobre Lubuntu en la dirección IP `192.168.100.30`. Esta máquina representa un ordenador comprometido o un atacante en la red local que intentará descifrar la contraseña de administración del servidor lanzando ataques automatizados de fuerza bruta sobre el puerto 52222.
  >
  > A continuación, vamos a simular los dos casos de uso sobre este laboratorio virtualizado para ver la reacción de nuestro sistema."
* **Transición**: *(Pasa a la diapositiva 10)*

---

### Diapositiva 10 · Demo 1 · El Ataque y la Respuesta de Fail2Ban
* **Ponente**: IVÁN
* **Tiempo asignado**: 4.0 minutos
* **Qué mostrar en pantalla**: Las capturas de terminal de la máquina atacante Lubuntu y la respuesta de baneo reflejada en el status de Fail2Ban del servidor.
* **Guión hablado**:
  > *(Instrucciones para la presentación: Si estás mostrando la presentación desde las diapositivas de GitHub, señala las capturas de pantalla de la terminal. Si tienes las máquinas virtuales abiertas en vivo, cambia de ventana a las VM y haz la demo real ejecutando los comandos explicados).*
  >
  > "En esta primera demostración, asumiremos el papel de atacante desde la máquina Lubuntu con la IP `.30`. 
  >
  > El atacante intenta acceder al servidor mediante el comando `ssh adrian@192.168.100.10 -p 52222`. Dado que no tiene las credenciales del técnico ni acceso al dispositivo físico del token TOTP, introduce una contraseña aleatoria de un diccionario y un código dinámico falso.
  >
  > El servidor rechaza la conexión de inmediato y le solicita las credenciales por segunda vez. El atacante insiste, introduciendo un segundo fallo, y luego un tercero. Al fallar el tercer intento consecutivo, el servidor cierra la conexión remota con el mensaje `Permission denied (keyboard-interactive)`.
  >
  > Lo interesante viene ahora: si el atacante intenta volver a lanzar el comando SSH inmediatamente después, la terminal se queda congelada durante unos segundos y finalmente el sistema operativo le deniega la conexión mostrando el error: `Connection refused` o `Connection timed out`.
  >
  > Si nos trasladamos a la terminal de administración del servidor y consultamos el estado del centinela con `sudo fail2ban-client status sshd`, podemos auditar lo sucedido. Como se observa en la columna derecha de la pantalla, Fail2Ban ha detectado los 3 fallos consecutivos registrados en `/var/log/auth.log`, ha identificado la dirección IP hostil `192.168.100.30` y la ha añadido automáticamente a la lista de baneados temporales. A nivel de red, si ejecutamos `sudo ufw status`, se ve claramente cómo el cortafuegos descarta ahora activamente cualquier paquete proveniente del atacante."
* **Transición**: *(Pasa a la diapositiva 11)*

---

### Diapositiva 11 · Demo 2 · Acceso Legítimo con MFA
* **Ponente**: IVÁN
* **Tiempo asignado**: 4.0 minutos
* **Qué mostrar en pantalla**: Capturas de pantalla de la terminal PowerShell de Windows con el acceso correcto introduciendo el código TOTP de Proton Authenticator, y la sesión del servidor iniciada.
* **Guión hablado**:
  > *(Instrucciones para la presentación: Al igual que la anterior, explica la demo con las capturas o hazla en vivo sobre las máquinas virtuales).*
  >
  > "En esta segunda demostración, asumiremos el papel del técnico legítimo de la empresa desde su puesto de trabajo con Windows 10 en la IP `.20`.
  >
  > El técnico abre su terminal PowerShell y ejecuta el comando de conexión `ssh adrian@192.168.100.10 -p 52222`. En primer lugar, introduce su contraseña de usuario habitual de forma correcta.
  >
  > A continuación, el servidor le presenta el desafío de seguridad, solicitando en pantalla el mensaje *'Verification code'*. El técnico abre la aplicación Proton Authenticator en su móvil, busca el perfil de TecnoServicios y lee el código numérico dinámico de 6 dígitos que se está mostrando en ese instante. Lo introduce en la consola y presiona Enter.
  >
  > Como pueden ver en la parte derecha de la diapositiva, el servidor valida el código temporal de inmediato y nos da la bienvenida mostrando la información del sistema de Ubuntu Server, confirmando la apertura correcta de una shell remota. 
  >
  > Esta prueba nos demuestra que la fortificación implementada es extremadamente robusta ante ataques externos sin romper la usabilidad que nos exigía la dirección de la empresa: el técnico legítimo sólo tarda 5 segundos adicionales en acceder de forma segura a su espacio de trabajo.
  >
  > Le devuelvo la palabra a mi compañero Francisco para que nos desglose los costes financieros del proyecto."
* **Transición**: "Muchas gracias, Iván. A continuación, analizaremos el presupuesto..." *(Pasa a la diapositiva 12)*

---

### Diapositiva 12 · Presupuesto Económico
* **Ponente**: FRAN
* **Tiempo asignado**: 2.5 minutos
* **Qué mostrar en pantalla**: Las tablas de costes de software (0 EUR) y costes de personal (900 EUR), y el desglose de horas del equipo técnico.
* **Guión hablado**:
  > "Gracias, Iván. Un criterio muy importante a la hora de diseñar este sistema de seguridad ha sido la viabilidad económica y la optimización de los costes financieros para TecnoServicios S.L.
  >
  > Para ello, dividimos nuestro presupuesto en dos grandes bloques:
  >
  > 1. En primer lugar, los **Costes de Software**: Al basar el 100% de la arquitectura técnica en tecnologías de código abierto (Open Source), los costes de adquisición de licencias y suscripciones son de **0 euros**. Hemos utilizado herramientas potentes e integradas de forma libre en la comunidad empresarial, como el hipervisor VirtualBox, el sistema operativo Ubuntu Server, y los módulos de SSH, Fail2Ban, PAM, Chrony y las aplicaciones móviles de autenticación.
  >
  > 2. En segundo lugar, los **Costes de Personal**: El único coste real del proyecto deriva de las horas de ingeniería invertidas por nuestro equipo técnico para el análisis de necesidades, diseño de la arquitectura, configuración, despliegue del laboratorio y redacción de la documentación. Hemos valorado el proyecto en un total de **60 horas de trabajo** de ingeniería (distribuidas en 20 horas por cada uno de los 3 integrantes del grupo) a un precio de mercado muy competitivo de **15 euros la hora**.
  >
  > Multiplicando las 60 horas totales de desarrollo por los 15 euros por hora, el coste de personal asciende a un total de **900 euros**.
  >
  > En resumen, el presupuesto final de ejecución del proyecto es de **900 euros**. Esto demuestra que una empresa pequeña o mediana puede implementar sistemas de seguridad perimetral de nivel corporativo muy elevados sin incurrir en grandes costes de software propietario."
* **Transición**: *(Pasa a la diapositiva 13)*

---

### Diapositiva 13 · Metodología de Trabajo
* **Ponente**: FRAN
* **Tiempo asignado**: 2.5 minutos
* **Qué mostrar en pantalla**: La explicación del marco ágil SCRUM, el tablero Kanban de Trello con los Sprints y las columnas de tareas, y los logos de Discord y WhatsApp como herramientas de coordinación.
* **Guión hablado**:
  > "Para garantizar que el proyecto se desarrollara dentro de los plazos establecidos y cumpliera con los estándares de calidad técnicos, adoptamos una metodología de trabajo ágil basada en el marco **SCRUM**.
  >
  > El proyecto se organizó en varios ciclos iterativos de corta duración llamados **Sprints**. Para llevar a cabo el seguimiento visual diario y coordinar las tareas individuales, utilizamos la herramienta **Trello**.
  >
  > Diseñamos un tablero Kanban estructurado con columnas que representaban el flujo de vida de cada tarea: desde la recopilación de ideas en el 'Product Backlog', pasando por las tareas planificadas para el Sprint actual, las tareas 'En Proceso' que estábamos ejecutando en ese momento, y la columna final de tareas 'Hechas'. Esto nos dio una trazabilidad absoluta de las decisiones de diseño tomadas.
  >
  > Para la comunicación interna del grupo técnico, nos apoyamos en dos plataformas principales:
  > - **Discord**: Que sirvió como nuestra oficina virtual. Lo utilizamos para mantener reuniones de planificación de Sprints, compartir pantallas para la depuración colaborativa de configuraciones complejas de red y realizar sesiones de programación en pareja (*pair-programming*).
  > - **WhatsApp**: Lo reservamos como un canal ágil e inmediato para notificaciones urgentes, coordinar horarios de reunión rápidos o avisar de actualizaciones importantes en el tablero de Trello.
  >
  > Esta metodología ágil nos aportó una gran velocidad de desarrollo y nos permitió mitigar rápidamente los problemas técnicos en cuanto aparecían."
* **Transición**: "Le devuelvo la palabra a Iván para analizar la gestión de riesgos operacionales y planes de contingencia." *(Pasa a la diapositiva 14)*

---

### Diapositiva 14 · Identificación de Riesgos y Plan de Prevención
* **Ponente**: IVÁN
* **Tiempo asignado**: 2.0 minutos
* **Qué mostrar en pantalla**: La tabla que resume las actividades del proyecto, sus riesgos físicos u operativos identificados y las medidas preventivas adoptadas.
* **Guión hablado**:
  > "Gracias, Francisco. A la par del despliegue técnico, elaboramos una matriz de identificación de riesgos y prevención para proteger tanto la salud del equipo de trabajo como la integridad del servidor de producción:
  >
  > 1. En primer lugar, identificamos el riesgo de **fatiga visual y postural** derivado de las largas jornadas de trabajo frente a las pantallas. Como medida preventiva, adoptamos la regla de realizar pausas activas de 10 minutos por cada hora de desarrollo y mantuvimos una correcta ergonomía física en las estaciones de trabajo.
  >
  > 2. En segundo lugar, evaluamos el riesgo en la **configuración de ficheros críticos** como `/etc/ssh/sshd_config` o los archivos de configuración de PAM. Un simple fallo sintáctico en estos archivos puede provocar que el servicio SSH caiga o nos deniegue el acceso como root de forma irreversible, bloqueándonos fuera del servidor permanentemente. Para prevenirlo, aplicamos de forma estricta dos medidas: realizar instantáneas (*snapshots*) de la máquina virtual en VirtualBox antes de tocar archivos críticos, y mantener siempre una sesión de terminal SSH local abierta de forma activa y en paralelo como administrador para verificar la conectividad antes de cerrar la terminal principal.
  >
  > 3. En tercer lugar, el riesgo ante la **pérdida o daño del dispositivo móvil con el token MFA**. La medida adoptada es el almacenamiento seguro de los códigos de recuperación de emergencia generados por PAM durante el registro inicial del usuario, lo que permite el acceso alternativo en caso de extravío del teléfono.
  >
  > 4. Y por último, el riesgo de **sobrecarga de hardware (RAM/CPU)** en el ordenador físico anfitrión al arrancar varias máquinas virtuales en paralelo. Lo solventamos configurando recursos mínimos ajustados en VirtualBox (por ejemplo, asignando solo 1 GB de RAM a Lubuntu y 1.5 GB a Ubuntu Server) y cerrando procesos secundarios innecesarios en el ordenador host durante las pruebas de red.
  >
  > Le cedo la palabra a mis compañeros Francisco y Adrián para concluir la presentación con las conclusiones y recomendaciones de futuro."
* **Transición**: *(Pasa a la diapositiva 15)*

---

### Diapositiva 15 · Conclusiones y Recomendaciones
* **Ponente**: FRAN (Conclusiones) / ADRIÁN (Recomendaciones)
* **Tiempo asignado**: 3.0 minutos (1.5 minutos cada uno)
* **Qué mostrar en pantalla**: La comparativa a dos columnas con las Conclusiones técnicas en el lado izquierdo y las Recomendaciones de futuro a nivel empresarial en el derecho.
* **Guión hablado (FRAN - Conclusiones)**:
  > "Gracias, Iván. Como cierre de nuestro proyecto de fortificación, podemos extraer cuatro conclusiones principales en base al trabajo realizado para TecnoServicios S.L.:
  >
  > - En primer lugar, la **Seguridad es Robusta**: Hemos blindado la entrada remota al servidor eliminando por completo la posibilidad de comprometer el sistema por ataques automatizados de fuerza bruta gracias al uso combinado de TOTP y cambio de puerto.
  > - En segundo lugar, el **Coste es Cero en Licencias**: Validamos la eficiencia del modelo open-source en el entorno corporativo, demostrando que es viable proteger una red sin incurrir en costes de software propietario.
  > - En tercer lugar, la **Protección es Activa**: Fail2Ban y UFW cooperan eficientemente para detectar y aislar dinámicamente direcciones IP sospechosas antes de que puedan saturar el servidor.
  > - Y en cuarto lugar, la **Metodología y Replicabilidad**: La metodología ágil SCRUM nos permitió gestionar con éxito los sprints del proyecto y generar una documentación ordenada y reproducible paso a paso."
* **Guión hablado (ADRIÁN - Recomendaciones)**:
  > "De cara al crecimiento futuro de la empresa TecnoServicios, recomendamos a la dirección tres vías de mejora tecnológica:
  >
  > 1. **Automatización mediante Ansible**: A medida que la empresa incorpore más servidores Linux a su infraestructura, sugerimos utilizar Ansible (Infraestructura como Código) para automatizar la configuración del hardening del SSH y el despliegue de las jaulas de Fail2Ban de manera idéntica y en pocos segundos.
  > 2. **Monitorización Gráfica**: Integrar herramientas de visualización de métricas y eventos en tiempo real, como Prometheus y Grafana, para mapear geográficamente la procedencia de los intentos de acceso y los baneos activos en paneles de control visuales.
  > 3. **Gestión de Identidades Centralizada**: Implementar un sistema de directorio LDAP (como OpenLDAP) cuando el número de técnicos aumente, permitiendo dar de alta o baja credenciales de acceso SSH y tokens MFA de forma centralizada en toda la organización en lugar de gestionarlos usuario por usuario en cada servidor local.
  >
  > Por último, Adrián presentará las fuentes que sustentan este proyecto."
* **Transición**: *(Pasa a la diapositiva 16)*

---

### Diapositiva 16 · Fuentes de Información
* **Ponente**: ADRIÁN
* **Tiempo asignado**: 1.5 minutos
* **Qué mostrar en pantalla**: Las tablas con las categorías de protocolos, seguridad e infraestructura, incluyendo manuales de OpenSSH, GitHub, RFC de la IETF y documentación de Ubuntu y Chrony.
* **Guión hablado**:
  > "Para finalizar, queremos mostrar el marco documental e informativo que ha fundamentado las decisiones técnicas de este proyecto.
  >
  > Hemos consultado el Manual Oficial de OpenSSH y los repositorios de código libre en GitHub para la implementación del módulo de PAM de Google Authenticator. En cuanto a la estandarización y buenas prácticas internacionales, nos basamos en los estándares de la IETF: el RFC 6238 para la generación del token TOTP, el RFC 4251 para la arquitectura de SSH y el RFC 5905 para la sincronización horaria NTP mediante Chrony.
  >
  > Adicionalmente, recurrimos a la documentación oficial de sistemas de Ubuntu Server 24.04 LTS y de la comunidad de UFW. Queremos destacar que nos apoyamos en asistentes de inteligencia artificial como Gemini para optimizar las líneas de comandos y pulir la redacción técnica de nuestra documentación. Toda esta documentación y los códigos fuente de la presentación se encuentran a su entera disposición en nuestro repositorio público de GitHub."
* **Transición**: *(Pasa a la diapositiva 17)*

---

### Diapositiva 17 · Turno de Preguntas
* **Ponente**: TODOS
* **Tiempo asignado**: ~5-7 minutos (Turno de preguntas del tribunal)
* **Qué mostrar en pantalla**: El logo de TecnoServicios, el texto "¿Preguntas?" y los datos del proyecto.
* **Guión hablado**:
  > **ADRIÁN**: "Con esto damos por concluida la exposición de nuestro proyecto intermodular de fortificación de acceso SSH. Queremos agradecer enormemente al tribunal y al público asistente su atención. Quedamos a su entera disposición para responder a cualquier pregunta o aclaración que deseen realizar sobre el proyecto, las tecnologías implantadas o nuestra metodología de trabajo. Muchas gracias."

---

## 🛡️ Preparación de Preguntas del Tribunal (Q&A)

A continuación, se presentan las preguntas más comunes que un tribunal de SMR suele hacer en este tipo de proyectos y quién es el más adecuado para responder según la parte que expuso:

### ❓ Pregunta 1: ¿Por qué habéis elegido el puerto 52222 y no otro? ¿Tiene alguna ventaja técnica?
* **Quién responde**: **IVÁN** (Capa de Evasión)
* **Respuesta preparada**:
  > *"Elegimos el puerto 52222 por dos razones principales. Primero, porque está muy por encima del rango de puertos conocidos o reservados (que van del 0 al 1023) y de los puertos comúnmente escaneados. El puerto estándar de SSH es el 22. Las botnets realizan escaneos masivos en el puerto 22 o en puertos alternativos típicos como el 2222.
  > Segundo, según los estándares de la IANA, los puertos del rango del 49152 al 65535 son puertos dinámicos o privados que no están asignados a ningún servicio por defecto. El puerto 52222 entra dentro de esta categoría, lo que nos asegura que no entrará en conflicto con ningún otro servicio del sistema y nos da la capa de evasión ideal."*

### ❓ Pregunta 2: ¿Qué ocurre si el servidor se desincroniza de la hora de internet? ¿Podríamos quedarnos fuera de la máquina?
* **Quién responde**: **FRAN** (Stack Tecnológico / Chrony)
* **Respuesta preparada**:
  > *"Sí, si el servidor experimenta una deriva horaria significativa, los códigos de doble factor generados por el móvil no coincidirán con los calculados por el servidor, ya que el algoritmo TOTP del estándar RFC 6238 depende estrictamente del tiempo transcurrido (en bloques de 30 segundos) desde una época fija.
  > Para evitar este riesgo crítico, implementamos Chrony (NTP), que mantiene el reloj del sistema permanentemente sincronizado con servidores de hora en internet con una precisión milimétrica. Además, durante la instalación de Google Authenticator en el servidor, el módulo te permite habilitar una ventana de tolerancia horaria (usualmente de +/- 1 paso, que equivale a +/- 30 segundos) para mitigar pequeñas desviaciones horarias de los móviles de los técnicos."*

### ❓ Pregunta 3: Si un usuario pierde su móvil con el token MFA, ¿cómo lo recuperamos sin formatear el servidor?
* **Quién responde**: **ADRIÁN** (Módulos PAM / Configuración inicial)
* **Respuesta preparada**:
  > *"No es necesario formatear el servidor ni reiniciar ningún servicio. Cuando ejecutamos la configuración inicial de Google Authenticator en el servidor para un usuario, el programa genera en la terminal cinco códigos de recuperación de emergencia únicos de 8 dígitos. Estos códigos deben guardarse en un lugar seguro y offline (como una caja fuerte de contraseñas de la empresa).
  > Si el técnico pierde su móvil, puede iniciar sesión en SSH introduciendo su contraseña y, en el campo del código de verificación, utilizar uno de estos códigos de emergencia (que son de un solo uso). Una vez dentro del servidor, el administrador simplemente accede al fichero de configuración `~/.google_authenticator` del usuario afectado en su directorio personal para ver el archivo o, más sencillo aún, puede volver a ejecutar el comando `google-authenticator` para sobrescribir la configuración y generar un nuevo código QR y un nuevo conjunto de claves para el técnico."*

### ❓ Pregunta 4: ¿Qué pasa si el servicio de Fail2Ban se detiene o se cae? ¿El servidor queda desprotegido?
* **Quién responde**: **FRAN** (Fail2Ban)
* **Respuesta preparada**:
  > *"Si el servicio Fail2Ban se detiene, el servidor seguirá funcionando con normalidad y el cortafuegos UFW mantendrá activas las reglas estáticas (como permitir el tráfico solo en el puerto 52222). Sin embargo, perderíamos la protección dinámica. Es decir, las IPs que ya estaban bloqueadas en el cortafuegos antes de la caída seguirán bloqueadas, pero si un atacante empieza a realizar ataques de fuerza bruta en ese momento, el sistema no añadirá nuevas reglas de baneo automático.
  > Para mitigar este riesgo, en un entorno de producción configuraríamos systemd para que reinicie automáticamente el servicio de Fail2Ban si este falla, y añadiríamos alarmas de monitorización que notifiquen al equipo técnico si el demonio se detiene."*

### ❓ Pregunta 5: ¿Por qué no habéis usado llaves SSH (claves públicas/privadas) en lugar de contraseñas si las llaves son más seguras?
* **Quién responde**: **IVÁN** o **ADRIÁN**
* **Respuesta preparada**:
  > *"Evaluamos el uso de claves SSH y de hecho es una de las soluciones más seguras. Sin embargo, no cumplía plenamente con el requisito operativo impuesto por la dirección de TecnoServicios de mantener la máxima simplicidad en el flujo de trabajo diario y facilidad de gestión para un equipo de red pequeño con rotación de personal. 
  > Gestionar, desplegar y revocar claves públicas en los servidores para cada técnico requiere herramientas de gestión centralizada o una administración muy rigurosa para evitar dejar llaves huérfanas de antiguos técnicos. Al implementar el doble factor basado en TOTP sobre contraseñas tradicionales, conseguimos una seguridad equivalente a la de las llaves (ya que introducimos un segundo factor de autenticación robusto) pero manteniendo un flujo de administración y onboarding de técnicos extremadamente sencillo y rápido mediante el escaneo de un código QR."*
