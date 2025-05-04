# Análisis de ransomware: RYUK

## Origen

Ryuk es un malware que pertenece a la categoría de los ransomware. En la mayoría de los casos este tipo de malware tienen por objetivo cifrar los archivos de una máquina víctima para dejarlos inaccesibles y luego extorsionar a la misma pidiéndole un pago, en criptomonedas, por la recuperación de sus archivos.

Ryuk está basado en otro ransomware llamado Hermes (Lazarus group):

<https://malpedia.caad.fkie.fraunhofer.de/details/win.hermes>

<https://web.archive.org/web/20200922165625/https://dcso.de/2019/03/18/enterprise-malware-as-a-service/>

<https://baesystemsai.blogspot.com/2017/10/taiwan-heist-lazarus-tools.html>

El ransomware Ryuk es más lucrativo que su predecesor. Se dirige a grandes organizaciones y agencias gubernamentales que terminan pagando grandes cantidades. La verdad es que, sin los grandes beneficios, procesar los ataques de Ryuk no es sostenible. Implica un alto grado de procesos manuales (explotación directa, solicitudes de pago manejadas por correo electrónico, etc.) y los atacantes no quieren perder el tiempo si el ROI no es bueno.

En un principio se sospechó que su operador fuese un solo grupo vinculado a la inteligencia de Corea del Norte, dados los antecedentes de Hermes, aunque parece que los piratas informáticos detrás del amenazante ransomware Ryuk en realidad se extienden a través de dos o más organizaciones cibercriminales prolíficas, que parecen provenir de Rusia o antiguos estados satélites. Se han encontrado puebas que su principal operador es el grupo <a href="https://apt.etda.or.th/cgi-bin/showcard.cgi?g=UNC1878&n=1" target="_blank"> UNC1878</a>, asociado a la organización <a href="https://attack.mitre.org/groups/G0102/" target="_blank">Wizard Spider</a>. Al principio Ryuk compartió mucho código con Hermes, pero con el tiempo Wizard Spider modificó mucho más a Ryuk. 




## Funcionamiento

- El ransomware Ryuk no es el comienzo, sino el final de un ciclo de infección. Ryuk depende de todo un sistema de malware empleado para su entrada y propagación.
Borrar:<https://protecciondatos-lopd.com/empresas/ryuk-ransomware/>
- Algunas de las informaciones de este análisis pertenencen a las conclusiones del TFM de Ciberseguridad de la UNED: <a href="https://e-spacio.uned.es/entities/publication/2512f1be-f2c6-4345-9023-825dc1f273eb" target="_blank"> 'Análisis del ransomware Ryuk</a>, impacto en los sistemas afectados y técnicas API Hooking y de aprendizaje automático como medidas de mitigación ante el ransomware', realizado por Héctor Santamaría Claro
- Vídeo: Funcionamiento de Ryuk
<https://www.youtube.com/watch?v=PZqM8pwrLdQ&t=1368s>

### Vectores de ataque

La mayoría de los ataques de Ryuk Ransomware se pueden rastrear hasta el acceso a Protocolo de Escritorio Remoto o al phishing por correo electrónico como <a href="https://www.coveware.com/ryuk-ransomware" target="_blank">vectores de ataque</a>

Respecto al phishing, como ocurre con muchos ataques de malware, el método de envío es malspam. Los correos electrónicos suelen enviarse desde una dirección falsa, por lo que el nombre del remitente no levanta sospechas. Cada campaña es única y está diseñada a medida para cada víctima (ya sea una organización, un sector o un individuo):
<img src="./assets/malspam_Ryuk.png" />

### Entrada: Emotet

Presentación del malware Emotet, considerado como uno de los peores malwares y una parte importante del ecosistema cibercriminal.
<a href="./assets/emotet-the-enduring-and-persistent-threat-to-the-hph-tlpclear.pdf" target="_blank">Presentación Emotet</a>
Según varias informaciones, la botnet de EMOTET fue supuestamente desmantelada en enero de 2021
<https://blog.elhacker.net/2021/01/europol-desmantela-emotet-la-botnet-de-malware-mas-grande-del-mundo-troyano-bancario.html>
Es por ese y otros motivos que el que parece ser el sucesor de RYUK, Conti, utiliza otros vectores de ataque y malware de apoyo para su cometido
<a href="https://www.logpoint.com/en/blog/detecting-conti-ransomware-the-successor-of-infamous-ryuk/" target="_blank">Conti, el sucesor deRyuk</a>

### Propagación: TrickBot y Bazaarloader
Borrar:<https://protecciondatos-lopd.com/empresas/trickbot/>
Dado que TrickBot es un troyano bancario, es probable que haya recopilado y exfiltrado información de cuentas financieras en los sistemas infectados antes de instalar la infección con el ransomware Ryuk.


### Proceso Infección y Cifrado
Una vez en un sistema, Ryuk roba credenciales para propagarse por la red mediante PsExec, WMI, PowerShell o directivas de grupo, intentando infectar el mayor número posible de endpoints y servidores, evadiendo los controles de endpoints.

