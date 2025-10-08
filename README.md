# Projeto de Exercícios — Processamento Gráfico (OpenGL Moderno)

Última atualização: 07/10/2025
Resumo
------
Este repositório centraliza os exercícios das disciplinas de Processamento Gráfico / Fundamentos de Computação Gráfica.
Autores
-------
- LEONARDO FRONZA

Objetivo
--------
Fornecer um conjunto de exercícios práticos para aprender o pipeline programável (vertex/fragment shaders), criar janelas com GLFW, carregar texturas e trabalhar com transformações (GLM).
Requisitos
----------
- CMake >= 3.11
- Compilador com suporte a C++17 (g++, clang, MSVC)
- Driver/GPU com suporte a OpenGL 3.3+ (idealmente 4.0+)
Principais dependências (via CMake FetchContent ou arquivos locais)
- GLFW — janela, contexto e input
- GLAD — loader (arquivo local em `common/glad.c`)
- GLM — matemática para gráficos
- stb_image — carregamento de imagens (texturas)
Estrutura do repositório
------------------------

Como compilar (PowerShell — Windows)
------------------------------------
Abra o PowerShell na raiz do repositório e execute:
```powershell
# criar pasta de build, configurar e compilar (Debug)
if (-not (Test-Path build)) { New-Item -ItemType Directory build }
Observações:
- Em sistemas Unix (Linux/macOS) substitua os comandos pelo equivalente `mkdir -p build; cd build; cmake ..; cmake --build .`.
- Se o gerador CMake pedir escolha de toolchain, use o provedor que preferir (Visual Studio, MinGW, Ninja).
Executando os exercícios
------------------------
Depois de compilar, os executáveis ficam na pasta de build (ou subpastas dependendo do gerador).
O que esperar:
- Janela criada via GLFW
- Shaders compilados (logs aparecem no terminal se houver erro)
- Controles: ESC fecha a janela
Adicionar um novo exercício
---------------------------
1. Criar o arquivo em `src/ListaN/MeuEx.cpp` (copiar um template, ex.: `HelloTriangle.cpp`).
Trechos úteis no CMake
----------------------
Procure por uma variável `EXERCISES` no `CMakeLists.txt` e adicione entradas como:
Dicas rápidas de desenvolvimento
-------------------------------
- Alterar resolução: editar `WIDTH` e `HEIGHT` no exercício.
- Usar MSAA: ativar `glfwWindowHint(GLFW_SAMPLES, N)` e habilitar `glEnable(GL_MULTISAMPLE)`.
- Transparência: habilitar blending com `glEnable(GL_BLEND)` e `glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA)`.
Resolução de problemas (rápido)
-------------------------------
- Janela não abre / crash
    - Causa provável: versão OpenGL não suportada
- Erro relacionado ao GLAD
    - Causa provável: `common/glad.c` não está compilado no alvo
    - Solução: confirmar inclusão em `CMakeLists.txt`
<!-- README em Português (PT-BR) -->

# Listas de Exercícios — Processamento Gráfico (OpenGL Moderno)

**Última atualização:** 07/10/2025

Descrição
---------
Repositório com exercícios em C++ que usam OpenGL moderno (Core Profile). Os exemplos cobrem: criação de janelas com GLFW, carregamento de funções com GLAD, operações matemáticas com GLM, gerenciamento de VBO/VAO e uso básico de texturas (stb_image).

Autores
-------
- LEONARDO FRONZA

Requisitos
----------
- CMake >= 3.11
- Compilador com suporte a C++17 (g++, clang, MSVC)
- Driver/GPU com suporte a OpenGL 3.3+ (4.0 recomendado)

Dependências principais
-----------------------
- GLFW — janela, contexto OpenGL e input
- GLAD — loader (arquivo local: `common/glad.c`)
- GLM — matemática para gráficos
- stb_image — carregamento de texturas (header-only)

Estrutura do repositório
------------------------
```
common/        -> utilitários (ex.: glad.c)
include/       -> headers públicos (glad/, KHR/)
src/Lista1/    -> Exercícios da Lista 1
src/Lista2/    -> Exercícios da Lista 2
CMakeLists.txt -> Configuração do projeto (gera executáveis por exercício)
```

Como compilar
-------------
Escolha o fluxo conforme seu sistema.

PowerShell (Windows)
```powershell
# Na raiz do repositório
if (-not (Test-Path build)) { New-Item -ItemType Directory build }
Set-Location build
cmake ..
cmake --build . --config Debug
```

Linux / macOS (bash)
```bash
mkdir -p build
cd build
cmake ..
cmake --build .
```

Observações
- Se o CMake pedir toolchain, escolha um gerador compatível (Visual Studio, MinGW, Ninja etc.).
- Para build de Release em Windows, use `--config Release`.

Executando os exemplos
----------------------
Os binários ficam em `build/` (ou subpastas do gerador).

PowerShell (Windows)
```powershell
.\HelloTriangle.exe
```

Linux/macOS
```bash
./HelloTriangle
```

O que esperar
- Janela criada via GLFW
- Shaders compilados e vinculados (erros aparecem no terminal)
- Tecla ESC: fecha a janela

Adicionar um novo exercício
---------------------------
1. Crie o arquivo `src/ListaN/MeuEx.cpp` (copie um template, ex.: `HelloTriangle.cpp`).
2. Abra `CMakeLists.txt` e adicione o caminho (sem `.cpp`) à variável `EXERCISES`.
3. Reconfigure o CMake e compile novamente.

Exemplo (em `CMakeLists.txt`):
```cmake
set(EXERCISES
    Lista2/HelloTriangle
    Lista1/L1_Ex05a
    # adicionar novos exercícios aqui
)
```

Dicas rápidas
-------------
- Alterar resolução: modificar `WIDTH` e `HEIGHT` no exercício.
- Habilitar MSAA: `glfwWindowHint(GLFW_SAMPLES, N)` + `glEnable(GL_MULTISAMPLE)`.
- Transparência: `glEnable(GL_BLEND); glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);`.

Resolução de problemas
----------------------
| Problema | Causa provável | Solução |
|---|---|---|
| Janela não abre / crash | Versão OpenGL não suportada | Comentar ou ajustar `glfwWindowHint` de versão; testar 3.3 |
| Erro GLAD / símbolo indefinido | `common/glad.c` não está sendo compilado | Verificar `CMakeLists.txt` e inclusão do arquivo |
| Tela preta / nada desenhado | Shaders falharam ou VBO/VAO incorretos | Conferir logs do shader e atributos/locations |
| Build falha no Windows | Toolchain ausente | Instalar Visual Studio Build Tools ou MinGW-w64 |

Contribuição
------------
- Mantenha cabeçalho com autoria e data nos arquivos fonte.
- Prefira pull requests pequenas e com explicação clara das mudanças.

Contato
-------
Dúvidas: contate o responsável pela disciplina ou o autor listado.

Licença
-------
Uso educacional; ajuste conforme as regras da sua instituição.

---


