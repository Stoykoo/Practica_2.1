## Practica_2.1
# 2.1.1 Practica De inicio es la básico de Desplegar algo en pantalla, algunos quieren el logo de ISC, esta bien, otro texto simple, se agradece,  en el OLED DIsplay
<div style="text-align: justify;">
"La práctica 2.1.1, llamada 'Práctica de Inicio en el OLED Display', se trata de aprender los conceptos fundamentales para mostrar contenido en una pantalla OLED. En esta práctica, estoy aprendiendo a mostrar elementos simples en la pantalla, como el logo de ISC (posiblemente una entidad o institución), o cualquier otro texto básico que desee incluir. Mi objetivo principal es familiarizarme con el funcionamiento y las capacidades de un OLED Display, lo cual es esencial para desarrollar proyectos más avanzados que involucren la visualización de información en pantallas. Esta práctica marca el inicio de mi exploración de las posibilidades que ofrece esta tecnología en el contexto de la informática y la electrónica."
</div>

Código que utilice en la práctica: 

	"""
	MARTINEZ GARCIA GABRIEL STOYKO - 20211808
	"""

	# Importar las bibliotecas necesarias
	from machine import Pin, I2C  # Importar las clases Pin e I2C desde la biblioteca machine.
	from ssd1306 import SSD1306_I2C  # Importar la clase SSD1306_I2C desde la biblioteca ssd1306.
	import framebuf, sys  # Importar la clase framebuf y el módulo sys.
	import utime  # Importar el módulo utime.

	# Definir la resolución de la pantalla OLED
	pix_res_x = 128  # Definir la resolución horizontal en píxeles (128 píxeles).
	pix_res_y = 64   # Definir la resolución vertical en píxeles (64 píxeles).

	def init_i2c(scl_pin, sda_pin):
    """
    Inicializa un dispositivo I2C y escanea las direcciones disponibles.

    Args:
        scl_pin (int): Pin para el reloj SCL del bus I2C.
        sda_pin (int): Pin para la línea de datos SDA del bus I2C.

    Returns:
        I2C: Objeto I2C configurado.
    """
    # Inicializar un objeto I2C en el bus I2C 1 con los pines SCL y SDA especificados.
    i2c_dev = I2C(1, scl=Pin(scl_pin), sda=Pin(sda_pin), freq=400000)
    
    # Escanear las direcciones I2C disponibles y almacenarlas en i2c_addr.
    i2c_addr = [hex(ii) for ii in i2c_dev.scan()]
    
    if not i2c_addr:
        print('No I2C Display Found')
        sys.exit()
    else:
        print("I2C Address      : {}".format(i2c_addr[0]))
        print("I2C Configuration: {}".format(i2c_dev))
    
    return i2c_dev

	def display_logo(oled):
    """
    Muestra el logotipo de Raspberry Pi en la pantalla OLED.

    Args:
        oled: Objeto SSD1306_I2C que representa la pantalla OLED.
    """
    # Definir la representación de bytes (plain_bytes) del logotipo de Raspberry Pi.
    plain_bytes = [
        0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xc7, 0xfc, 0x7f, 0xff, 0xff, 
	0xff, 0xff, 0xfe, 0x07, 0xf8, 0x1f, 0xff, 0xff, 0xff, 0xff, 0xfe, 0x07, 0xf8, 0x1f, 0xff, 0xff, 
	0xff, 0xff, 0xfe, 0x03, 0xf8, 0x1f, 0xff, 0xff, 0xff, 0xff, 0xf6, 0x03, 0xf0, 0x1f, 0xff, 0xff, 
	0xff, 0xff, 0xd6, 0x03, 0xf0, 0x1e, 0xff, 0xff, 0xff, 0xff, 0xff, 0x00, 0x00, 0x3f, 0xe7, 0xff, 
	0xff, 0xf8, 0xff, 0x00, 0x00, 0x1f, 0xc3, 0xff, 0xff, 0xf0, 0x7c, 0x00, 0x00, 0x07, 0x81, 0xff, 
	0xff, 0xe0, 0x30, 0x00, 0x02, 0x02, 0x00, 0xff, 0xff, 0xe0, 0x00, 0x00, 0x02, 0x00, 0x01, 0xff, 
	0xff, 0xf0, 0x00, 0x80, 0x00, 0x00, 0x03, 0xff, 0xff, 0xf0, 0x00, 0x00, 0x40, 0x00, 0x07, 0xff, 
	0xff, 0xf8, 0x00, 0x0f, 0xfe, 0x00, 0x0f, 0xff, 0xff, 0xfc, 0x00, 0x7e, 0x0f, 0xc0, 0x0b, 0x7f, 
	0xff, 0xfc, 0x00, 0xf0, 0x01, 0xe0, 0x07, 0xff, 0xff, 0xfc, 0x01, 0xc0, 0x00, 0x70, 0x05, 0xff, 
	0xff, 0xf8, 0x03, 0x82, 0x48, 0x38, 0x03, 0x7f, 0xff, 0xf8, 0x07, 0x02, 0x48, 0x1c, 0x01, 0x87, 
	0xf8, 0x10, 0x0e, 0x00, 0x00, 0x0e, 0x00, 0x07, 0xf8, 0x00, 0x1c, 0x07, 0xfc, 0x07, 0x00, 0x03, 
	0xf8, 0x00, 0x1c, 0x0f, 0xfe, 0x03, 0x00, 0x03, 0xf8, 0x00, 0x38, 0x7f, 0xff, 0xc3, 0x80, 0x03, 
	0xf8, 0x00, 0x38, 0x0f, 0xfe, 0x03, 0x80, 0x03, 0xfc, 0x00, 0x30, 0x0f, 0xfe, 0x01, 0x80, 0x3f, 
	0xff, 0x80, 0x70, 0x2f, 0xfe, 0xc1, 0x80, 0xf7, 0xff, 0xc0, 0x70, 0x0f, 0xfe, 0x01, 0xc0, 0xf9, 
	0xff, 0xc0, 0x70, 0x2f, 0xfe, 0xc1, 0xc0, 0x7f, 0xff, 0xc2, 0x70, 0x0f, 0xfe, 0x01, 0xc0, 0xe7, 
	0xff, 0x40, 0x70, 0x0f, 0xfe, 0x01, 0xc0, 0x7b, 0xff, 0xe0, 0x70, 0x7f, 0xff, 0xc1, 0x80, 0x4f, 
	0xff, 0xc0, 0x38, 0x0f, 0xfe, 0x01, 0x80, 0x1f, 0xfe, 0x00, 0x38, 0x0f, 0xfe, 0x03, 0x80, 0x03, 
	0xf8, 0x00, 0x38, 0x7f, 0xfe, 0xc3, 0x80, 0x03, 0xf8, 0x00, 0x1c, 0x07, 0xfe, 0x07, 0x00, 0x03, 
	0xf8, 0x00, 0x1c, 0x07, 0xfc, 0x07, 0x00, 0x03, 0xf8, 0x00, 0x0e, 0x00, 0x00, 0x0e, 0x00, 0x07, 
	0xfc, 0x00, 0x0f, 0x02, 0x48, 0x1c, 0x03, 0xc7, 0xfc, 0xf8, 0x07, 0x82, 0x48, 0x3c, 0x00, 0xff, 
	0xff, 0xf8, 0x03, 0xe0, 0x00, 0xf8, 0x04, 0xdf, 0xff, 0xfc, 0x01, 0xf8, 0x01, 0xe0, 0x07, 0xbf, 
	0xff, 0xfc, 0x00, 0x7f, 0xff, 0xc0, 0x03, 0x7e, 0xff, 0xfe, 0x00, 0x1f, 0xff, 0x00, 0x06, 0xfc, 
	0xff, 0xfc, 0x00, 0x03, 0xf8, 0x00, 0x03, 0xfd, 0xff, 0xf8, 0x00, 0x00, 0x00, 0x00, 0x01, 0xf9, 
	0xff, 0xf0, 0x00, 0x05, 0xdc, 0x00, 0x00, 0xf9, 0xff, 0xe0, 0x00, 0x05, 0xd0, 0x01, 0x00, 0xf9, 
	0xff, 0xf0, 0x18, 0x05, 0x74, 0x07, 0x81, 0xf9, 0xff, 0xf8, 0x7e, 0x05, 0xdc, 0x0f, 0xc3, 0xfd, 
	0xff, 0xfc, 0xff, 0x80, 0x00, 0x1f, 0xe7, 0xfc, 0xff, 0xff, 0xff, 0x00, 0x00, 0x1f, 0xff, 0xfe, 
	0xff, 0xff, 0xfb, 0x01, 0xf8, 0x1f, 0xff, 0xff, 0xff, 0xff, 0xff, 0x03, 0xf8, 0x0f, 0xff, 0xff, 
	0xff, 0xff, 0xff, 0x03, 0xf8, 0x0f, 0xff, 0xff, 0xff, 0xff, 0xfe, 0x02, 0x1c, 0x0f, 0xff, 0xff, 
	0xff, 0xff, 0xff, 0x06, 0xfc, 0x3f, 0xff, 0xff, 0xff, 0xff, 0xff, 0xf7, 0xff, 0xff, 0xff, 0xff, 
	0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 
	0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 
	0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff
    ]
    
    # Crear un objeto FrameBuffer a partir de la lista de bytes.
    fb = framebuf.FrameBuffer(bytearray(plain_bytes), 64, 64, framebuf.MONO_HLSB)
    
    # Rellenar la pantalla OLED con píxeles apagados (valor 0), colocar el logotipo en la posición (40, 0) y mostrarlo.
    oled.fill(0)
    oled.blit(fb, 40, 0)
    oled.show()

	def main():
    # Inicializar la conexión I2C y la pantalla OLED
    i2c_dev = init_i2c(scl_pin=27, sda_pin=26)
    oled = SSD1306_I2C(pix_res_x, pix_res_y, i2c_dev)
    
    # Mostrar el logotipo en la pantalla OLED
    display_logo(oled)

	if __name__ == '__main__':
    main()



