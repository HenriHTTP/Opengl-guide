# Capítulo 1. Introdução ao OpenGL
## Objetivos do Capítulo
Após ler este capítulo, você será capaz de:
- Descrever o propósito do OpenGL, o que ele pode e não pode fazer na criação de imagens geradas por computador.
- Identificar a estrutura comum de uma aplicação OpenGL.
- Enumerar os estágios de sombreamento que compõem o pipeline de renderização do OpenGL.

## Seções Principais do Capítulo
- **"O que é o OpenGL?"** explica o que é o OpenGL, o que ele faz e não faz, e como funciona.
- **"Seu Primeiro Contato com um Programa OpenGL"** oferece uma primeira visão de como é um programa OpenGL.
- **"Sintaxe do OpenGL"** descreve o formato dos nomes de comandos que o OpenGL utiliza.
- **"O Pipeline de Renderização do OpenGL"** discute o pipeline de processamento que o OpenGL utiliza na criação de imagens.
- **"Nosso Primeiro Programa: Uma Discussão Detalhada"** dissecará o primeiro programa apresentado e fornecerá mais detalhes sobre as atividades de cada seção do programa.

# O que é o OpenGL?

O OpenGL é uma Interface de Programação de Aplicações, conhecida como API, que é simplesmente uma biblioteca de software para acessar recursos em hardware gráfico. A Versão 4.5 da biblioteca OpenGL (que este texto abrange) contém mais de 500 comandos distintos que você usa para especificar os objetos, imagens e operações necessárias para produzir aplicativos de computação gráfica tridimensional interativos.

O OpenGL é projetado como uma interface simplificada e independente de hardware que pode ser implementada em muitos tipos diferentes de sistemas de hardware gráfico, ou totalmente em software (se não houver hardware gráfico presente no sistema), independente do sistema operacional ou sistema de janelas do computador. Como tal, o OpenGL não inclui funções para realizar tarefas de janelas ou processamento de entrada do usuário; em vez disso, sua aplicação precisará usar as facilidades fornecidas pelo sistema de janelas onde a aplicação será executada. Da mesma forma, o OpenGL não fornece nenhuma funcionalidade para descrever modelos de objetos tridimensionais, ou operações para ler arquivos de imagem (por exemplo, arquivos JPEG). Em vez disso, você deve construir seus objetos tridimensionais a partir de um pequeno conjunto de primitivas geométricas: pontos, linhas, triângulos e patches.

Dado que o OpenGL existe há algum tempo - foi desenvolvido pela primeira vez pela Silicon Graphics Computer Systems, com a Versão 1.0 lançada em julho de 1994 - existem muitas versões do OpenGL, bem como muitas bibliotecas de software construídas no OpenGL para simplificar o desenvolvimento de aplicativos, quer você esteja escrevendo um jogo, crianndo uma visualização para fins científicos ou médicos, ou apenas exibindo imagens. No entanto, a versão mais moderna do OpenGL difere significativamente da original. Neste livro, descrevemos como usar as versões mais recentes do OpenGL para criar esses aplicativos.

A seguir, listamos brevemente as principais operações que um aplicativo OpenGL realizará para renderizar uma imagem. 

- Especificar os dados para construir formas a partir das primitivas geométricas do OpenGL.
- Executar vários shaders para realizar cálculos nas primitivas de entrada a fim de determinar sua posição, cor e outros atributos de renderização.
- Converter a descrição matemática das primitivas de entrada em seus fragmentos associados às localizações na tela. Esse processo é chamado de rasterização. (Um fragmento no OpenGL é o que se torna um pixel, se chegar até a imagem final renderizada.)
- Finalmente, executar um fragment shader para cada um dos fragmentos gerados pela rasterização, que determinará a cor e a posição final do fragmento.
- Possivelmente realizar operações adicionais por fragmento, como determinar se o objeto do qual o fragmento foi gerado é visível, ou mesclar a cor do fragmento com a cor atual naquela localização na tela.

