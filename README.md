# JogoTesteDeReflexo
O projeto foi desenvolvido por Ariadne Silva, Arthur Queiroz e César Miranda

# Jogo Teste de Reflexo
O projeto visa desenvolver em um sistema digital embarcado um jogo chamado Jogo de Reflexo, com objetivo de testar a resposta de um usuário diante de um estímulo visual, aliada a recursos de temporização para controlar as etapas do teste: espera aleatória, sinal de resposta, detecção de tempo de reação e exibição dos resultados. O sistema é desenvolvido em linguagem VHDL e implementado na plataforma DE2(Cyclone II). Sua aplicação é utilizada em áreas de psicologia experimental, testes psicotécnicos, treinamento esportivo e reabilitação motora, oferecendo também uma base didática para ensino de lógica digital.

<p align="center">
  <img src="./imagem7.jpg" alt="QR code com o link do projeto"><br>
</p>

## 1. INTRODUÇÃO 

Neste relatório, iremos detalhar os requisitos técnicos, a escolha dos componentes, a estrutura de funcionamento e os resultados obtidos com a simulação do sistema. O projeto tem como foco o desenvolvimento de um Jogo de Reflexo, implementado em linguagem VHDL e executado na plataforma DE2 (FPGA Cyclone II), que visa testar a reação de um usuário diante de um estímulo visual. 

Esse tipo de aplicação é relevante em contextos como treinamentos cognitivos, testes psicotécnicos, exercícios de coordenação motora e ambientes educacionais, além de proporcionar uma oportunidade prática para consolidar conhecimentos em sistemas digitais embarcados. A implementação utiliza recursos fundamentais da placa DE2, incluindo botões (KEYs), switches (SWs), LEDs e displays de 7 segmentos. O controle do sistema é realizado por meio de uma FSM com cinco estados principais (IDLE, WAITING, GO, SUCCESS e SHOW), onde o tempo de espera e a janela de reação são controlados por contadores baseados no clock de 45 MHz da placa. Um gerador de número pseudo aleatório (baseado em LFSR) é utilizado para variar dinamicamente o tempo de espera, proporcionando imprevisibilidade ao teste de reflexo. As interações com o usuário ocorrem por meio de botões de start, resposta e reset, enquanto os LEDs indicam o estado atual do jogo. Os resultados são apresentados nos displays e por meio de sinais visuais, promovendo uma experiência interativa e didática. 

Serão detalhados os requisitos técnicos, a arquitetura do sistema, o funcionamento da lógica de controle, a montagem na placa e os testes realizados por simulação e validação prática. A escolha deste projeto se justifica pela capacidade de integrar múltiplos componentes da FPGA de forma coesa, evidenciando sua aplicabilidade tanto acadêmica quanto funcional. 

## 2. DESENVOLVIMENTO 

Apresentaremos os principais elementos envolvidos no desenvolvimento do projeto Jogo de Reflexo. Isso inclui os requisitos técnicos, a montagem na plataforma DE2, a lógica de funcionamento representada por um fluxograma, além da codificação realizada em linguagem VHDL. 

### 2.1 Requisitos Técnicos 
O projeto foi desenvolvido de acordo com os requisitos estabelecidos pela disciplina de Sistemas Embarcados, sendo todos eles devidamente atendidos e usando em uma FPGA Cyclone II EP2C35F672C6 como hardware principal. Abaixo estão os itens obrigatórios e como eles foram implementados.

<p align="center">Tabela 01 – Itens de Entradas Digitais e Comportamentos Lógicos
</p>

<p align="center">
  <img src="./imagem1.jpeg" alt="Tabela 1"><br>
</p>
<p align="center"><em>Fonte: Elaborada pelos autores.</em></p>

Foram utilizados 2 displays de 7 segmentos para realizar a contagem de acertos, também implementamos uma lógica com máquinas de estados finita(FSM), com cinco estados: IDLE, WATITING, GO, SUCESS e SHOW. 

<p align="center">Figura 01 - Ilustração do funcionamento da Máquina de Estados Finitos
</p>
  
<p align="center">
  <img src="./imagem2.jpeg" alt="Imagem2"><br>
</p>
<p align="center"><em>Fonte: Elaborada pelos autores.</em></p>

A simulação foi realizada no Quartus para verificação das transições dos estados. Além disso, foram coletados requisitos mínimos para criação desse projeto, após a execução do VHDL.

<p align="center">Figura 02 - Elementos Lógicos e Flip Flops
</p>

