# Motor-Brushless-X-NUCLEO-IHM07M1
Acionamento de motor brushless utilizando a lógica six-step por meio de um hardware de potência dedicado. A montagem contará com um botão Start-Stop e um potenciômetro para ajuste de velocidade.

## Diagrama de blocos
A seguir, diagrama de blocos do projeto.

<div align="center">
<img src ="https://github.com/Brenoaalencar/DAC_R2R_8bits/assets/72100554/8c00558d-62e4-49a8-9fbe-363b3e025902" width="500px"/>
</div>

## Diagrama de ligações
A seguir, diagrama de ligações.

<div align="center">
<img src ="https://github.com/Brenoaalencar/DAC_R2R_8bits/assets/72100554/b7db100f-f0a4-477e-8482-ddfde3cb62e3" width="500px"/>
</div>

## Fluxograma
A etapa anterior a elaboração do código de acionamento do motor consiste na 
realização de um fluxograma que represente e facilite a organização da estratégia de 
programação.

<div align="center">
<img src ="https://github.com/Brenoaalencar/DAC_R2R_8bits/assets/72100554/0675a765-e264-4363-a39d-6dcead726f85" width="200px"/>
</div>

## Software
A seguir, sotware com a lógica six-step implementada.

```C++
// Inclusão bibliotecas
#include "mbed.h"
// definição I/O
PwmOut IN_1(PA_8);
PwmOut IN_2(PA_9);
PwmOut IN_3(PA_10);
DigitalOut EN_1(PC_10); // U
DigitalOut EN_2(PC_11); // V
DigitalOut EN_3(PC_12); // W
InterruptIn bt_usuario(PC_13);
AnalogIn Pot(A5);
bool start = 0;
void liga() { start = !start; }

int main() {
  // configurações iniciais
  EN_1.write(0);     // Braço 1 (A)
  EN_2.write(0);     // Braço 2 (B)
  EN_3.write(0);     // Braço 3 (C)
  IN_1.period_ms(1); // PWM = 1 ms
  IN_2.period_ms(1); // PWM = 1 ms
  IN_3.period_ms(1); // PWM = 1 ms
  IN_1.write(0);     // PWM com valor 0%
  IN_2.write(0);     // PWM com valor 0%
  IN_3.write(0);     // PWM com valor 0%
  bt_usuario.rise(&liga);
  int tempo = 3;

  while (1) {
    if (start == 1) {
      // 1
      EN_1.write(1);
      EN_2.write(1);
      EN_3.write(0);
      IN_1.write(1);
      IN_2.write(0);
      IN_3.write(0);
      wait_ms(Pot.read() * 1.2 + 1.8);
      // 2
      EN_1.write(1);
      EN_2.write(0);
      EN_3.write(1);
      IN_1.write(1);
      IN_2.write(0);
      IN_3.write(0);
      wait_ms(Pot.read() * 1.2 + 1.8);
      // 3
      EN_1.write(0);
      EN_2.write(1);
      EN_3.write(1);
      IN_1.write(0);
      IN_2.write(1);
      IN_3.write(0);
      wait_ms(Pot.read() * 1.2 + 1.8);
      // 4
      EN_1.write(1);
      EN_2.write(1);
      EN_3.write(0);
      IN_1.write(0);
      IN_2.write(1);
      IN_3.write(0);
      wait_ms(Pot.read() * 1.2 + 1.8);
      // 5
      EN_1.write(1);
      EN_2.write(0);
      EN_3.write(1);
      IN_1.write(0);
      IN_2.write(0);
      IN_3.write(1);
      wait_ms(Pot.read() * 1.2 + 1.8);
      // 6
      EN_1.write(0);
      EN_2.write(1);
      EN_3.write(1);
      IN_1.write(0);
      IN_2.write(0);
      IN_3.write(1);
      wait_ms(Pot.read() * 1.2 + 1.8);
    }
  }
}
```

Detalhando melhor o desenvolvimento de cada um dos sinais de controle, observa-se 
que as etapas do 6-step foram seguidas atribuindo nível lógico alto para o sinal de 
enable (EN) das fases ativadas e nível lógico baixo ao enable das fases que se 
encontram desativadas. Os sinais IN, que são do tipo PWM são responsáveis por 
estabelecer o sentido da corrente que flui entre as fases, é estabelecido valor 1 para a 
fase “emissora” de corrente e o valor 0 para as fases “receptoras”. 
Para iniciar o ciclo da sequência de acionamento definido pelo 6-step, é necessário que 
o botão do usuário seja ativado, alterando o valor da variável “start” para 1 por meio de 
uma interrupção iniciada com a ocorrência da borda de subida do botão. 
Além disso, o tempo entre um estado e outro do 6-step é alterado constantemente ao 
longo do código conforme o valor do potenciômetro presente no Shield é alterado, 
garantindo que a velocidade de rotação do motor seja variada conforme os valores 
estabelecidos. 
Os valores possíveis para o delay entre o acionamento das fases foram estabelecidos de 
forma a respeitar um intervalo de 1,8 a 3 milissegundos definido empiricamente 
conforme a resposta no giro do motor. 

## Montagem
Após a conclusão do esquemático, a montagem física foi realizada energizando 
o shield com a fonte de bancada e ligando as fases do motor a ele. Uma vez que não foi 
possível acessar porta da placa responsável por fornecer os valores do potenciômetro 
diretamente, foi necessário usar um cabo para receber as informações da porta 
indiretamente por meio de outra entrada analógica (A5).

<div align="center">
<img src ="https://github.com/Brenoaalencar/DAC_R2R_8bits/assets/72100554/f8fcff3c-35dd-417a-8f7a-c9084cd31f02" width="500px"/>
</div>









