# LOD-of-the-Kings-The-Assembly-of-the-Rings
Arquitetura em desenvolvimento para rodar jogos e simulações em hardware fraco ou assimétrico, usando LOD agressivo, sistema de “anéis” de prioridade e múltiplas GPUs (iGPU, dGPU e placas extras) como co-processadores de cálculo, com foco inicial em Linux/openSUSE e futura expansão para ARM. Uso de geração procedural, geometria vetorial e fractais.

# LOD of the Kings: The Assembly of the Rings

Arquitetura em desenvolvimento para rodar jogos e simulações em hardware fraco ou assimétrico, usando LOD agressivo, sistema de "anéis" de prioridade, múltiplas GPUs como co-processadores de cálculo e inteligência artificial como orquestradora do sistema, com foco inicial em Linux/NeuroCortex EXO e futura expansão para ARM e computação distribuída heterogênea.

---

## 1. Visão Geral

O **LOD of the Kings: The Assembly of the Rings** nasce com uma missão clara:

> **Manter jogos e simulações jogáveis e estáveis em hardware fraco, velho ou assimétrico,  
> usando inteligência de software em vez de depender só de hardware caro.**

A ideia central gira em torno de quatro pilares:

1. **LOD (Level of Detail) agressivo e inteligente** como defesa do frametime;
2. **"Anéis" de prioridade** para separar o que é crítico do que pode esperar;
3. **Uso de placas de vídeo como co-processadores**, não só para renderizar,  
   mas também para cálculos (iGPU, dGPU secundária, GPU antiga etc.);
4. **IA como guia do sistema**, orientando decisões de LOD, prioridade e distribuição de carga em tempo real.

O ambiente alvo inicial é **Linux**, utilizando o **NeuroCortex EXO** como ambiente base para os primeiros testes e experimentos. A escolha do NeuroCortex EXO é **experimental**, servindo como plataforma de referência neste início, e pode ser revisada no futuro conforme o projeto evoluir.

---

## 2. Pilares da Arquitetura

### 2.1. LOD como Defesa do Frametime

Aqui, **LOD** não é só "baixar gráfico pra rodar melhor". Ele é tratado como um **escudo de defesa** do tempo de frame.

- **LOD guiado por frametime e gargalo:**
  - considera o tempo de frame atual e recente,
  - observa se a tendência está piorando,
  - tenta inferir se o gargalo é CPU, GPU ou ambos.

- **Culling agressivo e contextual:**
  - elementos pouco relevantes pro gameplay imediato podem:
    - ser ocultados,
    - ter a complexidade reduzida,
    - ter a frequência de atualização diminuída.

- **Transições de LOD suaves (quando possível):**
  - a ideia é evitar "pop-in" brusco,
  - degradando em etapas dentro de uma margem de segurança de desempenho.

> Se o sistema prevê dor no próximo frame, o LOD entra em ação antes do stutter.

---

### 2.2. A Assembleia dos Anéis (Prioridade de Tarefas)

Inspirado na ideia de anéis de privilégio da CPU, o projeto organiza o trabalho em **"Anéis de Execução"**:

- **Inner Rings (alta prioridade):**
  - física e posição do jogador,
  - input e resposta imediata,
  - componentes essenciais do frame atual.

- **Outer Rings (baixa prioridade):**
  - IA de NPCs distantes,
  - simulações de mundo que podem atrasar alguns frames,
  - atualizações que não afetam imediatamente a sensação do jogador.

A "Assembleia dos Anéis" é o scheduler conceitual que decide:

- o que precisa rodar agora,
- o que pode ser atrasado,
- o que pode ser empurrado para outro hardware (GPU auxiliar, núcleo ocioso etc.).

---

### 2.3. Placas de Vídeo como Co-processadores

Um dos pontos centrais da arquitetura é **tratar GPUs como co-processadores de cálculo**,  
não apenas como dispositivos de vídeo.

Isso inclui:

- **GPU principal (dGPU):**
  - continua responsável pela renderização,
  - mas pode assumir tarefas paralelas em janelas de ociosidade dentro do mesmo frame.

- **iGPU e GPUs extras (placas antigas ou secundárias):**
  - atuam como **subcalculadoras dedicadas**, recebendo tarefas como:
    - física não crítica,
    - pathfinding,
    - geração procedural,
    - pré-processamento de dados para o próximo frame,
  - muitas vezes **sem renderizar vídeo**, apenas computando.

> Se existe silício parado na máquina, o projeto tenta achar cálculo útil pra ele.

---

### 2.4. IA como Guia do Sistema

A inteligência artificial entra aqui não como feature de gameplay, mas como **cérebro de decisão da própria arquitetura**:

- **Observar o comportamento do sistema em tempo real:**
  - frametime, temperatura, uso de CPU e GPU,
  - padrões de carga por tipo de cena ou situação.

- **Tomar decisões proativas:**
  - quando acionar LOD mais agressivo,
  - qual anel de prioridade deve ser comprimido,
  - qual tarefa pode ser deslocada para um co-processador disponível.

- **Aprender com o hardware disponível:**
  - adaptar-se ao perfil específico da máquina do usuário,
  - descobrir o que roda melhor em cada GPU ou núcleo disponível.

A ideia é que a IA funcione como um **maestro invisível**, ajustando continuamente o que o sistema faz sem que o jogador precise configurar nada manualmente.

---

## 3. Computação Distribuída e Hardware Abandonado

Uma das visões mais ambiciosas do projeto é a ideia de **aproveitar hardware ocioso ao redor do jogador**, inspirada em abordagens de computação distribuída heterogênea.

A premissa é simples:

