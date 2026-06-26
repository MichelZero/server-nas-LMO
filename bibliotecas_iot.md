
# Bibliotecas IoT
#### Bibliotecas MQTT para ESP32 e ESP8266

### doubleResetDetector
- **Descrição:** Detecta se o microcontrolador foi reiniciado duas vezes em um curto período de tempo, útil para entrar em modo de configuração.
- **Instalação:** Pode ser instalada via Arduino IDE Library Manager ou baixada do GitHub: [https://github.com/MichelZero/ESP_DoubleResetDetector.git](https://github.com/MichelZero/ESP_DoubleResetDetector.git)
- **Uso:** Inclua a biblioteca no seu código e configure o tempo de detecção. Se o microcontrolador reiniciar duas vezes dentro desse período, você pode executar uma ação específica, como entrar em modo de configuração Wi-Fi.

### WiFiManager
- **Descrição:** Facilita a configuração de redes Wi-Fi em dispositivos ESP32 e ESP8266, permitindo que o usuário configure a rede através de um portal web.
- **Instalação:** Pode ser instalada via Arduino IDE Library Manager ou baixada do GitHub: [https://github.com/MichelZero/WiFiManager.git](https://github.com/MichelZero/WiFiManager.git)
- **Uso:** Inclua a biblioteca no seu código e configure o portal de configuração. Quando o dispositivo não conseguir se conectar à rede Wi-Fi, ele iniciará um ponto de acesso para que o usuário possa configurar a rede.

### pubsubclient
- **Descrição:** Biblioteca MQTT para ESP32 e ESP8266, permitindo a publicação e assinatura de tópicos MQTT.
- **Instalação:** Pode ser instalada via Arduino IDE Library Manager ou baixada do GitHub: [https://github.com/MichelZero/pubsubclient.git](https://github.com/MichelZero/pubsubclient.git)
- **Uso:** Inclua a biblioteca no seu código e configure as conexões MQTT. Você pode publicar mensagens em tópicos específicos e assinar tópicos para receber mensagens.

### ESP32MQTTClient
- **Descrição:** Biblioteca MQTT específica para ESP32, oferecendo funcionalidades avançadas para comunicação MQTT.
- **Instalação:** Pode ser instalada via Arduino IDE Library Manager ou baixada do GitHub: [https://github.com/cyijun/ESP32MQTTClient.git](https://github.com/cyijun/ESP32MQTTClient.git)
- **Uso:** Inclua a biblioteca no seu código e configure as conexões MQTT. Você pode publicar mensagens em tópicos específicos e assinar tópicos para receber mensagens.

### ESP8266WiFi
- **Descrição:** Ela é uma biblioteca nativa (Built-in): A ESP8266WiFi.h já vem embutida quando você instala o pacote de placas do ESP8266 na Arduino IDE.

