# 📡 Visão Geral do Projeto: Rastreamento por Wi-Fi (ESP32)

O objetivo central deste código é utilizar o **ESP-WROOM-32** para determinar uma localização aproximada dentro de uma área pré-definida. Ele faz isso escaneando redes Wi-Fi próximas, calculando a distância até elas com base na intensidade do sinal (RSSI) e enviando esses dados, juntamente com seu status de ocupação, para um servidor.

## 💻 Tecnologias e Componentes

| Categoria |	Componente/Tecnologia |	Função no Projeto |
| :--- | :--- | :--- |
| **Microcontrolador** | ESP-WROOM-32 | Processamento de dados, varredura Wi-Fi, comunicação HTTP. |
| **Linguagem** | C++/C (Framework Arduino) |	Código principal de firmware. |
| **Comunicação** |	Wi-Fi, HTTPClient |	Conexão à rede local e envio de dados para o servidor (Endpoint `192.168.0.106:3000). |
| **Sinalização** |	3 LEDs (Verde, Vermelho, Amarelo) |	Feedback visual sobre status e erros. |
| **Interface** |	Botão (`botaoStatus`) |	Alternância de estado (`Disponível` / `Ocupado`). |
| **Localização** |	RSSI (Potência do Sinal) |	Usado para **trilateração** ou **fingerprinting** (a função `calcularDistancia` sugere cálculo de distância). |

# 💡 Modelo de Funcionamento e Sinais Visuais

O dispositivo opera em um ciclo contínuo (`loop`) de varredura de Wi-Fi e envio de dados, com sinalização clara do estado:

| LED |	Cor |	Função Principal |	Estado Lógico (Variável) |
| :--- | :--- | :--- | :--- |
| 🟢 |	**Verde** |	**Disponível** (Status Padrão) |	`ledVerdeLigado = true` |
| 🔴 |	**Vermelho** |	**Ocupado** | (Acionado pelo botão) |	`ledVerdeLigado = false` |
| 🟡 |	**Amarelo** |	**Sinalização de Erro** |	Erros de conexão Wi-Fi, falha no acesso à internet ou falha de push HTTP. |

# 🟡 Sinalização de Erro (LED Amarelo):

O código já define um protocolo de piscadas para erros específicos:

| Evento | Padrão de Piscar (Amarelo) |	Função de Erro |
| :--- | :--- | :--- |
| **Tentativa de Conexão Wi-Fi** |	2 piscadas (lenta) |	Indica que o dispositivo está tentando se conectar à rede local. |
| **Falha na Internet** |	4 piscadas (lenta) |	Conectado ao Wi-Fi, mas **sem acesso à internet** (falha no `checagemDeInternet`). |
| **Falha Crítica (Wi-Fi)** |	5 piscadas (rápida) |	Falha após muitas tentativas de conexão/reconexão Wi-Fi. |
| **Falha no POST HTTP** |	3 piscadas (normal) |	Erro ao tentar enviar dados ao servidor de rastreamento. |

# 📝 Análise e Pontos de Melhoria
**1. Funções de Rastreamento**
   * `calcularDistancia(int32_t rssi, int txPower)`: Esta função implementa um modelo de atenuação de sinal para estimar a distância em metros a partir do RSSI.
   * `selecionarRoteadoresEspecificos(...)`: Esta função é crítica para a localização. Ela tenta encontrar os SSIDs **alvo** definidos (que estão vazios no código) ou, na ausência deles, seleciona os **três roteadores mais próximos** disponíveis. A robustez do rastreamento dependerá de quão bem o servidor utiliza esses dados (trilateração, machine learning, etc.).

**2. Tratamento de Botão (Interrupção)**
   * O uso de `attachInterrupt` e a função `IRAM_ATTR botaoFoiPressionado()` é a maneira correta de lidar com a entrada do botão, garantindo que o estado mude imediatamente, sem a necessidade de checagem constante no `loop`. O *debounce* de 50ms (microssegundos) é um bom mecanismo de software.

  **3. Conexão e Robustez**

O código inclui funções importantes (`conectarWiFi()` e `verificarConexaoWiFi()`) que usam um loop de reconexão e até mesmo tentam uma **checagem de acesso à internet** (`checagemDeInternet()`), o que torna o dispositivo razoavelmente robusto contra falhas de rede.
