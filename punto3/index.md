# Análisis de ransomware: RYUK

## Origen

Ryuk es un malware que pertenece a la categoría de los ransomware. En la mayoría de los casos este tipo de malware tienen por objetivo cifrar los archivos de una máquina víctima para dejarlos inaccesibles y luego extorsionar a la misma pidiéndole un pago, en criptomonedas, por la recuperación de sus archivos.

Ryuk está basado en otro ransomware llamado Hermes (Lazarus group):

<https://malpedia.caad.fkie.fraunhofer.de/details/win.hermes>

<https://web.archive.org/web/20200922165625/https://dcso.de/2019/03/18/enterprise-malware-as-a-service/>

<https://baesystemsai.blogspot.com/2017/10/taiwan-heist-lazarus-tools.html>

El ransomware Ryuk es más lucrativo que su predecesor. Se dirige a grandes organizaciones y agencias gubernamentales que terminan pagando grandes cantidades. La verdad es que, sin los grandes beneficios, procesar los ataques de Ryuk no es sostenible. Implica un alto grado de procesos manuales (explotación directa, solicitudes de pago manejadas por correo electrónico, etc.) y los atacantes no quieren perder el tiempo si el ROI no es bueno.

Sospechoso de ser un solo grupo vinculado a la inteligencia de Corea del Norte, los piratas informáticos detrás del amenazante ransomware Ryuk en realidad se extienden a través de dos o más organizaciones cibercriminales prolíficas, que parecen provenir de Rusia o antiguos estados satélites.

- Algunas de las informaciones de este análisis pertenencen a las conclusiones del TFM de Ciberseguridad de la UNED: 'Análisis del ransomware Ryuk, impacto en los sistemas afectados y técnicas API Hooking y de aprendizaje automático como medidas de mitigación ante el ransomware', realizado por Héctor Santamaría Claro
<https://e-spacio.uned.es/entities/publication/2512f1be-f2c6-4345-9023-825dc1f273eb>

## Funcionamiento

El ransomware Ryuk no es el comienzo, sino el final de un ciclo de infección.
Borrar:<https://protecciondatos-lopd.com/empresas/ryuk-ransomware/>

### Vídeo: Funcionamiento de Ryuk
<https://www.youtube.com/watch?v=PZqM8pwrLdQ&t=1368s>

### Vectores de ataque

La mayoría de los ataques de Ryuk Ransomware se pueden rastrear hasta el acceso a Protocolo de Escritorio Remoto o al phishing por correo electrónico como vector de ataque.
<https://www.coveware.com/ryuk-ransomware>


### Entrada: Emotet

Presentación del malware Emotet, considerado como uno de los peores malwares y una parte importante del ecosistema cibercriminal.
<a href="./assets/emotet-the-enduring-and-persistent-threat-to-the-hph-tlpclear.pdf" target="_blank">Presentación Emotet</a>
La botnet de EMOTET fue supuestamente desmantelada en enero de 2021
<https://blog.elhacker.net/2021/01/europol-desmantela-emotet-la-botnet-de-malware-mas-grande-del-mundo-troyano-bancario.html>

### Propagación: TrickBot
Borrar:<https://protecciondatos-lopd.com/empresas/trickbot/>

### Cifrado

Ryuk escanea los sistemas infectados y cifra casi todos los archivos, directorios, recursos de la red, unidades, etc. utilizando algoritmos de cifrado RSA y AES irrompibles con tres claves

El ransomware Ryuk suele añadir la extensión estándar ".ryk" a los archivos cifrados. Existe una variante que no añade ninguna extensión especial a los archivos, pero utiliza el mismo cifrado. Un archivo cifrado seguiría el siguiente patrón (ejemplo de un documento de Word):

filename.doc.ryk

Ryuk utiliza un modelo de cifrado de confianza de tres niveles. El primer nivel (base) es el cifrado asimétrico con el par de claves RSA que poseen los atacantes. La clave privada de este par de claves no está disponible para la víctima hasta que se adquiere un descifrador. El segundo nivel es un par de claves RSA por víctima. La mayoría de los ransomware generan este par de claves durante el proceso de cifrado y cifran la clave privada resultante utilizando la clave global de nivel superior. Con Ryuk, el ransomware llega con el par de claves preinstalado y la clave privada precifrada. El tercer nivel es una clave de cifrado simétrico AES estándar generada para cada archivo de la víctima mediante la función CryptGenKey de la API de Win32. Esta clave se exporta mediante CryptExportKey, se cifra con la clave de segundo nivel y el resultado se adjunta al archivo cifrado.

## Algunos ataques conocidos
- Prosegur NOV/2019
<https://www.itdigitalsecurity.es/actualidad/2019/11/prosegur-la-ultima-victima-del-ransomware-ryuk>
- SEPE: Uno de los ataques más conocidos y que generó un gran impacto fue el que realizó sobre Servicio Público de Empleo Estatal (SEPE) en España.


- Por otra parte, el ataque al Universal Health Services en Estados Unidos en SEPT 2020 fue, según explican algunos medios, el ciberataque al sector de la salud más grande en la historia de aquel país:
<https://www.incibe.es/incibe-cert/publicaciones/bitacora-de-seguridad/universal-health-services-victima-del-ransomware-ryuk>
<https://blog.elhacker.net/2020/09/red-hospitales-universal-health-services-uhs-ransomware-ryuk.html>
<https://www.bleepingcomputer.com/news/security/uhs-hospitals-hit-by-reported-country-wide-ryuk-ransomware-attack/>


## Rescates y técnicas de negociación empleadas

Típica nota de rescate, en la que se incluye una wallet para el ingreso de alguna criptomoneda y una dirección de servicios de mail cifrados como ProtonMail o Tutanota, para el contacto con el/los secuestradores y obtener la hipotética clave de descifrado.

Normalmente se deja en un archivo nombrado como RyukReadMe.txt

<https://www.ransomlook.io/notes/ryuk>

No hay que pagar nunca un rescate ya que:

- Pagar no te garantiza que volverás a tener acceso a los datos, recuerda que se trata de delincuentes.
- Si pagas es posible que seas objeto de ataques posteriores pues, ya saben que estás dispuesto a pagar.
- Puede que te soliciten una cifra mayor una vez hayas pagado.
- Pagar fomenta el negocio de los ciberdelincuentes.

Aun así muchas víctimas (sobretodo empresas pequeñas y medianas) pagan  rescates para recuperar sus datos:
<https://www.darkreading.com/cyberattacks-data-breaches/how-to-negotiate-with-ransomware-attackers>

Casi cada aparición del malware utilizó una billetera única. Poco después de que se realizara el pago del rescate, los fondos se dividieron para después transferirse a través de muchas otras cuentas.
<https://www.itdigitalsecurity.es/vulnerabilidades/2018/08/ryuk-el-ransomware-sucesor-de-hermes-que-ya-ha-recaudado-mas-de-600000-dolares>

### El sucesor de RYUK: Conti ransomware
<https://www.logpoint.com/en/blog/detecting-conti-ransomware-the-successor-of-infamous-ryuk/>