> **Muita gente tem um setup velho parado, um celular antigo na gaveta,  
> uma placa de vídeo abandonada, ou até um pendrive com capacidade ociosa.  
> Por que não colocar isso pra trabalhar?**

### O que isso significa na prática (visão em estudo)

- **Outros computadores na rede local:**
  - máquinas antigas ou secundárias podem assumir tarefas de cálculo (física, IA de NPCs, pathfinding),
  - funcionando como "nós de processamento" sem precisar rodar o jogo completo,
  - apenas recebendo tarefas, calculando e devolvendo resultados.

- **Celulares e dispositivos ARM:**
  - com conectividade e poder de processamento crescente,
  - podem participar como nós leves de cálculo,
  - ou como terminais de controle e monitoramento.

- **GPUs antigas e placas dedicadas ociosas:**
  - mesmo placas velhas têm capacidade de compute via OpenCL ou Vulkan Compute,
  - podem ser ativadas como co-processadores mesmo em máquinas separadas.

- **Pendrives e armazenamento distribuído:**
  - uso para streaming de assets, cache distribuído ou dados procedurais,
  - aliviando RAM e disco da máquina principal.

> **Importante:** tudo isso é uma visão de longo prazo em estudo, não um compromisso imediato.  
> O foco atual é explorar o hardware local da máquina principal, com computação distribuída  
> como horizonte futuro natural do projeto.

---

## 4. Aplicações Alvo

O conceito do LOD of the Kings é pensado para cenários concretos, como:

- **FPS (First-Person Shooters):**
  - priorizar input e resposta do jogador,
  - reduzir detalhe de elementos periféricos em troca de estabilidade de frametime,
  - lidar com cenas cheias de NPCs, partículas e física em hardware fraco.

- **Jogos de corrida:**
  - foco em fluidez constante e sensação de velocidade,
  - LOD dinâmico para cenário distante, público e detalhes do ambiente,
  - uso de GPUs extras para simular IA de oponentes, clima ou física de pista em paralelo.

- **Integração com engines e ferramentas existentes:**
  - **Cube 2 (Sauerbraten)** como referência de FPS leve em Linux, útil para validar ideias em hardware simples;
  - **Godot 4.x (Vulkan)** como alvo natural para protótipos, pela abertura do código e suporte a Linux.

---

## 5. Jogo Interno de Teste

Como parte do projeto, está prevista a criação de um **jogo interno de teste**, um FPS pensado especificamente para validar, em cenário real, os conceitos de:

- LOD agressivo;
- anéis de prioridade;
- co-processamento com múltiplas GPUs;
- IA coordenando carga entre CPU, GPU(s) e, futuramente, outros dispositivos.

Características planejadas desse jogo de teste:

- **FPS com vários modos de jogo**, incluindo:
  - modos competitivos e cooperativos,
  - variações com muitos bots/NPCs para estressar CPU e GPU,
  - cenários abertos e fechados para testar padrões de carga diferentes.

- **Suporte a múltiplas plataformas (visão):**
  - jogável em **computador** (desktop Linux),
  - com a intenção de permitir também jogar a partir de **celular** (via ARM, cliente leve, streaming ou outra abordagem a ser estudada).

Esse FPS interno não é "o produto final", mas um **laboratório jogável** para medir, comparar e ajustar a arquitetura em situações reais.

---

## 6. Ambiente Alvo Inicial

### 6.1. Foco inicial em Linux + NeuroCortex EXO

O projeto pretende **começar os testes e experimentos em Linux**, utilizando o **NeuroCortex EXO** como ambiente base de referência, por motivos como:

- fornece um ecossistema já preparado para desenvolvimento e experimentação;
- facilita padronizar o ambiente de teste no início do projeto;
- se apoia em uma base consolidada derivada do universo openSUSE, sem precisar reinventar tudo.

> **Importante:** o uso do NeuroCortex EXO é **experimental e não definitivo**.  
> No futuro, a arquitetura pode:
> - migrar para outros ambientes Linux,
> - suportar múltiplas distros,
> - ou adotar uma base diferente, conforme os testes mostrarem o que faz mais sentido.

### 6.2. ARM, celular e computação distribuída (menção de relance)

A longo prazo, o projeto considera:

- **portabilidade para ARM**;
- uso de **dispositivos ARM (como celulares)** como nós de processamento ou clientes de jogo;
- participação de outros computadores e hardware “abandonado” como co-processadores em rede local.

No momento, isso é uma **visão em estudo**, não um compromisso imediato.

---

## 7. Estado Atual do Projeto

Neste momento, o repositório é:

- uma **especificação em construção**;
- um registro público da visão e dos conceitos;
- a base para futuros protótipos e testes em hardware real.

> Tudo aqui pode ser revisado, melhorado ou alterado conforme as ideias amadurecem  
> e os experimentos mostram o que funciona melhor.

---

## 8. Licença

Este projeto está licenciado sob a **MIT License**.  
Veja o arquivo `LICENSE` no repositório para mais detalhes.
 "Este projeto é disponibilizado sob a licença MIT para uso não comercial, acadêmico e pessoal. Para uso comercial por empresas, é necessária uma licença comercial. Entre em contato para detalhes."
---

## 9. Contribuições

Se você se interessa por:

- otimização em hardware fraco ou legado;
- uso de múltiplas GPUs como co-processadores;
- computação distribuída e hardware ocioso;
- Linux (especialmente via NeuroCortex EXO);
- FPS, jogos de corrida, Godot, ARM ou celular como parte do ecossistema;

acompanhe o repositório, abra issues com ideias, dúvidas, críticas e relatos de testes.  
A documentação e a arquitetura são **documentos vivos**, e a comunidade faz parte dessa construção.
