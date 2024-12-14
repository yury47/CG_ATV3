# (4) Olha o sapo 2 - 20/12/2024
## Discentes
- Karl Eloy Marques Henrique

# Introdução
O objetivo principal do aplicativo é permitir que o usuário explore e modifique um objeto 3D de forma interativa. O controle da rotação e iluminação do objeto oferece uma visualização completa de suas características intrínsecas, enquanto o controle das propriedades da luz e do material, permite ao usuário ajustar a aparência estética do objeto de acordo com suas preferências.

A ideia por trás da concepção deste aplicativo é a criação de um catálogo de espécies que poderia ser utilizado em um contexto educacional, especialmente em aulas de biologia ou alguma disciplina que demande um detalhamento mais profundo de características de espécimes. O aplicativo permite que diferentes modelos de animais ou plantas sejam visualizados em 3D, que podem ser alternados dinamicamente, possibilitando uma análise detalhada das características desses organismos. A capacidade de modificar propriedades do modelo também pode ser útil para destacar características específicas, como diferentes partes do corpo ou variabilidade genética, proporcionando uma experiência de aprendizagem interativa e visual em tempo real.

## Funcionalidades Principais
Permite a aplicação de texturas personalizadas (“mapeamento de imagem”) aos modelos, carregadas a partir de arquivos no formato .jpg ou .png. O controle de iluminação possibilita o ajuste das propriedades de intensidade da luz ambiental (Ia), difusa (Id) e especular (Is). As propriedades dos materiais são feitas através do ajuste dinâmico das propriedades ka (reflexão ambiental), kd (reflexão difusa) e ks (reflexão especular) de cada modelo. Controle do expoente especular, que define o espalhamento do brilho especular. A implementação do um modelo de iluminação foi baseada no modelo de Blinn-Phong, com controles detalhados sobre os componentes especulares e difusos. Proporciona interatividade avançada permitindo ao usuário rotacionar os modelos em todas as direções utilizando uma interface TrackBall, que permite ao usuário arrastar o mouse sobre a tela para girar o objeto nas direções desejadas, e fazer o ajuste dinâmico das propriedades baseadas na intensidade dos valores RGB. Além disso permite alternância entre objetos, permitindo alternar entre eles para comparações e explorações visuais.

# Guia de Utilização
A interação com o aplicativo é simples e intuitiva, permitindo uma experiência fluida para usuários de todos os níveis. 

## Ajuste de Propriedades de Materiais e Luzes

É possível alterar os valores dos componentes RGB das constantes ka, kd, ks, Ia, Id e Is. Os ajustes podem ser feitos por meio de uma paleta de cores, clicando no quadrado de cor ao lado da constante que se deseja alterar, ou alterando diretamente os valores de cada componente RGB individualmenteo que pode ser feito de duas formas; clicando em cima da componente que se deseja alterar a arrastando o mouse para a direita ou esquerda, ou clicando duas vezes sobre a componente e digitar um valor específico. 

## Constante de Espalhamento Especular:
A constante que define o espalhamento do brilho especular pode ser aumentada ou diminuída diretamente pelo usuário.   

## Rotação do Modelo e do Ponto de Luz
O objeto 3D pode ser rotacionado em todas as direções utilizando o mouse. Use o botão esquerdo do mouse para rotacionar o modelo em qualquer direção. A rotação é suave e precisa, proporcionando uma experiência de visualização imersiva. O ponto de iluminação também pode ser alterado usando o botão direito do mouse para ajustar a posição da luz em relação ao modelo.

## Alteração entre Objetos
Utilize o botão “Load 3D Model” para carregar diferentes arquivos .obj, que correspondem a diferentes espécies que se deseja estudar durante a execução do aplicativo.

Essas funcionalidades permitem explorar e manipular os modelos e suas características de forma interativa e visualmente rica.