Ryuk escanea los sistemas infectados y cifra casi todos los archivos, directorios, recursos de la red, unidades, etc... atacando específicamente las copias de seguridad. La recuperación del sistema se inhibe mediante la destrucción de copias de instantáneas de volumen (VSS) a través de vssadmin. 


Ryuk utiliza un modelo de cifrado de tres niveles. El primer nivel (base) es el cifrado asimétrico con el par de claves RSA que poseen los atacantes. La clave privada de este par de claves no está disponible para la víctima hasta que se adquiere un descifrador. El segundo nivel es un par de claves RSA por víctima. La mayoría de los ransomware generan este par de claves durante el proceso de cifrado y cifran la clave privada resultante utilizando la clave global de nivel superior. Con Ryuk, el ransomware llega con el par de claves preinstalado y la clave privada precifrada. El tercer nivel es una clave de cifrado simétrico AES estándar generada para cada archivo de la víctima mediante la función CryptGenKey de la API de Win32. Esta clave se exporta mediante CryptExportKey, se cifra con la clave de segundo nivel y el resultado se adjunta al archivo cifrado.

El ransomware Ryuk suele añadir la extensión estándar ".ryk" a los archivos cifrados. Existe una variante que no añade ninguna extensión especial a los archivos, pero utiliza el mismo cifrado. Un archivo cifrado seguiría el siguiente patrón (ejemplo de un documento de Word):
filename.doc.ryk


## Algunos ataques conocidos

Los objetivos de Ryuk suelen ser organizaciones de alto perfil, donde los atacantes saben que probablemente recibirán el pago de sus cuantiosos rescates, generalmente del sector sanitario. Entre las víctimas se incluyen EMCOR, hospitales UHS y varios periódicos. Se estima que, al atacar a estas organizaciones, Ryuk generó ingresos de 61 millones de dólares para sus operadores solamente entre el período entre febrero de 2018 y octubre de 2019.

El <a href="https://www.bleepingcomputer.com/news/security/uhs-hospitals-hit-by-reported-country-wide-ryuk-ransomware-attack" target="_blank">ataque al Universal Health Services</a> en Estados Unidos en SEPT 2020 fue, según explican algunos medios, el ciberataque al sector de la salud más grande en la historia de aquel país. UHS vio bloqueada su infraestructura informática y no se pudo acceder al sistema telefónico de la organización ni a los historiales médicos de los pacientes. UHS tardó aproximadamente tres semanas en restaurar sus sistemas y estimó pérdidas de 67 millones de dólares debido al ataque.

Además de los hospitales de UHS, <a href="https://www.nytimes.com/2020/11/26/us/hospital-cyber-attack.html" target="_blank"> otros hospitales estadounidenses </a> fueron víctimas de ataques de ransomware Ryuk en 2020. Los ataques cifraron datos críticos, interrumpiendo tratamientos y retrasando procedimientos para muchos pacientes.

Ataque a Tribune Publishing: En 2018, Ryuk se propagó a varios periódicos de Estados Unidos a través del <a href="https://www.nytimes.com/2018/12/30/business/media/los-angeles-times-cyberattack.html" target="blank"> software infectado de Tribune Publishing</a>. Los ataques interrumpieron la impresión de periódicos durante varios días.

En España:
- <a href="https://www.itdigitalsecurity.es/actualidad/2019/11/prosegur-la-ultima-victima-del-ransomware-ryuk" target="_blank"> Ataque a Prosegur </a>(NOV/2019).
- Uno de los <a href="https://protecciondatos-lopd.com/empresas/ryuk-ransomware/" target="_blank">ataques más conocidos</a> y que generó un gran impacto fue el que realizó sobre Servicio Público de Empleo Estatal (SEPE) en España.


## Rescates y técnicas de negociación empleadas

Normalmente la nota de rescate se deja en un archivo nombrado como <a href="https://www.ransomlook.io/notes/ryuk" target="_blank">RyukReadMe.txt</a>

<img src="./assets/ryuk_readme.png" />

En la nota de rescate se incluye una wallet para el ingreso de alguna criptomoneda y una dirección de servicios de mail cifrados como ProtonMail o Tutanota, para el contacto con el/los secuestradores y obtener la hipotética clave de descifrado.

Obviamente no hay que pagar nunca un rescate ya que:
- Pagar no te garantiza que volverás a tener acceso a los datos, recuerda que se trata de delincuentes.
- Si pagas es posible que seas objeto de ataques posteriores pues, ya saben que estás dispuesto a pagar.
- Puede que te soliciten una cifra mayor una vez hayas pagado.
- Pagar fomenta el negocio de los ciberdelincuentes.

Aun así muchas víctimas (sobretodo empresas pequeñas y medianas) pagan  rescates para recuperar sus datos:
<https://www.darkreading.com/cyberattacks-data-breaches/how-to-negotiate-with-ransomware-attackers>

Casi cada aparición del malware utilizó una billetera única. Poco después de que se realizara el pago del rescate, los fondos se dividieron para después transferirse a través de muchas otras cuentas.
<https://www.itdigitalsecurity.es/vulnerabilidades/2018/08/ryuk-el-ransomware-sucesor-de-hermes-que-ya-ha-recaudado-mas-de-600000-dolares>


