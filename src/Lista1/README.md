### 2. O que são primitivas gráficas? Como fazemos o armazenamento dos vértices na OpenGL?

**Primitivas Gráficas** são os elementos básicos de desenho que a GPU consegue renderizar diretamente. São formas geométricas fundamentais que servem como blocos de construção para objetos mais complexos.

**Principais tipos de primitivas na OpenGL:**
- **GL_POINTS**: Pontos individuais
- **GL_LINES**: Linhas entre pares de vértices
- **GL_LINE_STRIP**: Linhas conectadas sequencialmente
- **GL_LINE_LOOP**: Linhas conectadas em loop fechado
- **GL_TRIANGLES**: Triângulos independentes (mais comum)
- **GL_TRIANGLE_STRIP**: Triângulos conectados em tira
- **GL_TRIANGLE_FAN**: Triângulos em leque

**Armazenamento de vértices na OpenGL:**

1. **Na CPU (aplicação)**:
   ```cpp
   float vertices[] = {
       // posições      // cores
       -0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,  // vértice 0
        0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,  // vértice 1
        0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f   // vértice 2
   };
   ```

2. **Na GPU (VBO - Vertex Buffer Object)**:
   ```cpp
   GLuint VBO;
   glGenBuffers(1, &VBO);
   glBindBuffer(GL_ARRAY_BUFFER, VBO);
   glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
   ```

### 3. Explique o que é VBO, VAO e EBO, e como se relacionam

**VBO (Vertex Buffer Object)**:
- **Definição**: Buffer que armazena dados dos vértices na memória da GPU
- **Função**: Transfere grandes quantidades de dados de vértices da CPU para GPU de uma só vez
- **Conteúdo**: Posições, cores, normais, coordenadas de textura, etc.
- **Vantagem**: Acesso muito mais rápido que transferir dados a cada frame

**VAO (Vertex Array Object)**:
- **Definição**: Objeto que armazena configurações de como interpretar os dados dos VBOs
- **Função**: "Lembra" como os atributos dos vértices estão organizados
- **Conteúdo**: Ponteiros para VBOs, layout dos atributos, habilitação de atributos
- **Vantagem**: Permite trocar entre diferentes configurações de vértices rapidamente

**EBO (Element Buffer Object) / IBO (Index Buffer Object)**:
- **Definição**: Buffer que armazena índices dos vértices para reutilização
- **Função**: Evita duplicação de vértices compartilhados entre primitivas
- **Exemplo**: Para um quadrado, ao invés de 6 vértices (2 triângulos), usa 4 vértices + 6 índices
- **Vantagem**: Economiza memória e largura de banda

**Relação entre VBO, VAO e EBO:**

```
VAO (contém configurações)
├── VBO #1 (posições dos vértices)
├── VBO #2 (cores dos vértices)  
├── VBO #3 (coordenadas de textura)
└── EBO (índices para reutilizar vértices)
```

**Fluxo de uso:**
1. **Criar VAO**: `glGenVertexArrays()` e `glBindVertexArray()`
2. **Criar e preencher VBO**: `glGenBuffers()`, `glBindBuffer()`, `glBufferData()`
3. **Configurar atributos**: `glVertexAttribPointer()`, `glEnableVertexAttribArray()`
4. **Opcional - Criar EBO**: Para reutilizar vértices
5. **Renderizar**: `glBindVertexArray()` + `glDrawArrays()` ou `glDrawElements()`

### 4. Análise do código fonte do projeto Hello Triangle

**No HelloTriangle.cpp, os conceitos estão organizados da seguinte forma:**

**Shaders:**
```cpp
// Vertex Shader - processa cada vértice
const GLchar *vertexShaderSource = R"(
 #version 400
 layout (location = 0) in vec3 position;
 void main() {
     gl_Position = vec4(position.x, position.y, position.z, 1.0);
 })";

// Fragment Shader - processa cada pixel
const GLchar *fragmentShaderSource = R"(
 #version 400
 uniform vec4 inputColor;
 out vec4 color;
 void main() {
     color = inputColor;
 })";
```

**VBO (na função setupGeometry()):**
```cpp
// Dados dos vértices na CPU
float vertices[] = { /* coordenadas dos vértices */ };

// Criação e preenchimento do VBO na GPU
GLuint VBO;
glGenBuffers(1, &VBO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

**VAO (na função setupGeometry()):**
```cpp
// Criação do VAO
GLuint VAO;
glGenVertexArrays(1, &VAO);
glBindVertexArray(VAO);

// Configuração dos atributos dos vértices
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```

**Renderização (no loop principal):**
```cpp
// Ativação do programa de shader
glUseProgram(shaderProgram);

// Binding do VAO (que "lembra" toda configuração)
glBindVertexArray(VAO);

