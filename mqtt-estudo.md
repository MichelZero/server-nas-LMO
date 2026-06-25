# Roteiro de Estudos: Sistemas de Software e Painéis Web com MQTT

Este roteiro contém **4 fases progressivas** projetadas para guiar seu aprendizado desde a visualização rápida de dados até a construção de uma arquitetura de software profissional, segura e escalável utilizando o seu servidor EMQX.

---

## ⏱️ Fase 1: O Primeiro Painel Web (Sem Programação)
*O objetivo aqui é entender o fluxo de dados visualmente e criar um painel funcional.*

*   **Instalação do Node-RED:** Aprenda a instalar e rodar o Node-RED (localmente ou no seu servidor). Essa é a principal ferramenta de desenvolvimento visual baseada em fluxos para ecossistemas IoT.
*   **Domínio de Nós Básicos:** Entenda o funcionamento prático dos nós nativos `mqtt in` (para assinar tópicos do seu EMQX) e `mqtt out` (para publicar comandos).
*   **Criação de Dashboards Rápidos:** Instale o módulo `@flowfuse/node-red-dashboard` (ou o tradicional `node-red-dashboard`). Use os componentes visuais de **Gauge (Medidor)** e **Chart (Gráfico)** para renderizar em tempo real o JSON que você já envia pelo MQTTX.

---

## 🌐 Fase 2: Conexão Nativa Web (JavaScript no Navegador)
*Aqui você aprenderá como os sites modernos recebem dados em tempo real sem a necessidade de atualizar a página (F5).*

*   **Protocolo MQTT sobre WebSockets:** Entenda por que os navegadores web não conseguem usar a porta padrão `1883`. Estude o uso das portas **8083 (WS)** e **8084 (WSS)** configuradas no seu EMQX.
*   **Biblioteca MQTT.js:** Aprenda a importar e utilizar a biblioteca oficial `mqtt.js` diretamente em um arquivo HTML via CDN ou gerenciadores de pacote (npm).
*   **Estrutura de Código Fundamental:** Domine a escrita de scripts em JavaScript para:
    *   Iniciar a conexão: `mqtt.connect('ws://seu-servidor:8083')`
    *   Inscrever-se em tópicos dinamicamente: `client.subscribe('teste/esp32')`
    *   Tratar eventos de chegada: `client.on('message', (topic, message) => { ... })`
*   **Manipulação de DOM:** Pratique a inserção e atualização de elementos HTML na tela (como textos e tabelas) assim que uma nova mensagem MQTT é disparada pelo broker.

---

## 🏗️ Fase 3: Arquitetura de Software e Backend
*Aprenda a processar, centralizar e salvar volumes massivos de dados de forma profissional.*

*   **Padrão Worker / Consumidor:** Crie um serviço em segundo plano (utilizando Node.js, Python ou C#) que rode no servidor 24/7. Ele deve se conectar ao broker e assinar tópicos globais usando wildcards (ex: `empresa/+/sensores/#`) para centralizar as mensagens.
*   **Bancos de Dados para IoT:** Estude a diferença entre bancos relacionais tradicionais (PostgreSQL/MySQL) e bancos de dados de séries temporais (**InluxDB** ou **TimescaleDB**), que são otimizados para armazenar registros ordenados por tempo (*timestamps*).
*   **Engine de Regras do EMQX:** Explore o *Rules Engine* nativo do seu painel EMQX. Aprenda a criar regras SQL internas para direcionar dados diretamente para um banco de dados ou disparar Webhooks HTTP externos sem precisar escrever código de backend.

---

## 🛡️ Fase 4: Produção, Performance e Segurança Web
*O conhecimento necessário para transformar um projeto de testes em um sistema comercial seguro.*

*   **Criptografia WSS:** Configure certificados digitais (como Let's Encrypt) no seu EMQX para que toda a comunicação web trafegue obrigatoriamente sob o protocolo seguro `wss://` (Porta 8084).
*   **Autenticação e Autorização Avançada:** Integre o EMQX ao banco de dados do seu sistema web via JWT (JSON Web Tokens) ou Webhooks de autenticação. Isso garante que um usuário logado no painel só consiga visualizar ou comandar os tópicos aos quais ele realmente tem direito.
*   **Persistência de Estado (Mensagens Retidas):** Domine o uso da flag `retain: true` nas publicações. Isso resolve o problema do "painel em branco": sempre que um usuário abrir o site, o gráfico exibirá instantaneamente o último estado salvo, sem precisar esperar o sensor enviar um novo dado.