O OpenGL é implementado como um sistema cliente-servidor, onde a aplicação que você escreve é considerada o cliente e a implementação do OpenGL fornecida pelo fabricante do hardware gráfico do seu computador é o servidor. Em algumas implementações do OpenGL (como aquelas associadas ao sistema X Window), o cliente e o servidor podem ser executados em máquinas diferentes conectadas por uma rede. Nesses casos, o cliente emitirá os comandos do OpenGL, que serão convertidos em um protocolo específico do sistema de janelas e transmitidos para o servidor por meio da rede compartilhada, onde serão executados para produzir a imagem final.

Na maioria das implementações modernas, um acelerador gráfico de hardware é usado para implementar a maior parte do OpenGL e está integrado (mas ainda separado) do processador central do computador, ou está montado em uma placa de circuito separada e conectado à placa-mãe do computador. Em ambos os casos, é razoável pensar no cliente como sendo sua aplicação e no servidor como sendo o acelerador gráfico.


# Primeiro Programa em OpenGL

Como você pode realizar muitas coisas com o OpenGL, um programa OpenGL pode potencialmente ser grande e complicado. No entanto, a estrutura básica de todas as aplicações OpenGL geralmente é semelhante à seguinte:

1. **Inicializar o estado associado a como os objetos que devem ser renderizados.**
2. **Especificar esses objetos a serem renderizados.**

Antes de analisarmos qualquer código, vamos introduzir alguns termos gráficos comumente utilizados. **Renderização**, que já utilizamos sem definir, é o processo pelo qual um computador cria uma imagem a partir de modelos. O OpenGL é apenas um exemplo de um sistema de renderização; existem muitos outros. O OpenGL é um sistema baseado em rasterização, mas existem outros métodos para gerar imagens também, como o ray tracing, cujas técnicas estão fora do escopo deste livro. No entanto, até mesmo um sistema que usa ray tracing pode usar o OpenGL para exibir uma imagem ou calcular informações para serem usadas na criação de uma imagem.

Além disso, a flexibilidade disponível nas versões recentes do OpenGL se tornou tão grande que algoritmos como ray tracing, mapeamento de fótons, traçado de caminhos, e renderização baseada em imagens tornaram-se relativamente fáceis de implementar em hardware gráfico programável.

Nossos modelos, ou objetos - usaremos os termos de forma intercambiável - são construídos a partir de primitivas geométricas: pontos, linhas e triângulos, que são especificados por seus vértices. Outro conceito essencial para usar o OpenGL são os shaders, que são funções especiais executadas pelo hardware gráfico. A melhor maneira de pensar em shaders é como pequenos programas compilados especificamente para sua unidade de processamento gráfico (GPU). O OpenGL inclui todas as ferramentas do compilador internamente para pegar o código-fonte do seu shader e criar o código necessário para a GPU executar.

No OpenGL, existem seis estágios de shader que você pode usar. Os mais comuns são os vertex shaders, que processam dados de vértices, e os fragment shaders, que operam nos fragmentos gerados pelo rasterizador. A imagem final gerada consiste em pixels desenhados na tela; um pixel é o elemento mais pequeno visível no seu display. Os pixels no seu sistema são armazenados em um framebuffer, que é uma parte da memória que o hardware gráfico gerencia e alimenta para o dispositivo de exibição.

A [Figura 1.1](./figura.1.1.png) mostra a saída de um programa OpenGL simples, que renderiza dois triângulos azuis em uma janela. O código-fonte do exemplo completo é fornecido no [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp).

Funções especiais são executadas pelo hardware gráfico, e a melhor maneira de pensar nelas é como pequenos programas compilados especificamente para sua unidade de processamento gráfico (GPU). O OpenGL inclui todas as ferramentas do compilador internamente para pegar o código-fonte do seu shader e criar o código necessário para a GPU executar.

No OpenGL, existem seis estágios de shader que você pode usar. Os mais comuns são os vertex shaders, que processam dados de vértices, e os fragment shaders, que operam nos fragmentos gerados pelo rasterizador. A imagem final gerada consiste em pixels desenhados na tela; um pixel é o elemento mais pequeno visível no seu display. Os pixels no seu sistema são armazenados em um framebuffer, que é uma parte da memória que o hardware gráfico gerencia e alimenta para o dispositivo de exibição.