// Desenho das primitivas
glDrawArrays(GL_TRIANGLES, 0, 3);
```

**Relação entre os conceitos:**
1. **Shaders** definem COMO processar os dados
2. **VBO** armazena OS DADOS dos vértices na GPU
3. **VAO** configura COMO INTERPRETAR os dados do VBO
4. **Pipeline**: VAO → Vertex Shader → Rasterização → Fragment Shader → Tela

## Respostas (continuação) — Questões 5 a 10

### 5) Desenhe 2 triângulos: a) preenchido b) contorno c) pontos d) juntos.

- a) Preenchido: usar GL_TRIANGLES com vértices formando dois triângulos.
- b) Contorno: usar GL_LINE_LOOP (ou GL_LINE_STRIP) para desenhar os contornos dos mesmos vértices.
- c) Pontos: usar GL_POINTS; ajustar `glPointSize()` para visualização.
- d) Juntos: pode-se enviar os mesmos vértices em buffers e fazer múltiplos draw calls com primitivas diferentes, ou desenhar em um único VBO/VAO e chamar `glDrawArrays` repetidas vezes com modos diferentes. Exemplo de fluxo:

1. Criar VBO com a lista de vértices (posições e cores).
2. Criar VAO e configurar atributos.
3. No loop de render:
    - glPolygonMode(GL_FRONT_AND_BACK, GL_FILL); glDrawArrays(GL_TRIANGLES,...);
    - glPolygonMode(GL_FRONT_AND_BACK, GL_LINE); glDrawArrays(GL_TRIANGLES,...);
    - glDrawArrays(GL_POINTS,...);

Obs: usar `glEnable(GL_PROGRAM_POINT_SIZE)` e controlar `gl_PointSize` no vertex shader se preferir pontos por vértice.

### 6) Desenhe um círculo por parametrização. Depois: a) octógono b) pentágono c) pac-man d) fatia de pizza e) estrela (desafio).

- Círculo paramétrico: para N segmentos, para i=0..N-1: theta = 2*pi*i/N; x = cx + r*cos(theta); y = cy + r*sin(theta). Use GL_TRIANGLE_FAN (centro + anel) para preencher.
- Octógono/Pentágono: usar o mesmo método com N=8 ou N=5.
- Pac-man: desenhar um setor (ângulo menor que 2*pi) para o corpo e um triângulo faltante (boca). Controle o ângulo inicial/final para abrir a boca.
- Fatia de pizza: desenhar um setor (triangle fan entre centro e dois limites angulares).
- Estrela: combinar vértices em duas raias (interna/externa) alternadas; pode usar GL_TRIANGLES com índices para formar a estrela. Para estrela de 5 pontas: calcular 10 vértices alternando raio interno/externo e desenhar com EBO.

### 7) Desenhe uma espiral.

- Parametrização básica (espiral logarítmica ou aritmética): r = a + b * theta (ou r = a * e^{b*theta}). Converter para cartesiano: x = cx + r*cos(theta), y = cy + r*sin(theta). Incrementar theta em pequenos passos e desenhar como linha (GL_LINE_STRIP) ou série de triângulos para espessura.

### 8) Dado um triângulo P1/P2/P3 com cores RGB: a) buffers (VBO/VAO/EBO) b) atributos no vertex shader e implemente.

- a) Buffers:
   - VBO: intercalar posição(x,y,z) e cor(r,g,b) por vértice.
   - VAO: armazenará os ponteiros de atributo.
   - EBO: opcional para índices [0,1,2] (útil se houver reutilização).

Exemplo de layout (float): [x,y,z,r,g,b] por vértice.

- b) Atributos no vertex shader:
   - layout(location = 0) in vec3 position;
   - layout(location = 1) in vec3 color;
   - out vec3 vColor; // passado ao fragment shader

Vertex shader (esqueleto):
```glsl
#version 400
layout(location = 0) in vec3 position;
layout(location = 1) in vec3 color;
out vec3 vColor;
uniform mat4 projection;
void main(){
   gl_Position = projection * vec4(position, 1.0);
   vColor = color;
}
```

Fragment shader simples:
```glsl
#version 400
in vec3 vColor;
out vec4 FragColor;
void main(){ FragColor = vec4(vColor,1.0); }
```

### 9) Reproduza um desenho quadriculado com primitivas (pode usar múltiplos VAOs e draw calls).

- Abordagem 1 (dinâmica, sem muitos VAOs): gerar uma grade (grid) de linhas usando um único VBO com pares de vértices para cada linha e desenhar com GL_LINES. Por exemplo, para uma grade MxN, gerar (M+1) linhas verticais e (N+1) linhas horizontais.
- Abordagem 2 (instância/retângulos): criar um quad unitário e usar instancing (glDrawArraysInstanced) para posicionar múltiplos quads via matriz de modelo enviada por instância (mais avançado).
- Abordagem 3 (múltiplos VAOs): menos eficiente; reserve VAO/VBO por objeto se for demonstração pedagógica.

### 10) Implemente uma classe de shaders por arquivos (feito em aula, repositório atualizado).

- Estrutura típica:
   - `Shader.h` / `Shader.cpp` com métodos para: carregar arquivo fonte, compilar shader (vertex/fragment), linkar programa, usar (`use()`), setar uniforms (int, float, vec3, mat4 etc.) e desalocar recursos.
- Métodos principais:
   - constructor(pathVertex, pathFragment) -> compila e linka
   - use()
   - setFloat(name, value), setMat4(name, mat), setVec3(name, vec)
   - destructor() -> glDeleteProgram

Exemplo de uso:
```cpp
Shader shader("shaders/vertex.glsl","shaders/fragment.glsl");
shader.use();
shader.setMat4("projection", projectionMatrix);
```

