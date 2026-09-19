# 🌱 EcoMercadito SV - Green Software Engineering para iOS

El proyecto "EcoMercadito SV" consiste en el diseño, desarrollo y evaluación de una aplicación móvil nativa para iOS, concebida bajo los principios de Green Software Engineering. Su propósito funcional es servir como un tablero digital de transferencia de datos ultraligera mediante una arquitectura multi-rol. Permite a pequeños productores, comedores y mercados locales de El Salvador gestionar y publicar excedentes de alimentos perecederos mediante un perfil de "Comercio". A su vez, ofrece a los ciudadanos un perfil de "Consumidor" para localizar y adquirir dichos productos a precios de descuento antes de su fecha de caducidad[cite: 1].

## 🎯 Objetivos de la Investigación

- **General:** Desarrollar una aplicación móvil nativa para iOS que reduzca el desperdicio de alimentos en comercios locales de El Salvador. Esta implementación busca minimizar la huella de carbono digital, el consumo de batería y el tráfico de datos del dispositivo.
- **Eficiencia de Datos:** Implementar una arquitectura de datos basada en cargas útiles JSON menores a 1 KB. Se elimina la transferencia de imágenes rasterizadas para reducir el consumo energético de la antena LTE/5G del dispositivo[cite: 1].
- **Eficiencia Gráfica:** Diseñar una interfaz gráfica nativa con SwiftUI en modo oscuro puro[cite: 1]. Esto aprovecha la tecnología OLED para minimizar el consumo de energía en la renderización gráfica[cite: 1].
- **Ejecución Local:** Utilizar SwiftData para implementar un enfoque Offline-First[cite: 1]. Esto permite que las consultas de ofertas y los cálculos de impacto ambiental se realicen localmente y reduzcan los ciclos de CPU asociados a la espera de respuestas de servidores externos[cite: 1].

## 🏛️ Arquitectura Sostenible (Green Computing)

Cada decisión de diseño en "EcoMercadito SV" obedece a la reducción estricta de ciclos de CPU, asignación de RAM, uso de GPU y tráfico de red:

1.  **Modelo de Datos Ultraligero (SwiftData):** El modelo central, `OfertaExcedente`, se compone estrictamente de tipos de datos primitivos ligeros (String, Double, Int, Date, Bool, UUID)[cite: 1]. Incluye atributos como `titulo`, `precioDescuento`, `cantidadDisponible` y una llave foránea ligera `negocioId` para relacionar los comercios optimizando el uso de la memoria RAM. Esto garantiza una serialización a JSON ultraligera (payloads < 1 KB), minimizando los ciclos de CPU necesarios para la codificación y decodificación[cite: 1].
2.  **Gestión de Usuarios Multi-rol:** El modelo `Usuario` gestiona las sesiones mediante un enum `RolUsuario` con casos para `consumidor` y `comercio`. Su atributo `rolRaw` persiste de forma nativa para mantener las transferencias de red ultraligera.
3.  **Costo Visual Cero (GPU Optimization):** Se prohíbe el uso de imágenes rasterizadas o videos cargados desde la red. Toda la representación visual se realiza mediante iconografía vectorial nativa del sistema operativo (SF Symbols), la cual pesa escasos bytes y se renderiza matemáticamente mediante la GPU de forma hiper-optimizada.
4.  **UI Dark Mode Puro (#000000):** La interfaz utiliza fondos de color negro absoluto. En los paneles OLED de los dispositivos iOS modernos, el color negro permite que los píxeles se apaguen físicamente, logrando una reducción drástica en el consumo de la batería.
5.  **Networking y Antena Baseband:** El proyecto define una capa de red sobre URLSession que agrupa peticiones (batching) y aplica políticas de caché. Se utiliza un mecanismo de sincronización diferencial mediante cabeceras HTTP ETag y Last-Modified. Esto evita despertar repetidamente la antena de radiofrecuencia (el componente que más energía consume después de la pantalla), solicitando datos únicamente cuando el caché local ha expirado o cuando notificaciones push silenciosas detectan cambios[cite: 1].
6.  **Autenticación de Bajo Impacto:** El sistema adopta el estándar JSON Web Token (JWT) almacenado de forma segura en el Keychain de iOS[cite: 1]. Al autorizar peticiones localmente mediante validación criptográfica, se elimina la necesidad de que el servidor ejecute consultas iterativas a la base de datos, reduciendo la huella de carbono en la infraestructura backend[cite: 1].

## ⚙️ Requisitos del Sistema Para El Desarrollo

- **Hardware:** Ordenador Mac (MacBook, Mac mini, Mac Studio, iMac).
- **Sistema Operativo:** macOS 14.0 (Sonoma) o superior.
- **IDE:** Xcode 15.0 o superior (Requerido para el uso nativo de SwiftData y la herramienta Energy Log).
- **SDK:** iOS 17.0 o superior.

## 🚀 Guía de Ejecución del Proyecto en Xcode (Mac)

Sigue estos pasos para compilar y ejecutar el proyecto desde cero hasta visualizarlo en el simulador de tu Mac:

1.  **Clonar el Repositorio:**
    Abre la aplicación **Terminal** en tu Mac y ejecuta:

    ```bash
    git clone [git@github.com:dani503sv/EcoMercadito-IOS.git](git@github.com:dani503sv/EcoMercadito-IOS.git)
    cd EcoMercadito-IOS
    ```

2.  **Abrir el Proyecto en Xcode:**
    En la misma terminal, abre el archivo del proyecto ejecutando:

    ```bash
    open EcoMercadito-IOS.xcodeproj
    ```

    _(Nota: Si usas Swift Package Manager local o configuraste un workspace, abre `EcoMercadito-IOS.xcworkspace`)._

3.  **Seleccionar el Dispositivo Destino (Simulador):**
    - En la barra superior de Xcode, haz clic en el selector de dispositivos (junto al esquema "EcoMercadito-IOS").
    - Selecciona un simulador con pantalla OLED simulada para apreciar el diseño Dark Mode (ej. **iPhone 15 Pro** o **iPhone 15 Pro Max**).

4.  **Compilar y Ejecutar (Build & Run):**
    - Haz clic en el botón de **Play** (▶) en la esquina superior izquierda de Xcode, o utiliza el atajo de teclado: `Command (⌘) + R`.
    - Xcode comenzará a compilar el código fuente en Swift, resolverá las librerías nativas y lanzará el **Simulator**.
    - El simulador de iOS arrancará (puede tomar unos segundos la primera vez) y la aplicación mostrará la Pantalla de Carga y Redirección en negro absoluto (`#000000`). Si no hay un token JWT en el Keychain, el sistema te redirigirá instantáneamente a la pantalla de Login[cite: 1].

## 📊 Protocolos de Auditoría Energética

El proyecto no solo entregará el producto de software funcional, sino que incluirá una auditoría técnica documentada. Para validar empíricamente el ahorro de recursos, utilizaremos las siguientes herramientas de instrumentación al ejecutar la app:

- **Xcode Energy Gauge:** Accesible durante la ejecución en la pestaña _Debug Navigator_. Muestra en tiempo real el costo energético de la app (Network, CPU, GPU).
- **Instruments (Energy Log):** Herramienta de profiling (Atajo: `Command (⌘) + I`) que permite rastrear picos de encendido de la antena baseband y ciclos de CPU ineficientes.
- **MetricKit:** Framework integrado en el código que medirá cuantitativamente en producción los miliamperios-hora (mAh) consumidos, el tiempo de uso de la CPU y los bytes transferidos en red frente a otras aplicaciones convencionales[cite: 1].
