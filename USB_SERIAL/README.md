# Programando o ESP01/ESP01S com o conversor digital de outra placa Arduino/Esp 

## Programando o ESP-01 com a Interface USB-Serial do Arduino UNO

Há um bom tempo atrás adquiri alguns módulos relés com ESP-01 e uns móduos ESP01S que ficaram parados e resolvi colocá-los em uso. O problema é que esses módulos não possuem suporte nativo para comunicação direta com USB, diferente de um arduino UNO ou um Esp32 que possuem circuitos dedicados para comunicação USB,  o que dificulta a gravação de novos códigos. Para resolver isso, adquiri um conversor específico para esses módulos, mas logo começaram os problemas: o módulo não conectava de jeito nenhum! Atualizei drives, fiz as configurações iniciais, mas sem sucesso parti para o esquemático da  placa e o chip central estava sem identificação, pelo formato um CH340, mas as pinagens não batiam, noo gerenciador de dispositivos também não... Parti então para outra abordagem:


**⚠️ Observações importantes:**
Este método é funcional apenas para fazer o upload de novos códigos.
Em alguns casos, a comunicação do monitor serial pode não funcionar corretamente após a gravação, mas já encontrei outra solução para isso — funciona bem e compartilho os detalhes mais abaixo.

## Com Arduino:
<img src ="https://github.com/user-attachments/assets/f6aec310-41b1-4085-bf3b-00b0a0cb9f98">

Esquemático:

<img src ="https://github.com/user-attachments/assets/5f4c624a-6ea5-49d1-9d58-c3d4163e6f6a">


**Etapas**
- Certifiquece de que não tem nenhum monitor serial aberto
- FAÇA TODA A MONTAGEM COM O ARDUINO DESCONECTADO DA USB
- ENABLE ESP01 -> 3.3V
- Inicie o Arduino na USB como GPIO0 do ESP01 em 3.3V, isso diz para o chip entrar em modo de gravação na flash, faça o uploud. Mantenha lá até que tudo tenha sido enviado (pode ficar aparecendo umas mensagens de erro na USB, ignore, o importante é que a placa apareça na COM0/1/2/3....)
- Retire o GPIO0 do 3.3V e faça um reset; RST ESP01-> GND (Toque rápido) 

As duas dúvidas que podem surgir é: 

- Porque o monitor serial não funciona:
- 
    Tenho algumas suposições, entre elas a de que isso ocorre devido que na comunicação UART (RX/TX) no microcontrolador ATMEGA328P, o nível de tensão é 5V  e no ESP 3.3V, quando o dado é enviado pelo pc (Ex. Firmware),  o dado sai no TX do chip serial ainda na faixa de 5V, mas isso contornamos com um divisor de tensão que muda o rande de 5V para aproximadamente 3.3V. Contudo, o chip serial também espera dados no seu RX, mas na faixa de 5V o que não é tão simples como utilizar resistores, teriamos que elevar a saida TX do Esp para faixa de 5V para ele entender bem o que é 0 e o que é 1: 0 -> ~0V e 1 -> ~5V, colocando o TX direto podem ocorrer falhas, essa é a minha proposição
  
- Por que RX-RX e TX-TX
- 
Não estamos comunicando o ESP01 com o microcontrolador da placa, mas "substituindo" ele por outro que se comunicar com o chip serial. Para isso temos que saber onde o chio serial se comunica, ele se comunica no RX0 e TX0 da placa que é a mesma conexão chip, como vou substitui-lo, preciso que o novo componente tenha os pinos da comunicação no lugar do anterior, internamente o conversor serial conecta-se RX-TX e TX-RX.

## Protoboard ruim, cabos folgados

Para tirar essa dúvida descidi fazer algo "final", peguei o conversor que não funcionava e tirei os componentes para usar apenas a barra de pinos dele, mas mesmo assim continuou sem o monitor serial e mesmos resultados...


## Ideia final e conclusão

  Embora possa quebrar um galho, trabalhar sem o monitor pode ser muito ruim, um vez que você não tem feedback, debugar o código nem se fala, em vários momentos aparecia a mensagem de sucesso no uploud, mas o código ainda era o mesmo... Estava trabalhando por wifi e o quão bom é poder ir até o monitor serial e ver o IP que a rede setou, simples e rápido.

A lógica é a mesma, mas agora vamos utilizar uma placa de dessenvolvimento que tenha chip serial e que o microcontrolador ja trabalhe em 3.3V, escolhi um ESP32, como a UART dele já trabalha no mesmo range do ESP1/ESP01S, o chip serial já entenderá o ESP01/ESP01S. Essa foi minha dúvida e ao conectar deu certo.

**⚠️OBS**

Para manter o chip do ESP32 desligado, conecte EN (Enable) em GND
