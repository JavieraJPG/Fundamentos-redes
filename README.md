# 🧩 1. ¿Qué es cada dispositivo?

| Concepto | Definición simple | Nivel técnico (breve) | Ejemplo real |
|----------|------------------|----------------------|--------------|
| **Router** | Conecta tu red con otras redes (como internet) y decide por dónde enviar los datos. | Capa 3 (red). Usa IP, tabla de rutas y NAT para enrutar paquetes entre redes. | Router de tu casa o gateway en AWS (Internet Gateway / NAT Gateway) |
| **Switch** | Conecta dispositivos dentro de la misma red y envía datos solo al destino correcto. | Capa 2. Usa MAC y una tabla CAM para conmutar tramas por puerto. | Switch en oficina/data center conectando servidores |
| **Hub** | Conecta dispositivos pero envía todo a todos sin filtrar. | Capa 1. Repite señales; broadcast constante; sin MAC/IP awareness. | Equipos antiguos/laboratorio (obsoleto en producción) |

---

# ⚙️ 2. ¿Cómo funciona realmente?

## 🔹 ¿Qué hace el router con los paquetes?
- Lee la IP de destino del paquete.  
- Consulta su tabla de rutas para elegir el siguiente salto (next hop).  
- Si sale a internet, aplica NAT (IP privada → pública).  
- Reenvía el paquete hacia la red correcta.  

## 🔹 ¿Cómo decide un switch a dónde enviar?
- Aprende qué MAC está en cada puerto (tabla MAC/CAM).  
- Al recibir una trama:  
  - Si conoce la MAC → envía solo a ese puerto (unicast).  
  - Si no la conoce → hace broadcast y aprende.  

## 🔹 ¿Por qué el hub es obsoleto?
- No usa MAC ni IP; todo es broadcast.  
- Genera colisiones y baja eficiencia.  
- No hay segmentación ni seguridad.  
- Fue reemplazado por switches.  

---

# 💻 3. Conexión directa con desarrollo (CLAVE)

## 🔹 Rol del router en una petición a API
```js
fetch("https://api.miapp.com")
```
-Tu app resuelve DNS → obtiene una IP.
-El router envía el paquete hacia internet (ruta por defecto).
-Aplica NAT para usar una IP pública.
-Recibe la respuesta y la devuelve al cliente.
##🔹 ¿Por qué el switch es importante en backend/data center?
Conecta servidores (app, DB, cache) con baja latencia.
Evita tráfico innecesario (usa MAC).
Permite VLANs para separar entornos (prod, staging).
##🔹 Problemas de red que afectan tu app
DNS mal configurado → no resuelve la API.
Rutas/NAT incorrectas → paquetes no salen o no regresan.
Congestión o pérdida → timeouts.
VLAN mal configurada → servicios aislados.
Firewall/Security Groups bloqueando puertos.
🌍 4. Caso práctico real

“Tu aplicación está en producción, pero los usuarios no pueden acceder.”

🔹 ¿Podría ser problema de router?

Sí:

Ruta por defecto caída
NAT fallando
Gateway incorrecto
🔹 ¿Podría ser problema de switch?

Sí:

VLAN mal configurada
Puerto incorrecto
Tabla MAC inconsistente
🔹 ¿Cómo distinguir red vs código?
ping, traceroute, curl
Logs de la app → ¿llegan requests?
Health checks → ¿instancias sanas?

Regla práctica:

No llega tráfico → problema de red
Llega tráfico pero falla → problema de backend
🧪 5. Analogía obligatoria
Router = oficina de correos entre ciudades (usa direcciones IP)
Switch = recepcionista que entrega a la persona exacta (usa MAC)
Hub = alguien que grita el mensaje a todos
🎤 Presentación (resumen sugerido)
🔹 Diferencia clave
Router → IP, entre redes
Switch → MAC, misma red
Hub → broadcast sin control
🔹 Ejemplo en desarrollo

fetch → DNS → Router → Internet → API → respuesta

🔹 Analogía

Correos / recepcionista / grito