A [Figura 1.1](./figura.1.1.png) mostra a saída de um programa OpenGL simples, que renderiza dois triângulos azuis em uma janela. O código-fonte do exemplo completo é fornecido no [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp). Renderização baseada em imagens tornaram-se relativamente fáceis de implementar em hardware gráfico programável.

Embora esse código possa ser mais longo do que você estava esperando, você irá descobri que este programa será a base de praticamente todas as aplicações OpenGL que você escrever. Utilizamos algumas bibliotecas de software adicionais que não fazem oficialmente parte do OpenGL para simplificar coisas como criar uma janela ou receber entrada de mouse ou teclado, elementos que o OpenGL não possui nativamente. Também criamos algumas funções auxiliares e pequenas classes em C++ para simplificar nossos exemplos. Enquanto o OpenGL é uma biblioteca em linguagem C, todos os nossos exemplos são em C++, mas em uma forma bastante simples. Na verdade, a maior parte do C++ que usamos destina-se a implementar construções matemáticas, como vetores e matrizes.

Resumidamente, aqui está o que o [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp) faz. Explicaremos todos esses conceitos em detalhes mais adiante, então não se preocupe.

No começo do programa, incluímos os arquivos de cabeçalho (headers) necessários e declaramos algumas variáveis globais. Embora seja geralmente não seja um boa prática declarar variáveis globais em grandes aplicativos, utilizamos aqui para fins de demonstração.

