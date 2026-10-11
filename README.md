# 🧺 Varal Automatizado Residencial com CLP (TCC)

Projeto e desenvolvimento de um **Varal Automatizado Residencial** de alta robustez, concebido como Trabalho de Conclusão de Curso Técnico em Automação Industrial no **Instituto Federal Fluminense (IFF - Campus Santo Antônio de Pádua)**.

Ao contrário de soluções domésticas convencionais baseadas em microcontroladores de prototipagem (como Arduino), este sistema foi projetado com **Controladores Lógicos Programáveis (CLP)** industriais, garantindo operabilidade contínua sem falhas a longo prazo, imunidade a variações térmicas e proteção contra intempéries.

---

## 🛠️ Arquitetura de Hardware e Componentes

| Componente | Função / Especificação | Preço (BRL) |
| :--- | :--- | :---: |
| **Sensor de Chuva e Módulo (MH-RD)** | Detecção capacitiva/resistiva de gotas de água | R$ 7,29 |
| **Relé de 5V (SRD-05VDC-SL-C)** | Conversão de sinal do sensor (5V) para o CLP (24V) | R$ 8,30 |
| **Relés de 24V (YA224H-C-S-)** | Casamento de sinal entre saídas transistor do CF2n e entradas do LOGO! | R$ 10,00 |
| **CLP Principal (CF2n-18MT-2T / Lollete)** | Cérebro do sistema (Equivalente Mitsubishi FX2NC / Modbus RS232) | R$ 344,23 |
| **CLP Auxiliar (Siemens LOGO! RC24)** | Atuação de inversão de polaridade (Ponte H) do motor | R$ 890,00 |
| **Motor de Vidro Elétrico Automotivo** | Atuador DC 12V/24V de alto torque com rotação bidirecional | R$ 60,00 |
| **Estrutura Mecânica** | Madeira, mão francesa e roldanas de tração | R$ 62,00 |
| **Quadro de Comando** | Caixa de passagem elétrica com grau de proteção para ambiente externo | — |
| **CUSTO TOTAL DO PROJETO** | | **R$ 1.381,82** |

---

## ⚡ Soluções de Engenharia e Integração Elétrica

1. **Condicionamento de Sinal (5V ➔ 24V):**
   * Os CLPs industriais operam com entradas digitais de 24V, enquanto o sensor de chuva MH-RD fornece sinal lógico de 5V.
   * **Solução:** O sinal de 5V aciona a bobina do relé SRD-05VDC, que comuta uma fonte externa de 24V para a entrada física `X005` do CLP principal.

2. **Casamento de Tecnologia Transistor ➔ Relé (Sourcing ➔ Sink):**
   * A saída do CLP CF2n é do tipo *transistor sourcing*, incompatível com a entrada do Siemens LOGO! (tipo *sink*).
   * **Solução:** Foram adicionados relés intermediários de 24V acionados pelo CF2n para fornecer contactos secos às entradas do Siemens LOGO!.

3. **Inversão de Polaridade do Motor DC (Ponte H via CLP):**
   * Para expor e recolher as roupas, o motor de vidro elétrico necessita de inversão de polaridade (+12V/-12V).
   * **Solução:** O CLP Siemens LOGO! foi programado para alternar a comutação das suas saídas a relé. Quando a Entrada 1 é acionada, energiza a polaridade horária; quando a Entrada 2 é acionada, desativa a primeira e energiza a polaridade anti-horária, evitando curto-circuito no sistema.

---

## 🧠 Lógica de Programação Ladder (GX Works2)

A programação do CLP principal foi desenvolvida em **Linguagem Ladder** através do software **MELSOFT GX Works2**.

* **Filtro Anti-Ruído Temporal:** Para evitar recolhimentos falsos provocados por respingos isolados ou humidade passageira, foram implementados temporizadores (`T200 K300`, `T253 K100`) e memórias virtuais (`M10`).
* **Sincronismo de Ação:** O sistema aguarda a confirmação de precipitação contínua antes de enviar o sinal de recolhimento (`Y002`). Se a gota escorrer do sensor, o temporizador reinicia o ciclo de contagem.
* **Comando Manual:** Botões físicos interligados às entradas `X001` e `X002` permitem expor ou recolher o varal manualmente a qualquer momento.

### 🧩 Diagrama Ladder em Texto (GX Works2)

```text
================================================================================
 LÓGICA 1: EXPOR VARAL (Aguardar confirmação de ausência de chuva)
================================================================================
 Line 25:
 |--[ X005 ]-------[/ X005 ]-------------------------------------( T200 K300 )--|
 |
 |--[ M10  ]-------[/ T253 ]-------------------------------------( M10  )-------|

 Line 36:
 |--[ T200 ]-------[/ T253 ]-------[/ Y001 ]-------[/ X002 ]-------( Y001 )-------|
 |
 |--[ X001 ]-------[/ X001 ]-------------------------------------( T253 K100 )--|

 Line 48:
 |--[ M10  ]-----------------------------------------------------[ RST T200  ]--|
 |
 |--[ X005 ]-----------------------------------------------------

================================================================================
 LÓGICA 2: RETRAIR VARAL (Detecção de chuva e contagem de segurança)
================================================================================
 Line 0:
 |--[ X005 ]-----------------------------------------------------( T201 K300 )--|

 Line 4:
 |--[ T201 ]-------[/ T251 ]-------[/ Y002 ]-------[/ X001 ]-------( Y002 )-------|
 |
 |--[ X002 ]-------[/ X002 ]-------------------------------------( T251 K100 )--|

 Line 16:
 |--[ X005 ]-----------------------------------------------------[ RST T201  ]--|

 Line 20:
 |--[ T201 ]-----------------------------------------------------[ RST T251  ]--|
 |
 |--[ X002 ]-----------------------------------------------------

 ---

## 🎓 Autores e Instituição

* **Autores:** João Pedro Rocha Assis, Álvaro Freitas, Davi Alexandre Silva Santana
* **Orientadora:** Prof.ª Valéria Rodrigues Valle
* **Instituição:** Instituto Federal Fluminense (IFF) — Campus Santo Antônio de Pádua
* **Data de Submissão:** Novembro de 2023
