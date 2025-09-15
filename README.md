# Documentación TCP/IP – NetPractice 42

## 1. Conceptos básicos de TCP/IP

- **TCP/IP**: conjunto de protocolos que permite la comunicación entre dispositivos en red.  
- **IP (Internet Protocol)**: asigna direcciones únicas a cada dispositivo.  
- **TCP/UDP**: funcionan encima de IP, gestionando la transmisión de datos.  

---

## 2. Direcciones IP y máscaras de subred

### 2.1 Dirección IPv4
- 32 bits divididos en 4 octetos: `192.168.1.42`  
- Cada máquina debe tener una IP única en su red.  

### 2.2 Máscara de subred
- Define qué parte de la IP es **red** y qué parte es **host**.  
- Ejemplo `/24 = 255.255.255.0` → 24 bits de red, 8 bits de host.  

### 2.3 CIDR vs máscara decimal
- **CIDR**: `/24`, `/27`, `/30` → indica el número de bits de red.  
- **Decimal**: `255.255.255.0`, `255.255.255.224`, `255.255.255.252` → la misma información, diferente notación.  

### 2.4 Direcciones especiales
- **Red**: todos los bits de host a `0` → no se puede usar como host.  
- **Broadcast**: todos los bits de host a `1` → no se puede usar como host.  
- **Hosts válidos**: direcciones intermedias dentro del bloque.  

---

## 3. Comunicación entre máquinas

- Dos máquinas pueden comunicarse directamente si están en la **misma red** (misma combinación IP + máscara).  
- Si están en redes diferentes, necesitan un **router (gateway)**.  

---

## 4. Routers y Switches

### 4.1 Router
- Conecta redes diferentes.  
- Capa 3 (red).  
- Usa direcciones IP.  
- Cada interfaz pertenece a una red distinta.  

Ejemplo:
```
PC1: 192.168.1.10/24 Gateway 192.168.1.1
Router: 192.168.1.1/24 ↔ 10.0.0.1/24
PC2: 10.0.0.20/24 Gateway 10.0.0.1
```

### 4.2 Switch
- Conecta máquinas dentro de la misma red.  
- Capa 2 (enlace de datos).  
- Usa direcciones MAC para enviar paquetes al puerto correcto.  

Ejemplo: PCs conectadas al mismo switch pueden comunicarse si están en la misma subred, pero no si están en redes distintas.  

---

## 5. Máscaras y bloques de subred

### 5.1 Cálculo del bloque
- Tamaño del bloque:
```
Tamaño del bloque = 2^(bits de host)
```
- Dirección de red: primer IP del bloque.  
- Dirección de broadcast: última IP del bloque.  
- Hosts válidos: IPs intermedias.  

### 5.2 Ejemplo /27 (255.255.255.224)
- Bits de host: 5 → bloque = 2⁵ = 32 IPs.  
- Bloques de 32 en 32 en el último octeto: `0–31`, `32–63`, `64–95` …  

Ejemplo:  
- Bloque `0–31`: Red = 0, Broadcast = 31, Hosts = 1–30  
- Bloque `32–63`: Red = 32, Broadcast = 63, Hosts = 33–62  

### 5.3 Ejemplo /30 (255.255.255.252)
- Bits de host: 2 → bloque = 2² = 4 IPs.  
- Solo 2 hosts válidos.  

Ejemplo:  
- Bloque `0–3`: Red = 0, Broadcast = 3, Hosts = 1–2  
- Bloque `4–7`: Red = 4, Broadcast = 7, Hosts = 5–6  

Se usa en **enlaces punto a punto entre routers**.  

---

## 6. Comunicación según bloque

- Dos IPs se ven directamente si están en el mismo bloque de su máscara.  
- Saltos de bloque dependen de la máscara:  

```
/24 → 256
/25 → 128
/26 → 64
/27 → 32
/28 → 16
/30 → 4
```

Ejemplo práctica:  
- `192.168.1.70/27` → bloque 64–95  
- `192.168.1.80/27` → bloque 64–95 ✅ misma red  
- `192.168.1.100/27` → bloque 96–127 ❌ otra red  

---

## 7. Rangos especiales de IP

| Rango         | Uso                      |
|---------------|--------------------------|
| 10.0.0.0/8    | Privada, LAN grande      |
| 172.16.0.0/12 | Privada, LAN mediana     |
| 192.168.0.0/16| Privada, LAN pequeña     |
| 127.0.0.0/8   | Loopback (localhost)     |
| 224.0.0.0/4   | Multicast                |
| 240.0.0.0/4   | Reservado / experimental |

---

## 8. Cómo convertir máscara decimal a CIDR

### 8.1 Regla general
1. Convierte cada octeto a binario.  
2. Cuenta los bits en `1`.  
3. El total = `/n` (CIDR).  

### 8.2 Ejemplos
- `255.255.255.0`  
```
11111111.11111111.11111111.00000000 → 24 unos → /24
```

- `255.255.255.224`  
```
11111111.11111111.11111111.11100000 → 27 unos → /27
```

- `255.255.192.0`  
```
11111111.11111111.11000000.00000000 → 18 unos → /18
```

- `255.255.240.0`  
```
11111111.11111111.11110000.00000000 → 20 unos → /20
```

### 8.3 Tabla rápida (para el último octeto)
- 255 = 8 bits → /8  
- 254 = 7 bits → /7  
- 252 = 6 bits → /6  
- 248 = 5 bits → /5  
- 240 = 4 bits → /4  
- 224 = 3 bits → /3  
- 192 = 2 bits → /2  
- 128 = 1 bit → /1  
- 0 = 0 bits  

---

## 9. Resumen de buenas prácticas NetPractice

- Identificar redes y máscaras.  
- Asignar IP válidas a hosts y routers.  
- Configurar gateways o rutas hacia redes remotas.  
- Verificar conectividad con `ping`.  