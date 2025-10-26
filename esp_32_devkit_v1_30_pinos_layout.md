          ┌────────────────────────────┐
     EN   *                            * D23
     VP   *        Processador         * D22
     VN   *                            * TX0
     D34  *                            * RX0
     D35  *                            * D21
     D32  *                            * D19
     D33  *                            * D18
     D25  *                            * D5
     D26  *                            * TX2
     D27  *                            * RX2
     D14  *                            * D4
     D12  *                            * D2
     D13  *                            * D15
     GND  *                            * GND
     VIN  *                            * 3V3
          └───────────USB──────────────┘


# LADO ESQUERDO !!!
| Pino            | Nome completo | Função principal        | Observações                                            |
| --------------- | ------------- | ----------------------- | ------------------------------------------------------ |
| **EN**          | Enable        | Habilita o chip         | Resetar = puxar para GND momentaneamente               |
| **VP (ou D36)** | ADC1_CH0      | Entrada analógica (ADC) | Somente entrada (não pode ser saída)                   |
| **VN (ou D39)** | ADC1_CH3      | Entrada analógica (ADC) | Somente entrada                                        |
| **D34**         | GPIO34        | ADC1_CH6                | Somente entrada                                        |
| **D35**         | GPIO35        | ADC1_CH7                | Somente entrada                                        |
| **D32**         | GPIO32        | ADC1_CH4, DAC1          | Entrada/Saída                                          |
| **D33**         | GPIO33        | ADC1_CH5, DAC2          | Entrada/Saída                                          |
| **D25**         | GPIO25        | ADC2_CH8, DAC1          | Entrada/Saída                                          |
| **D26**         | GPIO26        | ADC2_CH9, DAC2          | Entrada/Saída                                          |
| **D27**         | GPIO27        | ADC2_CH7                | Entrada/Saída                                          |
| **D14**         | GPIO14        | ADC2_CH6, HSPI_CLK      | Cuidado: usado no boot                                 |
| **D12**         | GPIO12        | ADC2_CH5, HSPI_MISO     | Pino sensível: se estiver alto no boot, pode travar    |
| **D13**         | GPIO13        | ADC2_CH4, HSPI_MOSI     | Entrada/Saída                                          |
| **GND**         | —             | Terra                   | Comum a todos os circuitos                             |
| **VIN**         | —             | Alimentação (5 V)       | Pode alimentar o ESP32 por aqui                        |


# LADO DIREITO !!!
| Pino    | Nome completo | Função principal  | Observações                                       |
| ------- | ------------- | ----------------- | ------------------------------------------------- |
| **D23** | GPIO23        | VSPI_MOSI         | Entrada/Saída normal                              |
| **D22** | GPIO22        | I2C_SCL           | Entrada/Saída normal                              |
| **TX0** | GPIO1         | UART0_TX          | Usado para serial (Serial Monitor)                |
| **RX0** | GPIO3         | UART0_RX          | Usado para serial (Serial Monitor)                |
| **D21** | GPIO21        | I2C_SDA           | Entrada/Saída normal                              |
| **D19** | GPIO19        | VSPI_MISO         | Entrada/Saída normal                              |
| **D18** | GPIO18        | VSPI_CLK          | Entrada/Saída normal                              |
| **D5**  | GPIO5         | VSPI_CS           | Usado no boot — evite pino alto ao ligar          |
| **TX2** | GPIO17        | UART2_TX          | Comunicação serial secundária                     |
| **RX2** | GPIO16        | UART2_RX          | Comunicação serial secundária                     |
| **D4**  | GPIO4         | ADC2_CH0          | Pode ser usado para LEDs, sensores, etc           |
| **D2**  | GPIO2         | ADC2_CH2          | Pino de boot (deve estar LOW para modo normal)    |
| **D15** | GPIO15        | ADC2_CH3          | Também influencia o boot                          |
| **GND** | —             | Terra             | Igual ao GND do outro lado                        |
| **3V3** | —             | Alimentação 3.3 V | Saída regulada da placa                           |



# Pinos de Atenção (não usar em alguns casos)
| Pino        | Motivo                                              |
| ----------- | --------------------------------------------------- |
| D12         | Pode travar o boot se estiver alto na inicialização |
| D2, D15, D5 | Também interferem no modo de boot                   |
| D34–D39     | Apenas entrada (não podem acionar LEDs, relés, etc) |


# Pinos “seguros” para uso geral (GPIO normais)
## Esses são ideais para testar LEDs, sensores, botões, etc:
D13, D14, D25, D26, D27, D32, D33
D21, D22, D23, D18, D19


# Sobre alimentação
VIN → recebe 5 V (do USB ou fonte externa 5 V).
3V3 → saída regulada (3.3 V) — não alimente o ESP32 por aqui se usar o USB ao mesmo tempo.
GND → comum a todos os componentes externos.