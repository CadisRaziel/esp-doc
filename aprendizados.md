# Conectar pelo USB
- Se for conectar somente a esp32 (sem sensores ou qualquer outro hardware junto), conecte pelo USB e coloque no computador (nao e necessario utilizar o pino vin de 5v)
| Pino    | Função                                         |
| ------- | ---------------------------------------------- |
| **VIN** | Entrada de 5V (quando **USB está fora**)       | -> USB conectado → NÃO usar VIN !!!!

| Fonte de energia | Pino usado                           | Situação                                    |
| ---------------- | ------------------------------------ | ------------------------------------------- |
| Via USB          | 5V chega internamente → regula → 3V3 | **Usar USB apenas**                         |
| Via externa 5V   | VIN                                  | **USB desconectado**NAO usar ao mesmo tempo |


# Luzes 
- Quando conectada e ligada uma luz vermelha e ligada (informa que tem energia e esta ligada)

# Sensores
- Quando for utilizar sensores (deixe a esp conectada pelo usb) utilize os pinos 3V3 e GND da ESP32 para alimentar sensores
| Pino    | Função                                         |
| ------- | ---------------------------------------------- |
| **3V3** | Alimentação de **3.3V regulada** para sensores |
| **GND** | Terra                                          |