# Vídeo de demonstração
Para ver o aplicativo funcionando, [clique aqui](https://drive.google.com/file/d/1OGZeqfUua4DHcL3dYxXEdSG1qNi08lK4/view?usp=drive_link).

# Rodando em WebAssembly
Os binários WebAssembly foram compilados e estão disponíveis na pasta **public** deste repositório. 
Criamos um site do **GitHub Pages** para permitir o acesso direto ao aplicativo.
Basta entrar no link [Olha_o_sapo](https://karleloy.github.io/Atividade2CG-Olha_o_sapo/public/Olha_o_sapo.html).

# Projeto e Desenvolvimento
O projeto foi desenvolvido com foco em atender aos requisitos da atividade, como os que seguem.

## Uso de Gráficos 3D com Primitivas do OpenGL
A renderização dos modelos 3D é feita utilizando primitivas do OpenGL, com suporte a mapeamento de texturas e aplicação de iluminação. Para carregar uma textura no OpenGL, primeiro chamamos a função `glGenTextures`, que cria um ou mais recursos de textura. Em seguida, ligamos o recurso de textura a um “alvo de textura”, como `GL_TEXTURE_2D` para texturas 2D. A textura é então preenchida com um mapa de bits por meio da função `glTexImage2D`. A função considera que cada pixel é uma tupla de valores RGBA (GL_RGBA), e que cada componente de cor é um byte sem sinal (`GL_UNSIGNED_BYTE`). No contexto da biblioteca ABCg, utilizamos a função auxiliar `abcg::loadOpenGLTexture`, que simplifica o processo de carregamento de texturas. Essa função recebe uma estrutura `abcg::OpenGLTextureCreateInfo` com informações sobre o arquivo de imagem. As funções descritas nesta seção podem ser encontradas nos arquivos **model.cpp** e **window.cpp**.

## Interação e Animação
A interação com o modelo 3D na aplicação é implementada por meio de um trackball virtual, permitindo rotações suaves e naturais em todas as direções. Essa funcionalidade é baseada em uma projeção do movimento do cursor na superfície de uma esfera virtual, que representa o espaço 3D da cena. Detalhes da implementação podem ser observados em **trackball.cpp**. O método `mouseMove` rastreia os movimentos do cursor do mouse, projetando sua posição em um hemisfério unitário para calcular a rotação do modelo. Essa rotação é definida por um eixo obtido pelo produto vetorial das posições projetadas e por um ângulo proporcional à magnitude do movimento. Pelo método `mousePress` quando o botão do mouse é pressionado, o rastreamento é ativado, armazenando a posição inicial projetada e reiniciando o temporizador. Esse estado é usado para determinar a interação ativa do usuário. Ao soltar o botão, o rastreamento é interrompido, mas a rotação continua de forma suave, utilizando a inércia calculada. Isso cria um efeito de continuidade que melhora a experiência de manipulação. Por `getRotation` permitimos que quando o mouse não está sendo rastreado, o movimento de rotação é mantido utilizando a velocidade angular acumulada. Esse mecanismo simula o comportamento de inércia, proporcionando uma transição fluida e natural para a rotação.

## Iluminação
A aplicação utiliza o modelo de iluminação Blinn-Phong, que é uma extensão do modelo Phong clássico. Este modelo equilibra precisão e eficiência ao calcular a iluminação, tornando-o ideal para gráficos em tempo real. O modelo é implementado por meio de shaders customizados, escritos em GLSL, permitindo controle detalhado das propriedades de luz e material para criar efeitos visuais realistas.\
O modelo Blinn-Phong combina três componentes principais para calcular a cor final de cada fragmento:
- Iluminação Ambiente (Ambient): Representa luz difusa presente em todos os pontos da cena, independentemente da posição da luz ou orientação do objeto.
- Iluminação Difusa (Diffuse): Representa a luz refletida em todas as direções a partir de uma superfície, proporcional ao ângulo de incidência entre a luz e a superfície.
- Iluminação Especular (Specular): Representa os reflexos brilhantes que aparecem nas superfícies, calculados com base no ângulo entre o vetor de visão e o vetor de reflexão.
\\
A iluminação é ajustada por três constantes (Ka, Kd, Ks), representando as componentes ambiente, difusa e especular do material, além do parâmetro `shininess`, que define o tamanho e intensidade do brilho especular.

## Texturização
Suporte ao carregamento de texturas externas e aplicação nos modelos por mapeamento UV. Um arquivo OBJ pode vir acompanhado de um arquivo .mtl opcional que contém a descrição das propriedades dos materiais de cada objeto. Ele inclui valores como o expoente especular (Ns), propriedades de reflexão ambiente (Ka), difusa (Kd) e especular (Ks), além do nome do mapa de textura que deve ser utilizado (e.g., map_Kd). Nossa implementação de `Model::loadObj`, que utiliza funções da biblioteca **TinyObjLoader**, carrega automaticamente a textura difusa, se ela existir. No método `Model::render`, chamado em `Window::onPaint`, a ativação da textura no pipeline de renderização é realizada. Após a ativação da unidade de textura, a função `glBindTexture` é chamada para associar o identificador de textura à unidade recém ativada. Essa implementação está localizada no arquivo **model.cpp**.

## Problemas e Soluções Encontradas
Algumas texturas não ficaram alinhadas corretamente, apresentando distorção devido a problemas no mapeamento UV. Como solução, ajustamos os algoritmos de carregamento para garantir que as coordenadas UV fossem corretamente interpretadas do arquivo OBJ.
Outro problema ocorreu quando objetos foram carregados sem a textura correta. Durante a inicialização, alguns modelos carregados apareciam sem a textura associada, mesmo com o arquivo .mtl disponível. Para corrigir alteramos a implementação de `Model::loadObj` para validar se a textura estava carregada corretamente antes de ativar o pipeline de renderização. Também adicionamos verificações para recarregar texturas ausentes dinamicamente.
Além disso foi necessário ajuste do posicionamento inicial pois os modelos carregados não estavam bem posicionados na janela, dificultando a visualização inicial. Foi necessário o ajuste de coordenadas para garantir que todos os objetos carregados ficassem melhor posicionados.

# Arquivos

A renderização gráfica do aplicativo é realizada utilizando OpenGL, uma das principais bibliotecas para gráficos 3D em tempo real, que garante alto desempenho e flexibilidade. Para simplificar a integração com SDL e ImGui, foi utilizada a biblioteca abcgOpenGL, que facilita a manipulação de eventos de entrada e a criação de interfaces gráficas. Isso permite uma experiência interativa fluida e responsiva para o usuário, com controles intuitivos para manipulação do modelo 3D e alteração das cores.

Consulte a pasta **Olha_o_sapo** deste repositório para obter os arquivos do projeto.
O programa é baseado nos seguintes arquivos:

**main.cpp**\
Este arquivo contém o ponto de entrada principal do aplicativo de visualização de modelos 3D. Cria uma instância da classe `abcg::Application`, que gerencia o ciclo de vida da aplicação OpenGL. Cria uma instância da classe Window, que define o comportamento da janela e a renderização dos modelos 3D. Define as configurações de OpenGL, como a quantidade de multisampling (samples = 4), que melhora a qualidade visual do modelo e as configurações da janela. Inicia o loop principal da aplicação, que gerencia a renderização, interação com o usuário e eventos da janela. Em caso de erro durante a execução, a aplicação captura exceções do tipo `std::exception` e imprime a mensagem de erro no terminal.

**model.cpp**\
Contém a implementação das principais funcionalidades relacionadas ao carregamento, manipulação e renderização de modelos 3D no aplicativo. Cria os buffers necessários para armazenar os dados do modelo na GPU: Vertex Buffer Object (VBO) e Element Buffer Object (EBO). Carrega um modelo 3D no formato .obj a partir do caminho fornecido com `loadObj(std::string_view path, bool standardize)`. Utiliza a biblioteca `tinyobj::ObjReader` para ler o arquivo. Armazena os vértices únicos e cria uma lista de índices para eliminar duplicação de vértices. Se a opção standardize for verdadeira, centraliza o modelo na origem e normaliza o tamanho para se ajustar ao espaço [-1, 1]. Renderiza o modelo 3D usando OpenGL com `render(int numTriangles) const`; determina o número de triângulos a serem desenhados e executa o comando `glDrawElements` para renderizar os triângulos armazenados no EBO. Configura o Vertex Array Object (VAO), que define como os vértices são processados: inicializa a posição e rotação do modelo, habilita o buffer de profundidade (depth buffering), necessário para a renderização 3D, vincula os atributos dos vértices (posição) ao VAO e configura a matriz de transformação do modelo e as variáveis uniformes relacionadas à projeção, cor e visualização. Desenha o modelo 3D na tela com `paint(glm::ivec2 m_viewportSize, GLuint program)`. Limpa o buffer de cor e o buffer de profundidade. Configura a matriz de transformação do modelo com translação, rotação e escala. Atualiza as cores do modelo com `update(float red, float green, float blue)` recebendo os valores RGB e os armazenando como variáveis internas, permitindo a alteração da cor do modelo durante a execução.

**model.hpp**\
Define a estrutura de dados e as funções para carregar, manipular e renderizar modelos 3D no aplicativo. Ele encapsula o gerenciamento de vértices, índices e buffers OpenGL necessários para a renderização eficiente dos objetos. A estrutura **Vertex** `glm::vec3 position:` define a posição 3D de um vértice no espaço. Carrega um arquivo de modelo 3D no formato .obj com `void loadObj(std::string_view path, bool standardize = true)`. Opcionalmente, o modelo pode ser padronizado para centralizar e normalizar suas dimensões. `void update(float red, float green, float blue)` atualiza os valores RGB para modificar dinamicamente a cor do modelo. Realiza a pintura do modelo no contexto OpenGL, configurando a matriz de modelo e aplicando transformações com `void paint(glm::ivec2 m_viewportSize, GLuint program)`. 

**trackball.cpp**\
 Implementa a classe TrackBall, que gerencia a interação de um trackball virtual para a manipulação de objetos 3D através de movimentos do mouse. Atualiza a rotação do trackball com base na posição atual do mouse com `void mouseMove(glm::ivec2 const &position)`. `project(position):` projeta a posição do mouse em coordenadas normalizadas (NDC) e ajusta para uma esfera virtual. Calcula o eixo de rotação com o produto vetorial entre a posição anterior e a atual. Ajusta a velocidade angular (m_velocity) e a aplica à rotação do objeto e concatena a nova rotação à rotação existente. `void mousePress(glm::ivec2 const &position)` inicia o rastreamento do movimento do mouse. `void mouseRelease(glm::ivec2 const &position)`finaliza o rastreamento do mouse.

**trackball.hpp**\
Gerencia a rotação de objetos 3D através da interação com o mouse. `void mouseMove(glm::ivec2 const &position)` atualiza a rotação do objeto com base na posição atual do mouse. `void mousePress(glm::ivec2 const &position)` inicia o rastreamento do movimento do mouse e registra a posição inicial. `void mouseRelease(glm::ivec2 const &position)` finaliza o rastreamento do mouse e calcula a rotação final. `void resizeViewport(glm::ivec2 const &size)` atualiza o tamanho do viewport para conversão precisa das coordenadas do mouse. `glm::mat4 getRotation() const` retorna a matriz de rotação acumulada do objeto e inclui uma simulação de inércia quando o mouse não está sendo rastreado. `void setAxis(glm::vec3 const axis) noexcept` define o eixo de rotação manualmente. `void setVelocity(float const velocity) noexcept` define a velocidade angular manualmente. Projeta a posição do mouse em uma esfera unitária virtual, simulando a rotação tridimensional `glm::vec3 project(glm::vec2 const &mousePosition) const`.

**window.cpp**\
Gerencia a janela principal da aplicação e a renderização dos modelos 3D. Ele utiliza OpenGL para a renderização gráfica e ImGui para a interface do usuário, permitindo a interação com o objeto exibido através de eventos de mouse e botões de interface. Gerencia os eventos de entrada do usuário, como movimentação do mouse, cliques e rolagem com `void Window::onEvent(SDL_Event const &event)
`. Em `void Window::onCreate()` configura o ambiente OpenGL, carregando shaders e inicializando o modelo 3D definindo a cor de limpeza do buffer de tela como preto, ativa o teste de profundidade para garantir a renderização correta dos objetos 3D, criando o programa OpenGL com os shaders de vértice e fragmento (depth.vert e depth.frag). Além disso carrega o modelo 3D (frog.obj) e configura seu VAO (Vertex Array Object) e armazena o número de triângulos a serem desenhados. `void Window::onUpdate()` atualiza a matriz de modelo e a matriz de visualização a cada frame, incrementando a rotação contínua do modelo e atualizando suas cores. `void Window::onPaint()` renderiza o modelo 3D na tela. `void Window::onPaintUI()` desenha a interface gráfica usando ImGui para controle das cores RGB do modelo, permite alterar a projeção entre perspectiva e ortogonal, cria botões para aumentar e diminuir os valores das componentes vermelha, verde e azul do modelo e atualiza as cores do modelo em tempo real conforme os botões são pressionados.

**window.hpp**\
Este arquivo contém a declaração da classe `Window`, que herda da classe base `abcg::OpenGLWindow`. A classe Window é responsável por gerenciar os principais eventos e a lógica da aplicação, incluindo:
- Manipulação de eventos do mouse e redimensionamento da janela.
- Configuração e atualização de shaders OpenGL.
- Renderização de um modelo 3D.
- Controle de interface gráfica usando ImGui para alterar as cores do modelo.

Com `void onPaintUI() override` renderiza a interface gráfica (UI) usando ImGui para controle das cores RGB. A classe Window é o ponto central de controle da aplicação, interagindo com o usuário através de eventos de entrada (mouse) e interface gráfica, e manipulando a renderização 3D do modelo carregado.

**depth.frag**\
Este arquivo define o Fragment Shader da aplicação, responsável por determinar a cor final de cada fragmento (pixel) renderizado na tela. `void main()` é função principal do shader que calcula a cor final do fragmento. `if (gl_FrontFacing)` verifica se o fragmento pertence à face frontal do triângulo renderizado. Se for face frontal, a cor do fragmento é definida diretamente como fragColor. Se for face traseira, a cor do fragmento é alterada para uma tonalidade avermelhada, reduzindo o valor do componente vermelho à metade, enquanto os outros componentes são configurados para zero.

**depth.vert**\
Este arquivo define o Vertex Shader da aplicação, responsável por transformar as coordenadas dos vértices do modelo de um espaço de objeto para o espaço de tela e calcular as propriedades de cor e profundidade dos vértices. Função principal do shader realiza a transformação e o cálculo de cor do vértice:

- Em `vec4 posEyeSpace = viewMatrix * modelMatrix * vec4(inPosition, 1);` a posição do vértice é transformada para o espaço de visão (olho), utilizando a multiplicação da matriz de modelo e a matriz de visualização.
- Em `float i = 1.0 - (-posEyeSpace.z / 3.0);` a profundidade do vértice no espaço de visão é calculada a partir da coordenada Z do vértice (posEyeSpace.z). Esse valor é utilizado para ajustar a intensidade de cor, criando um efeito de profundidade.
- Em `fragColor = vec4(i, i, i, 1) * color;` a cor final do vértice é calculada, ajustando o componente de intensidade i com base na profundidade. Isso cria um efeito visual onde objetos mais distantes se tornam mais escuros.
- Em `gl_Position = projMatrix * posEyeSpace;` a posição do vértice é transformada para o espaço de projeção, preparando o vértice para a renderização na tela.

Este shader aplica um efeito de escurecimento baseado na profundidade. Vértices mais próximos da câmera têm uma cor mais clara, enquanto vértices mais distantes tornam-se progressivamente mais escuros.