<p align="center">
  <img src="./imagem3.jpeg" alt="Imagem3"><br>
</p>
<p align="center"><em>Fonte: Produzido no Quartus II.</em></p>

A síntese e o mapeamento do projeto no software Quartus resultaram no uso dos seguintes recursos de hardware do FPGA:

● Células Lógicas (Logic Cells): 
O projeto utilizou um total de 293 célulaslógicas. Essas células lógicas são os blocos fundamentais do FPGA usados para implementar funções lógicas combinacionais (como as portas AND, OR, XOR) e sequenciais (registradores).

● Registradores (Dedicated Logic Registers): 
Foram utilizados 95 registradores dedicados. Estes são os Flip-Flops (FFs) que armazenam os estados do sistema, como o estado atual da máquina de estados (state), os contadores (wait_count, score_count) e o valor do gerador pseudo aleatório (lfsr_counter).

<p align="center">Figura 03 - Recursos Utilizados
</p>

<p align="center">
  <img src="./imagem4.jpeg" alt="Imagem4"><br>
</p>
<p align="center"><em>Fonte: Produzido no Quartus II.</em></p>

● Pinos de I/O (Pins): 
O projeto utilizou 64 pinos de entrada e saída paraconectar os componentes externos, como o clock, botões de reset/start/react e para controlar os 8 displays de 7 segmentos e os 4 LEDs. Além disso, uma análise detalhada da hierarquia de recursos mostrou que o sintetizador do Quartus inferiu automaticamente megafunções otimizadas (LPM - Library of Parameterized Modules) para as operações aritméticas presentes no código VHDL: 

● Módulo de Divisão (lpm_divide:Div0): 
Foi inferido para realizar a operação de divisão inteira (score_count / 10), necessária para obter o dígito das dezenas do placar. Este módulo consumiu 43 células lógicas.

● Módulo de Módulo (lpm_divide:Mod0): Foi inferido para a operação de módulo (score_count mod 10), usada para obter o dígito das unidades do placar. Esta operação é mais complexa que a divisão em hardware, o que se reflete no consumo de 55 células lógicas.

● Módulo de Multiplicação (lpm_mult:Mult0): Foi inferido para a operação de multiplicação (random_base * RANDOM_STEP_CYCLES), que calcula o tempo de espera aleatório. Este módulo foi otimizado para consumir apenas 16 células lógicas. 

Subtraindo os recursos consumidos pelos módulos aritméticos inferidos, conclui-se que a lógica principal do projeto (a máquina de estados, o gerador LFSR e os decodificadores para os displays) consome aproximadamente 179 células lógicas e a totalidade dos 95 registradores. Isso demonstra que as operações de divisão e módulo, apesar de simples em software, representam uma parte significativa (~39%) do consumo de lógica combinacional do projeto quando implementadas em hardware.

**Cálculo:**

● Total de Células Lógicas: 293

● Lógica dos Módulos Inferidos: 43 (Div) + 55 (Mod) + 16 (Mult) = 114

● Lógica Principal do Jogo: 293 - 114 = 179 células lógicas.

<p align="center">Figura 04 - Clocks
</p>

<p align="center">
  <img src="./imagem5.jpeg" alt="Imagem5"><br>
</p>
<p align="center"><em>Fonte: Produzido no Quartus II.</em></p>

Para realizar uma análise de tempo (timing analysis) precisa, foi criada uma restrição de design (através de um arquivo SDC) para informar ao software a frequência de operação do sistema. A primeira tabela demonstra essa configuração:

● Foi definido um clock base chamado clk com uma frequência alvo de 45.0 MHz, correspondente ao clock de entrada da placa FPGA.

● Isso estabelece um requisito de tempo: todos os caminhos lógicos síncronos com este clock devem ter um atraso total inferior ao seu período, que é de 22.222 ns.

<p align="center">Figura 04 - Clocks
</p>

<p align="center">
  <img src="./imagem6.jpeg" alt="Imagem6"><br>
</p>
<p align="center"><em>Fonte: Produzido no Quartus II.</em></p>

A tabela 'Slow Model Fmax Summary' apresenta a frequência máxima de operação (f_max) calculada para o projeto. O resultado foi obtido sob o 'Slow Model', que simula as piores condições de operação (tensão mais baixa e temperatura mais alta), garantindo a robustez do projeto.
● A f_max calculada para o domínio de clock clk foi de 133.12 MHz.
