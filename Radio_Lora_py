# Lora node Radio Link Test

import time
from LoRaRF import SX127x
import struct

# Configuración del Radio
# Bus SPI 0, Device 0, Reset en GPIO 17, Interrupción en GPIO 25
LoRa = SX127x()

def setup_lora():
    print("Iniciando Módulo LoRa...")
    if not LoRa.begin():
        print("Fallo al detectar el módulo. Revisa las conexiones.")
        return False
    
    # Configuración para México (915MHz)
    LoRa.setFrequency(915000000)
    LoRa.setTxPower(17) # 17dBm
    LoRa.setSpreadingFactor(7) # SF7 para mayor velocidad en ráfagas
    LoRa.setBandwidth(125000)
    LoRa.setSyncWord(0x12) # Debe coincidir con el módulo Miuva
    return True

def enviar_rafaga(datos):
    """Envía una lista de datos como una ráfaga única."""
    print(f"Enviando ráfaga: {datos}")
    
    # Empaquetamos datos (ejemplo: 3 floats)
    payload = struct.pack('fff', *datos)
    
    LoRa.beginPacket()
    LoRa.write(list(payload), len(payload))
    status = LoRa.endPacket()
    LoRa.wait() # Espera a que termine la transmisión
    return status

def loop_principal():
    if not setup_lora(): return

    while True:
        # 1. Simulación de ráfaga de datos (Temp, Hum, Pres)
        sensor_data = [25.5, 60.2, 1013.1] 
        enviar_rafaga(sensor_data)
        
        # 2. Modo Escucha (Ventana de actualización)
        print("Esperando actualización del Miuva...")
        LoRa.request()
        LoRa.wait()
        
        if LoRa.available():
            # Si recibimos algo, lo procesamos como configuración
            msg = ""
            while LoRa.available():
                msg += chr(LoRa.read())
            print(f"¡Actualización recibida!: {msg}")
            
        time.sleep(5) # Intervalo entre ráfagas

if __name__ == "__main__":
    try:
        loop_principal()
    except KeyboardInterrupt:
        print("\nSistema detenido.")
