# 💡 Smart Lamp — Lâmpada Inteligente IoT

Projeto de uma lâmpada inteligente baseada em ESP32, integrada via **MQTT** para permitir o controle remoto (ligar/desligar) e o monitoramento de luminosidade em tempo real, com foco em integração com plataformas IoT como o **FIWARE**.

Desenvolvido pelo grupo **Colosys**.

## 👥 Autores

- Beatriz dos Santos Silva — RM573698
- Gabriel Kenzo -  RM569780
- Lorenzo Mendes Pena — RM570036
- Maria Clara Ramos Santini — RM573246
- Murillo Perez da Fonseca — RM573674

## 📋 Sobre o projeto

O firmware roda em um ESP32 e:

- Conecta-se a uma rede Wi-Fi;
- Conecta-se a um broker MQTT;
- Escuta comandos remotos para ligar/desligar um LED (que representa a lâmpada);
- Publica periodicamente o estado atual da lâmpada (ligada/desligada);
- Lê um sensor de luminosidade (potenciômetro/LDR) e publica seu valor, convertido para uma escala de 0 a 100.

## 🔧 Hardware utilizado

| Componente | Pino | Função |
|---|---|---|
| LED onboard | GPIO 2 (`D4`) | Representa o estado da lâmpada (ligada/desligada) |
| Sensor de luminosidade (potenciômetro/LDR) | GPIO 34 | Leitura analógica de luminosidade |

> O projeto foi desenhado para simulação no [Wokwi](https://wokwi.com/), usando a rede `Wokwi-GUEST`, mas pode ser adaptado para uma placa ESP32 física.

## 📚 Bibliotecas necessárias

Instale via Arduino IDE (Gerenciador de Bibliotecas) ou PlatformIO:

- [`WiFi.h`](https://github.com/espressif/arduino-esp32) — nativa do core ESP32
- [`PubSubClient`](https://github.com/knolleary/pubsubclient) — cliente MQTT

## ⚙️ Configuração

As credenciais e parâmetros de conexão ficam no topo do arquivo `smart-lamp.ino` e podem ser ajustados conforme o ambiente:

```cpp
const char* default_SSID = "Wokwi-GUEST";        // Nome da rede Wi-Fi
const char* default_PASSWORD = "";               // Senha da rede Wi-Fi
const char* default_BROKER_MQTT = "46.17.108.113"; // IP do broker MQTT
const int   default_BROKER_PORT = 1883;          // Porta do broker MQTT
const int   default_D4 = 2;                      // Pino do LED onboard
```

## 📡 Tópicos MQTT

| Tópico | Direção | Descrição |
|---|---|---|
| `/TEF/<lamp-id>/cmd` | Inscrição (subscribe) | Recebe comandos de controle da lâmpada |
| `/TEF/<lamp-id>/attrs` | Publicação (publish) | Envia o estado atual da lâmpada (`s\|on` / `s\|off`) |
| `/TEF/<lamp-id>/attrs/l` | Publicação (publish) | Envia o valor de luminosidade (0–100) |

### Formato dos comandos

O dispositivo entende mensagens no padrão `<prefixo>@<comando>|`, onde o prefixo é `lamp200`(mudar para o id da lâmpada cadastrada):

- `lamp200@on|` → liga o LED
- `lamp200@off|` → desliga o LED

## 🚀 Como usar

1. Abra o projeto no [Wokwi](https://wokwi.com/) ou na Arduino IDE com suporte a placas ESP32 configurado.
2. Ajuste as constantes de Wi-Fi e broker MQTT, se necessário.
3. Faça o upload do firmware para a placa (ou rode a simulação).
4. Abra o Monitor Serial a 115200 baud para acompanhar os logs de conexão.
5. Use um cliente MQTT (ex: [MQTT Explorer](http://mqtt-explorer.com/) ou `mosquitto_pub`) para:
   - Publicar `lamp200@on|` ou `lamp200@off|` no tópico `/TEF/lamp200/cmd` e controlar o LED;
   - Assinar `/TEF/lamp200/attrs` e `/TEF/lamp200/attrs/l` para acompanhar o estado e a luminosidade.

Exemplo com `mosquitto_pub`:

```bash
mosquitto_pub -h 46.17.108.113 -t /TEF/lamp200/cmd -m "lamp200@on|"
```

## Links

- [Wokwi](https://wokwi.com/projects/475496233707084801)
- [Github](https://github.com/MurilloFonseca/smart-lamp-colosys)
- [Video](https://www.youtube.com/watch?v=De6Z0xVxRAE)


## 🔄 Fluxo de funcionamento

1. **Setup**: inicializa saída digital, Serial, Wi-Fi e MQTT; pisca o LED e publica estado inicial.
2. **Loop principal**:
   - Verifica e restabelece conexões Wi-Fi/MQTT quando necessário;
   - Publica o estado atual do LED a cada iteração;
   - Lê o sensor de luminosidade e publica o valor mapeado (0–100);
   - Processa mensagens MQTT recebidas via callback.

## 📄 Licença

Projeto acadêmico desenvolvido para fins de estudo.
