# 📡 Visão Geral do Projeto: Rastreamento por Wi-Fi (ESP32)

O objetivo central deste código é utilizar o ESP-WROOM-32 para determinar uma localização aproximada dentro de uma área pré-definida. Ele faz isso escaneando redes Wi-Fi próximas, calculando a distância até elas com base na intensidade do sinal (RSSI) e enviando esses dados, juntamente com seu status de ocupação, para um servidor.

## 💻 Tecnologias e Componentes

| Categoria |	Componente/Tecnologia |	Função no Projeto |
| :--- | :--- | :--- |
| Microcontrolador | ESP-WROOM-32 | Processamento de dados, varredura Wi-Fi, comunicação HTTP. |
| Linguagem | C++/C (Framework Arduino) |	Código principal de firmware. |
| Comunicação |	Wi-Fi, HTTPClient |	Conexão à rede local e envio de dados para o servidor (Endpoint 192.168.0.106:3000). |
| Sinalização |	3 LEDs (Verde, Vermelho, Amarelo) |	Feedback visual sobre status e erros. |
| Interface |	Botão (botaoStatus) |	Alternância de estado (Disponível / Ocupado). |
| Localização |	RSSI (Potência do Sinal) |	Usado para trilateração ou fingerprinting (a função calcularDistancia sugere cálculo de distância). |

# 💡 Modelo de Funcionamento e Sinais Visuais

O dispositivo opera em um ciclo contínuo (loop) de varredura de Wi-Fi e envio de dados, com sinalização clara do estado:

| LED |	Cor |	Função Principal |	Estado Lógico (Variável) |
| :--- | :--- | :--- | :--- |
| 🟢 |	Verde |	Disponível (Status Padrão) |	ledVerdeLigado = true |
| 🔴 |	Vermelho |	Ocupado | (Acionado pelo botão) |	ledVerdeLigado = false |
| 🟡 |	Amarelo |	Sinalização de Erro |	Erros de conexão Wi-Fi, falha no acesso à internet ou falha de push HTTP. |

# 🟡 Sinalização de Erro (LED Amarelo):

O código já define um protocolo de piscadas para erros específicos:

| Evento | Padrão de Piscar (Amarelo) |	Função de Erro |
| :--- | :--- | :--- |
| Tentativa de Conexão Wi-Fi |	2 piscadas (lenta) |	Indica que o dispositivo está tentando se conectar à rede local. |
| Falha na Internet |	4 piscadas (lenta) |	Conectado ao Wi-Fi, mas sem acesso à internet (falha no checagemDeInternet). |
| Falha Crítica (Wi-Fi) |	5 piscadas (rápida) |	Falha após muitas tentativas de conexão/reconexão Wi-Fi. |
| Falha no POST HTTP |	3 piscadas (normal) |	Erro ao tentar enviar dados ao servidor de rastreamento. |
