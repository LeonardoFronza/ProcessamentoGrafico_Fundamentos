## Respostas & Notas (Lista 2)

1) **Explique por que e quando usar projeção ortográfica (glm::ortho).**

	- A projeção ortográfica preserva paralelismo e não aplica perspectiva; é usada quando não queremos que objetos distantes fiquem menores (UI, mapas 2D, cenas técnicas). Usamos `glm::ortho` quando trabalhamos com coordenadas de mundo leve ou interfaces onde escala uniforme é desejada.

2) **Como mapear coordenadas de mundo para viewport e qual a diferença entre eles?**

	- Coordenadas de mundo são um sistema lógico onde definimos posições de objetos (por exemplo, -10..10). A viewport define a área da janela em pixels onde a cena será desenhada. O pipeline: coordenadas de mundo → multiplicação por projeção/view → coordenadas normalizadas → viewport (glViewport) que mapeia normalized device coordinates (NDC) para pixels.

3) **Por que inverter o eixo Y para coordenadas de tela?**

	- Sistemas de janela (UI) costumam ter origem no canto superior esquerdo; OpenGL tem origem no centro e usualmente y cresce para cima. Inverter Y (ymax=0) torna mais direto posicionar elementos em coordenadas de pixel com origem no topo.

4) **Explique a diferença entre alterar a matriz de projeção (glm::ortho) e alterar o viewport (glViewport).**

	- `glm::ortho` altera como coordenadas de mundo são projetadas em NDC (afeta escala/posição dos objetos). `glViewport` apenas restringe/regiiona a saída (mapeia NDC para uma sub-área da janela). Mudar a projeção altera a interpretação das coordenadas; mudar viewport apenas altera onde a imagem projetada aparece.

5) **Como renderizar a mesma cena em múltiplas viewports sem duplicar dados?**

	- Reuse o mesmo VAO/VBO contendo a geometria. Antes de cada draw call, chame `glViewport` com os parâmetros do quadrante desejado e, se necessário, ajuste matrizes de modelo para posicionamento. Execute `glDrawArrays` ou `glDrawElements` repetidamente — sem criar novos buffers.

6) **Explique a estratégia para capturar cliques do mouse e converter para coordenadas de mundo/tela (L2_Ex06).**

	- Use `glfwSetMouseButtonCallback` para capturar cliques. Em callback, use `glfwGetCursorPos` para obter coordenadas de janela (pixels). Se a projeção utilizada for de tela (0..800, 600..0), esses valores mapeiam diretamente; caso contrário, converta: x_ndc = (2*x_pixel / width) - 1; y_ndc = 1 - (2*y_pixel / height) (ou usar a matriz inversa da projeção para converter para coordenadas de mundo).

7) **Por que mostrar vértices temporários como pontos ao criar triângulos?**

	- Fornece feedback visual imediato ao usuário, indicando onde os próximos cliques serão transformados em triângulos; melhora usabilidade e depuração.

8) **Como gerenciar buffers dinamicamente quando triângulos são criados em tempo de execução?**

	- Estratégias:
	  - Recriar e re-enviar todo o VBO (glBufferData) quando a lista de vértices mudar — simples e suficiente para poucos triângulos.
	  - Usar glBufferSubData para atualizar apenas parte do buffer.
	  - Reservar um buffer grande (GL_DYNAMIC_DRAW) e gerenciar o offset manualmente.

9) **Como organizar os dados de cor e posição para triângulos dinâmicos?**

	- Intercalar atributos por vértice ([x,y,z,r,g,b]) facilita carregamento e evita múltiplos VBOs. Configurar `glVertexAttribPointer` para cada atributo respeitando o stride.

10) **Notas de otimização e segurança (curtas)**

	- Use VAOs para minimizar custo de reconfiguração. Prefira `GL_DYNAMIC_DRAW` para buffers que mudam com frequência. Sempre validar status de compilação dos shaders e tratar erros.

---