![](imagenes/1.jpg)

# 2.1.2  Desplegar la hora de Internet en la Pico usando su Wifi integrada para que interrogue un servidor NTP Time Server, en el OLED DIsplay
<div style="text-align: justify;">
  En la práctica 2.1.2, estoy emocionado por aprender cómo desplegar la hora de Internet en mi Pico utilizando la función WiFi integrada. En este caso, mi objetivo es que mi Pico se comunique con un servidor NTP Time Server para obtener la hora actual y luego mostrarla en el OLED Display. Esta práctica es un paso adelante en la exploración de las capacidades de mi dispositivo, ya que me permitirá acceder a una fuente confiable de tiempo en línea y presentarla de manera efectiva en la pantalla. ¡Vamos a sumergirnos en esta emocionante experiencia tecnológica!
</div>

Código que utilice en la práctica: 


	"""
	MARTINEZ GARCIA GABRIEL STOYKO - 20211808
	"""
	# Importación de módulos necesarios
	from machine import Pin, I2C  # Importa los módulos para controlar la placa y la comunicación I2C.
	from ssd1306 import SSD1306_I2C  # Importa el módulo para controlar la pantalla OLED.
	import utime  # Importa el módulo para gestionar el tiempo.
	import ntptime  # Importa el módulo para sincronizar la hora a través de NTP.

	# Configuración de la pantalla OLED y pines I2C
	pix_res_x = 128  # Resolución en el eje X de la pantalla OLED.
	pix_res_y = 64   # Resolución en el eje Y de la pantalla OLED.
	scl_pin = 27      # Pin de reloj (SCL) para la comunicación I2C.
	sda_pin = 26      # Pin de datos (SDA) para la comunicación I2C.

	def init_i2c(scl_pin, sda_pin):
    # Inicializa el dispositivo I2C con los pines especificados.
    i2c_dev = I2C(1, scl=Pin(scl_pin), sda=Pin(sda_pin), freq=400000)
    return i2c_dev  # Devuelve la instancia I2C inicializada.

	def update_time():
    try:
        # Sincroniza la hora con un servidor NTP utilizando el servidor predeterminado (time.google.com).
        ntptime.host = 'time.google.com'
        print("Hora actual actualizada desde Internet:", utime.localtime())
    except OSError as e:
        print("Error al sincronizar la hora desde Internet:", e)

	def display_time(oled, time_data):
    oled.fill(0)  # Limpia la pantalla estableciendo todos los píxeles en negro.
    oled.text("Hora actual:", 0, 0)  # Muestra un título en la pantalla.
    oled.text("{:02d}:{:02d}:{:02d}".format(time_data[3], time_data[4], time_data[5]), 0, 16)  # Muestra la hora en formato HH:MM:SS.
    oled.show()  # Actualiza la pantalla para mostrar el texto.

	def main():
    i2c_dev = init_i2c(scl_pin, sda_pin)  # Inicializa la comunicación I2C.
    oled = SSD1306_I2C(pix_res_x, pix_res_y, i2c_dev)  # Inicializa la pantalla OLED.
    
    while True:
        update_time()  # Actualiza la hora desde Internet.
        current_time = utime.localtime()  # Obtiene la hora actual como una tupla.
        
        # Muestra la hora en la pantalla OLED.
        display_time(oled, current_time)
        
        utime.sleep(1)  # Espera 1 segundo antes de actualizar la hora nuevamente.

	if __name__ == '__main__':
    main()  # Llama a la función principal si este es el script principal.

 
![](imagenes/hora.jpg)
