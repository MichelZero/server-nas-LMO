
# Bibliotecas IoT
#### Bibliotecas MQTT para ESP32 e ESP8266

### doubleResetDetector
- **Descrição:** Detecta se o microcontrolador foi reiniciado duas vezes em um curto período de tempo, útil para entrar em modo de configuração.
- **Instalação:** Pode ser instalada via Arduino IDE Library Manager ou baixada do GitHub: [https://github.com/MichelZero/ESP_DoubleResetDetector.git](https://github.com/MichelZero/ESP_DoubleResetDetector.git)
- **Uso:** Inclua a biblioteca no seu código e configure o tempo de detecção. Se o microcontrolador reiniciar duas vezes dentro desse período, você pode executar uma ação específica, como entrar em modo de configuração Wi-Fi.