A função ou método `init()` é utilizado para configurar dados para uso posterior no programa. Esses dados podem incluir informações de vértices para a renderização de primitivas ou dados de imagem para uma técnica chamada mapeamento de textura, a qual será descrita no [Capítulo 6](#).

Dentro da `init()`, primeiro especificamos as informações de posição para os dois triângulos que vão ser renderizados. Em seguida, especificamos os shaders que serão utilizados em nosso programa. Neste caso, estamos utilizando apenas os shaders de vértices e fragmentos necessários. A função ou método `LoadShaders()` é ultilizada basicamente para simplificar o processo de preparação de shaders para uma GPU. Dentro do [Capítulo 2](#), iremos aprofundar mais detalhadamente o que ela faz.

A parte final do `init()` é o que gostamos de chamar de "shader plumbing", onde você associa os dados em sua aplicação com as variáveis nos programas de shader. Isso também será descrito em detalhes no [Capítulo 2](#).

A função ou método `display()` é a responsável por realizar a renderização. Em outras palavras, ela chama as funções OpenGL que responsáveis por solicitar a renderização de algo. Quase todas as função ou método `display()` seguirão os mesmos três passos, como no nosso exemplo simples aqui:

1. Limpar a janela chamando `glClearBufferfv()`.
2. Executar as chamadas OpenGL necessárias para renderizar o objeto.
3. Solicitar que a imagem seja apresentada na tela.

Finalmente, a função `main()` realiza o trabalho pesado de criar uma janela, chamar `init()`, e finalmente entrar no loop de eventos. Aqui, você também vê funções que começam com "gl", mas parecem diferentes das outras funções na aplicação. Essas, que descreveremos em breve, são provenientes das bibliotecas que usamos para simplificar a escrita de programas OpenGL em diferentes sistemas operacionais e janelas: GLFW e GL3W.

Antes de nos aprofundarmos na descrição detalhada das rotinas, vamos explicar como o OpenGL rotula funções, constantes e outros construtos de programação úteis.

# OpenGL Syntax

Como você provavelmente percebeu, todas as funções na biblioteca OpenGL começam com as letras "`gl`", seguidas imediatamente por uma ou mais palavras em maiúsculas para nomear a função (por exemplo: `glBindVertexArray()`). Todas as funções no OpenGL seguem esse padrão. No programa, você também viu funções que começavam com "`glfw`", que pertencem à `GLFW`, uma biblioteca que abstrai o gerenciamento de janelas e outras tarefas do sistema. Da mesma forma, você observou uma única função, `gl3wInit()`, que vem da `GL3W`. Descreveremos a biblioteca GLFW com mais detalhes no [Apêndice A](#).

Semelhante à convenção de nomenclatura de funções no OpenGL, as constantes, como `GL_COLOR`, que você viu em `display()`, são definidas para a biblioteca OpenGL. Todos os tokens constantes começam com "`GL`" e usam underscores para separar palavras. Suas definições são simplesmente `#defines` encontrados nos arquivos de cabeçalho do OpenGL: `glcorearb.h` e `glext.h`.

Para facilitar a migrações de aplicações em OpenGL entre diferentes sistemas operacionais, o OpenGL também define vários tipos de dados para suas funções, como `GLfloat`, que é o tipo de valor de ponto flutuante que usamos para declarar vértices no [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp). O OpenGL define `typedefs` (tipos) para todos os tipos de dados aceitos por suas funções, os quais estão listados na [Tabela 1.1](#tabela). Além disso, como o OpenGL é uma biblioteca escrita em linguagem C, ele não possui sobrecarga (Overloard) de funções para lidar com diferentes tipos de dados; utiliza uma convenção de nomenclatura de função para organizar a variedade de funções resultantes dessa situação. Por exemplo, encontraremos uma função chamada `glUniform*()` no [Capítulo 2](#), "Fundamentos de sobre Shaders", que vem em diversas formas, como `glUniform2f()` e `glUniform3fv()`. Os sufixos no final da parte "central" do nome da função fornecem informações sobre os argumentos passados para a função. Por exemplo, o 2 em `glUniform2f()` representa que dois valores de dados serão passados para a função. (Há outros parâmetros também, mas eles são iguais em todas as 24 versões da função `glUniform*()`. Neste livro, usaremos `glUniform*()` para representar a coleção de todas as funções `glUniform*()`. Observe também o f que segue o 2. Isso indica que esses dois parâmetros são do tipo `GLfloat`. Finalmente, algumas versões dos nomes das funções terminam com um v, que é a abreviação de vetor, significando que os dois valores de ponto flutuante (no caso de `glUniform2fv()`) são passados como um array unidimensional de `GLfloats`, em vez de dois parâmetros separados.

### Tabela

| Suffix | Data Type | C-Language Type | OpenGL Type |
|---|---|---|---|
| **b** | 8-bit integer | signed char | GLbyte |
| **s** | 16-bit integer | signed short | GLshort |
| **i** | 32-bit integer | int | GLint, GLsizei |
| **f** | 32-bit floating-point | float | GLfloat, GLclampf |
| **d** | 64-bit floating-point | double | GLdouble, GLclampd |
| **ub** | 8-bit unsigned integer | unsigned char | GLubyte |
| **us** | 16-bit unsigned integer | unsigned short | GLushort |
| **ui** | 32-bit unsigned integer | unsigned int | GLuint, GLenum, GLbitfield |


# OpenGL Rendering Pipeline

O OpenGL implementa o que é comumente chamado de pipeline de renderização, que é uma sequência de estágios de processamento para converter os dados fornecidos de uma aplicação para o OpenGL transformar em uma imagem final renderizada. A [Figura 1.2](figura.1.2.png) mostra o pipeline do OpenGL associado à Versão 4.5. O pipeline do OpenGL evoluiu consideravelmente desde a sua primeira implementação em 1992.

O processo de Rendering no OpenGL começa com os dados geométricos que você fornece (vértices e primitivas geométricas) e os processa inicialmente por meio de uma sequência de estágios de shader - vertex shading, tessellation shading (que por si só pode usar dois shaders), e finalmente, shading de geometria - antes de serem passados para o rasterizador. O rasterizador irá gerar fragmentos para qualquer primitiva que esteja dentro da região de recorte e executar um fragment shader para cada um dos fragmentos gerados.

Como pode ser visto, os shaders desempenham um papel essencial na criação de aplicações OpenGL. Você tem controle total sobre quais estágios de shader são utilizados e o que cada um deles faz. Nem todos os estágios são obrigatórios; na verdade, apenas os shaders de vértices e fragmentos precisam ser incluídos. Os shaders de tesselação e geometria são opcionais.

Agora, vamos aprofundar em cada estágio para fornecer um pouco mais de contexto. Entendemos que isso pode ser um pouco avassalador neste ponto, mas continue conosco. Acontecerá que entender apenas alguns conceitos levará você muito longe com o OpenGL.

# Preparando o envio de dados no OpenGL

O OpenGL exige que todos os dados sejam armazenados em objetos de buffer, que são simplesmente blocos de memória gerenciados pelo OpenGL. O preenchimento desses buffers com dados pode ocorrer de várias maneiras, mas uma das mais comuns é especificar os dados ao mesmo tempo em que você especifica o tamanho do buffer usando o comando `glNamedBufferStorage()`, como no [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp). Há alguma configuração adicional necessária com buffers, que abordaremos no [Capítulo 3](#).


# Enviando dados no OpenGL

Após inicializarmos nossos buffers, podemos solicitar que primitivas geométricas sejam renderizadas chamando um dos comandos de desenho do OpenGL, como `glDrawArrays()`, como fizemos no [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp).

No OpenGL, desenhar geralmente significa transferir dados de vértices para o servidor OpenGL. Pense em um vértice como um conjunto de valores de dados processados em conjunto. Embora os dados no conjunto possam ser qualquer coisa que você deseje (ou seja, você define todos os dados que compõem um vértice), quase sempre incluem dados de posição. Qualquer outro dado será valores que você precisará determinar para a cor final do pixel. Os comandos de desenho são abordados em detalhes no [Capítulo 3](#), "Desenhando com OpenGL."

# Vertex Shading

Para cada vértice emitido por um comando de desenho, um vertex shader será chamado para processar os dados associados a esse vértice. Dependendo se há ou não outros shaders pré-rasterização ativos, os vertex shaders podem ser muito simples, talvez apenas copiando dados para passá-los por essa etapa de shading, o que chamamos de pass-through shader. Ou podem ser shaders muito complexos realizando muitos cálculos para potencialmente calcular a posição na tela do vértice (geralmente usando matrizes de transformação, descritas no [Capítulo 5](#)), determinar a cor do vértice usando cálculos de iluminação descritos no [Capítulo 7](#), ou qualquer variedade de outras técnicas.

Tipicamente, uma aplicação de alguma complexidade terá múltiplos vertex shaders, mas apenas um pode estar ativo a qualquer momento.

# Tessellation Shading

Após o vertex shader processar os dados associados a cada vértice, o estágio de tessellation shader continuará processando esses dados, se tiver sido ativado. Conforme veremos no [Capítulo 9](#), a tesselação usa patches para descrever a forma de um objeto e permite que coleções relativamente simples de geometria de patch sejam tesselladas para aumentar o número de primitivas geométricas, proporcionando modelos mais detalhados. O estágio de tessellation shading pode potencialmente utilizar dois shaders para manipular os dados do patch e gerar a forma final.

# Geometry Shading

O próximo estágio de shader, geometry shading, permite o processamento adicional de primitivas geométricas individuais, incluindo a criação de novas, antes da rasterização. Este estágio de shading é opcional, mas poderoso, como veremos no [Capítulo 10](#).

# Primitive Assembly

Os estágios de shading anteriores operam todos nos vértices, com as informações sobre como esses vértices estão organizados em primitivas geométricas sendo transportadas internamente pelo OpenGL. O estágio de Primitive Assembly organiza os vértices em suas primitivas geométricas associadas em preparação para a etapa de recorte e rasterização.

# Clipping

Ocasionalmente, vértices estarão fora da viewport - a região da janela onde você tem permissão para desenhar - e causarão a modificação da primitiva associada a esse vértice para que nenhum de seus pixels fique fora da viewport. Essa operação é chamada de recorte e é tratada automaticamente pelo OpenGL.

# Rasterization

Imediatamente após o recorte, as primitivas atualizadas são enviadas para o rasterizador para a geração de fragmentos. O papel do rasterizador é determinar quais localizações de tela são cobertas por uma determinada geometria (ponto, linha ou triângulo). Conhecendo essas localizações, juntamente com os dados de vértices de entrada, o rasterizador interpola linearmente os valores de dados para cada variável variável no fragment shader e envia esses valores como entradas para o seu fragment shader. Considere um fragmento como um "pixel candidato", pois os pixels têm um lugar no framebuffer, enquanto um fragmento ainda pode ser rejeitado e nunca atualizar sua localização de pixel associada. O processamento de fragmentos ocorre nos dois próximos. Embora a rasterização inicie a vida de um fragmento, e os cálculos feitos no fragment shader sejam essenciais para calcular a cor final do fragmento, não é de forma alguma todo o processamento que pode ser aplicado a um fragmento.

# Fragment Shading

A última etapa em que você tem controle programável sobre a cor de uma localização na tela é o fragment shading. Neste estágio de shader, você utiliza um shader para determinar a cor final do fragmento (embora o próximo estágio, operações por fragmento, possa modificar a cor uma última vez) e potencialmente o valor de profundidade. Os fragment shaders são muito poderosos, pois frequentemente utilizam mapeamento de textura para complementar as cores fornecidas pelos estágios de processamento de vértices. Um fragment shader também pode encerrar o processamento de um fragmento se determinar que o fragmento não deve ser desenhado; esse processo é chamado de descarte de fragmentos.

Uma maneira útil de pensar na diferença entre shaders que lidam com vértices e fragment shaders é a seguinte: vertex shading (incluindo tessellation e geometry shading) determina onde na tela uma primitiva está, enquanto fragment shading utiliza essa informação para determinar qual será a cor desse fragmento.

# Per-Fragment Operations

O processamento adicional de fragmentos, fora do que você pode fazer atualmente em um fragment shader, é o processamento final de fragmentos individuais. Durante esta etapa, a visibilidade de um fragmento é determinada usando o teste de profundidade (também conhecido como z-buffering) e o teste de stencil.

Se um fragmento passar com sucesso por todos os testes habilitados, ele pode ser gravado diretamente no framebuffer, atualizando a cor (e possivelmente o valor de profundidade) do seu pixel, ou, se a mistura estiver habilitada, a cor do fragmento será combinada com a cor atual do pixel para gerar uma nova cor que será escrita no framebuffer.

Como você viu na [Figura 1.2](figura.1.2.png), também há um caminho para dados de pixels. Geralmente, dados de pixels vêm de um arquivo de imagem, embora também possam ser criados ao renderizar usando o OpenGL. Os dados de pixels são geralmente armazenados em um mapa de textura para uso com mapeamento de textura, que permite que qualquer estágio de textura consulte valores de dados de um ou mais mapas de textura. O mapeamento de textura é abordado em detalhes no Capítulo 6.

Com essa breve introdução ao pipeline do OpenGL, vamos analisar o [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp) e mapear as operações de volta para o pipeline de renderização.

# Nosso Primeiro Programa: Uma Discussão Detalhada

Vamos dar uma olhada mais detalhada em nosso primeiro programa.

## Entrando na main()

Começando no início da execução do nosso programa, primeiro observamos o que está acontecendo na `main()`. As seis primeiras linhas utilizam o GLFW para configurar e abrir uma janela para nós. Embora os detalhes de cada uma dessas rotinas sejam abordados no Apêndice A, discutimos o fluxo dos comandos aqui.
A primeira função, `glfwInit()`, inicializa a biblioteca GLFW. Ela processa os argumentos da linha de comando fornecidos ao programa e remove qualquer um que controle como o GLFW pode operar (como especificar o tamanho de uma janela). `glfwInit()` precisa ser a primeira função do GLFW que sua aplicação chama, pois ela configura as estruturas de dados necessárias para as rotinas subsequentes do GLFW.

`glfwCreateWindow()` configura o tipo de janela que queremos usar em nossa aplicação e o tamanho da janela, como você poderia esperar. Embora não o façamos aqui, você também pode consultar o tamanho do dispositivo de exibição para dimensionar dinamicamente a janela em relação à tela do seu computador. `glfwCreateWindow()` também cria um contexto OpenGL associado a essa janela. Para começar a usar o contexto, devemos torná-lo atual, o que significa que os comandos OpenGL são direcionados para esse contexto. Uma única aplicação pode usar vários contextos e várias janelas, e o contexto atual é aquele que processa os comandos que você faz.

Continuando, a chamada para `gl3wInit()` inicializa outra biblioteca auxiliar que usamos: GL3W. GL3W simplifica o acesso a funções e outros fenômenos de programação interessantes introduzidos pelos diversos sistemas operacionais com OpenGL. Sem o GL3W, é necessário um trabalho considerável adicional para iniciar uma aplicação.

Neste ponto, estamos verdadeiramente preparados para realizar coisas interessantes com o OpenGL. A rotina `init()`, que discutiremos em breve, inicializa todos os nossos dados relevantes do OpenGL para que possamos usá-los para renderização posteriormente.

A última função em `main()` é um loop que interage com a janela e os sistemas operacionais para processar a entrada do usuário e outras operações semelhantes. É esse loop que determina se uma janela precisa ser fechada ou não (chamando `glfwWindowShouldClose()`), redesenha seu conteúdo e o apresenta ao usuário (chamando `glfwSwapBuffers()`), e verifica se há mensagens recebidas do sistema operacional (chamando `glfwPollEvents()`).

Se determinarmos que nossa janela foi fechada e que nossa aplicação deve ser encerrada, realizamos a limpeza da janela chamando `glfwDestroyWindow()` e, em seguida, desligamos a biblioteca GLFW chamando `glfwTerminate()`.

# Inicialização do OpenGL

A próxima rotina que precisamos discutir é `init()` do [Exemplo 1.1](https://github.com/openglredbook/examples/blob/master/src/01-triangles/01-triangles.cpp). Mais uma vez, aqui está o código para refrescar sua memória.

## Inicializando Nossos Objetos de Vértice-Array

Há muita coisa acontecendo nas funções e dados de `init()`. Começando do topo, começamos alocando um objeto de vértice-array chamando `glCreateVertexArrays()`. Isso faz com que o OpenGL aloque alguns nomes de objeto de array de vértices para nosso uso - no nosso caso, `NumVAOs`, que especificamos na seção de variáveis globais do código. `glCreateVertexArrays()` retorna esse número de nomes para nós no array fornecido, `VAOs` neste caso.

Aqui está uma descrição completa de `glCreateVertexArrays()`:

| Função                                | Descrição                                                                                                                                                                                                                                                                                       |
|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `void glCreateVertexArrays(GLsizei n, GLuint *arrays);` | A função OpenGL `glCreateVertexArrays()` retorna `n` nomes atualmente não utilizados para serem usados como objetos de array de vértices no array `arrays`. Esses nomes são inicializados com valores que representam o estado padrão de arrays de vértices não inicializados. No entanto, se `n` for um valor negativo, a função gera um erro `GL_INVALID_VALUE`. |**


Veremos muitos comandos do OpenGL no formato `glCreate*`, para alocar nomes para vários tipos de objetos do OpenGL. Um nome é um pouco como uma variável de tipo ponteiro em C, no sentido de que você pode alocar um objeto na memória e ter o nome referenciá-lo. Depois de ter o objeto, você pode vinculá-lo ao contexto do OpenGL para usá-lo. No nosso exemplo, vinculamos um objeto de array de vértices usando `glBindVertexArray()`.

| Função                                 | Descrição                                                                                                                                                                                                                                                                                                             |
|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `void glBindVertexArray(GLuint array);` | A função `glBindVertexArray()` no OpenGL possui duas funções importantes. Quando usamos um valor de array diferente de zero, que foi criado usando `glCreateVertexArrays()`, esse array de vértices se torna ativo. Isso significa que o OpenGL usará as configurações desse array para desenhar objetos. Se vincularmos o valor zero, o OpenGL para de usar qualquer array de vértices anteriormente ativo. Um erro `GL_INVALID_OPERATION` ocorre se o array não foi criado usando `glCreateVertexArrays()` ou se foi liberado usando `glDeleteVertexArrays()`. Em resumo, `glBindVertexArray()` ativa um array de vértices específico para desenho ou desativa todos os arrays quando vinculado a zero, e gera um erro se o array não estiver válido. |








