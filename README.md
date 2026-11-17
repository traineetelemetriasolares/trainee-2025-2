# Trainee Solares 2025/2 — Telemetria e Gerenciamento Energético

Projeto desenvolvido para o **Desafio Trainee 2025/2 (Novembro): Telemetria e Gerenciamento Energético**, da equipe Solares (Universidade Federal do Espírito Santo).

**Autores:** Augusto Lopes Zanotelli e Fernanda Coutinho Correa Calazans

---

## Sobre o desafio

Durante o Desafio Solar Brasil (DSB), a equipe precisa monitorar em tempo real os parâmetros do barco para tomar as melhores decisões de prova. O desafio trainee reproduz esse sistema de telemetria em quatro etapas:

| Etapa | Descrição | Período (edital) | Status |
|---|---|---|---|
| 1 | Simulação do circuito de telemetria no Tinkercad + relatório | 17/11/2025 a 20/11/2025 | Concluída |
| 2 | Circuito físico (ESP32) e envio de dados via Wi-Fi/websocket | 21/11/2025 e 24/11/2025 | Concluída |
| 3 | Servidor Socket.IO e página web com gráficos | 25/11/2025 a 01/12/2025 | Concluída |
| 4 | Apresentação final: teste no barco de competição | Depende de AS&GA | Concluída |

Os documentos do desafio e o relatório estão neste repositório:

- [`Desafio Trainee Telemetria 2025_2.pdf`](./Desafio%20Trainee%20Telemetria%202025_2.pdf): edital com as regras e requisitos.
- [`Trainee Solares - Etapa 1 - Relatório do Projeto.pdf`](./Trainee%20Solares%20-%20Etapa%201%20-%20Relatório%20do%20Projeto.pdf): relatório da Etapa 1.

---

## Etapa 1 — Simulação no Tinkercad

Conforme o edital, o circuito deve medir e interpretar:

- **Botão on/off:** identificar se está ligado ou desligado.
- **Sensor de temperatura:** leitura de um sensor digital, exibida em graus Celsius.
- **Potenciômetro (acelerador do barco):** terminais com 48 V; a tensão no terminal variável representa a posição do acelerador (0 V = posição 0; 48 V = posição 10).
- **LCD:** exibe os valores do potenciômetro e da temperatura.

🔗 **[Abrir o projeto simulado no Tinkercad](https://www.tinkercad.com/things/3M4xveGDgJu-controlador-de-velocidade-a-partir-de-1-potenciometro/editel?returnTo=https%3A%2F%2Fwww.tinkercad.com%2Fdashboard%2Fdesigns%2Fall&sharecode=p_4Hd1n5M2GiwKIocnELDb6se_sLlSwU05FmHlZsl0g)**

### Materiais

| Categoria | Componentes |
|---|---|
| Microcontrolador | ESP (modelo final) / Arduino UNO (simulação) |
| Potenciômetros | 1 × 5 kΩ, 1 × 10 kΩ |
| Resistores | 1 × 880 Ω, 1 × 220 Ω, 1 × 134 kΩ (ESP) / 91 kΩ (Arduino) |
| Outros | Regulador step-down 32 V → 5 V (LM2596), LCD 16x2, botão |

### Principais desafios e soluções

- **Fonte de ~48 V acima do limite do LM2596 (32 V):** a tensão foi reduzida para ~24 V com um resistor em série, dimensionado pela corrente total estimada (~30 mA):

  `R = 24 V / 0,03 A ≈ 800 Ω` → resistor de **820 Ω** na prática.

  A primeira abordagem (diodo zener + transistor NPN + resistor de 4,7 kΩ) foi trocada por uma solução mais simples, após orientação do avaliador.

- **Sinal do potenciômetro de 0 a 48 V:** as entradas analógicas do ESP32 aceitam no máximo 3,3 V. Foi projetado um **divisor de tensão** para adequar o sinal, mantendo a tensão sobre o potenciômetro abaixo de 5 V (resistor em série de ~86 kΩ com potenciômetro de 10 kΩ).

O circuito simulado mostra no LCD a temperatura e a posição do acelerador (escala de 0 a 10). O relatório completo, com limitações e caminhos percorridos, está no PDF da Etapa 1.

---

## Etapa 3 — Painel web (`painel-solares`)

Página web de telemetria "Poente Solares", desenvolvida em **TypeScript** (requisito do edital), com:

- **Aceleração** (0 a 10): valor atual, barra de progresso e gráfico histórico.
- **Temperatura** (°C): alerta visual acima de 32 °C e gráfico histórico.
- **Status de conexão** (online/offline).
- Histórico das últimas leituras, atualizado a cada segundo.

> **Estado atual:** os dados são **simulados** no front-end. A integração com o servidor via **Socket.IO** e com o ESP32 (websocket) foi concluída mas, para fins visuais, mostramos dados simulados no site atual.

### Tecnologias

React 19 · TypeScript · Vite · Tailwind CSS · Recharts · Lucide React

### Como rodar

Pré-requisito: [Node.js](https://nodejs.org/) instalado.

```bash
cd painel-solares
npm install
npm run dev
```

Abra no navegador o endereço exibido no terminal (normalmente `http://localhost:5173`).

Outros comandos:

```bash
npm run build     # gera a versão de produção
npm run preview   # visualiza a build localmente
npm run lint      # executa o ESLint
```

---

## Estrutura do repositório

```
.
├── painel-solares/                                   # Painel web de telemetria (React + Vite + TypeScript)
│   ├── src/
│   │   ├── App.tsx
│   │   ├── main.tsx
│   │   └── index.css
│   ├── index.html
│   └── package.json
├── Desafio Trainee Telemetria 2025_2.pdf             # Edital do desafio
├── Trainee Solares - Etapa 1 - Relatório do Projeto.pdf
└── README.md
```

---

## Referências

- Arduino. *Language Reference*. https://www.arduino.cc/reference/en/
- EONE. *1602A-1: liquid crystal display module*. https://blog.eletrogate.com/wp-content/uploads/2018/04/EONE-1602a1.pdf
- Datasheet LM2596 (3A step-down voltage regulator). https://www.alldatasheet.com/datasheet-pdf/pdf/223022/ESTEK/LM2596.html