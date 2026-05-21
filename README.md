# Engenharia Reversa e Análise de Hardware: Intelbras W6-1500

##  Visão Geral
Este projeto documenta a exploração de hardware do roteador **Intelbras W6-1500**. O objetivo foi identificar portas de depuração física (UART) na PCB, estabelecer comunicação serial e analisar o processo de boot do firmware.

##  Ferramentas Utilizadas
* **Hardware Alvo:** Roteador Intelbras W6-1500
* **Interface:** Adaptador USB-Serial (TTL) baseado no chip **CH340**
* **Multímetro:** Para identificação de pinagem (GND e VCC)
* **Software:** Linux (Pop!_OS), `screen` para comunicação serial
* **Conectores:** Jumpers macho-fêmea e solda

##  Metodologia

### 1. Teardown (Desmontagem)
O dispositivo foi aberto para expor a PCB (Placa de Circuito Impresso). Foi localizada uma interface de 4 pinos não povoados (pads), típica de conexões UART em roteadores baseados em arquitetura MIPS ou ARM.

### 2. Identificação de Pinout (Pin Hacking)
Utilizando um multímetro, identifiquei a pinagem dos pads:
* **GND:** Identificado via teste de continuidade com a blindagem da antena/USB.
* **VCC:** Identificado medindo a tensão (geralmente 3.3v) durante o boot. *Nota: Não conectar o VCC do adaptador se o roteador estiver ligado na tomada.*
* **TX/RX:** Identificados por tentativa e erro lógico (TX do roteador vai no RX do adaptador e vice-versa).

<img width="576" height="1024" alt="image" src="https://github.com/user-attachments/assets/567b336b-700d-4970-b028-36ec106ac30f" />

### 3. Conexão Serial
A conexão foi feita utilizando o adaptador CH340 com a seguinte configuração:
* **Baud Rate:** 115200 (Padrão de mercado)
* **Comando utilizado:**
    ```bash
    sudo screen /dev/ttyUSB0 115200
    ```

### 4. Análise do Bootlog
Ao ligar o roteador, a comunicação foi estabelecida com sucesso. Foi possível visualizar o carregamento do Bootloader (provavelmente U-Boot) e a inicialização do Kernel Linux.

<img width="1024" height="576" alt="image" src="https://github.com/user-attachments/assets/06c51cd0-442d-48fc-829a-be61da1aff06" />


##  Desafios e Próximos Passos
O acesso direto ao shell de root foi interrompido após o boot completo. O sistema não oferece um prompt de login direto via serial ou o shell está protegido.

**Próximos passos planejados:**
1.  Tentar interromper o **U-Boot** pressionando teclas específicas durante a inicialização inicial.
2.  Analisar o dump do firmware para procurar credenciais hardcoded.
3.  Tentar ataques de **Fault Injection** (Glitching) se o U-Boot estiver bloqueado.

##  Disclaimer
Este projeto foi realizado em equipamento próprio para fins de estudo em cibersegurança e hardware hacking.
