**ENTRADA:**

- `caminho_imagem`: Caminho para a imagem de entrada.
- `caminho_prototxt`: Caminho para o arquivo prototxt do modelo (opcional, padrão: "models/MobileNetSSD_deploy.prototxt.txt").
- `caminho_modelo`: Caminho para o arquivo caffemodel do modelo (opcional, padrão: "models/MobileNetSSD_deploy.caffemodel").
- `confianca_minima`: Limiar de confiança para filtrar detecções fracas (opcional, padrão: 0).

**SAÍDA:**

- Imagem com caixas delimitadoras e rótulos de classe para objetos detectados, salva em "images/output/image.jpg".
- Exibição da imagem de saída.

**VARIAVEIS:**

- `CLASSES`: Lista de rótulos de classe que o MobileNet SSD foi treinado para detectar.
- `CORES`: Matriz de cores aleatórias para cada classe.
- `rede`: Modelo de rede neural carregado.
- `imagem`: Imagem de entrada carregada.
- `altura, largura`: Dimensões da imagem de entrada.
- `blob`: Objeto blob de entrada para a rede neural.
- `deteccoes`: Detecções e previsões da rede neural.
- `confianca`: Confiança da detecção.
- `indice_classe`: Índice do rótulo de classe da detecção.
- `caixa_delimitadora`: Coordenadas da caixa delimitadora da detecção.
- `inicioX, inicioY, fimX, fimY`: Coordenadas inteiras da caixa delimitadora.
- `rotulo`: Rótulo de classe formatado com a confiança.
- `y`: Posição vertical do rótulo de texto.

**INICIO**
```
// Inicialização das classes e cores
CLASSES ← ["background", "aeroplane", "bicycle", "bird", "boat", "bottle", "bus", "car", "cat", "chair", "cow", "diningtable", "dog", "horse", "motorbike", "person", "pottedplant", "sheep", "sofa", "train", "tvmonitor"] 1
CORES ← gerar_cores_aleatorias(comprimento(CLASSES), 0, 255)

// Carregar o modelo
IMPRIMIR "[INFO] carregando modelo..."
rede ← carregar_rede_caffe(caminho_prototxt, caminho_modelo)

// Carregar e pré-processar a imagem
imagem ← carregar_imagem(caminho_imagem)
altura, largura ← obter_dimensoes(imagem)
blob ← criar_blob_imagem(redimensionar_imagem(imagem, 300, 300), 0.007843, (300, 300), 127.5)

// Detecção de objetos
IMPRIMIR "[INFO] computando detecções de objetos..."
definir_entrada_rede(rede, blob)
deteccoes ← propagacao_para_frente_rede(rede)

// Processar as detecções
PARA cada detecção em deteccoes FAZER
confianca ← obter_confianca_deteccao(deteccao)

SE confianca > confianca_minima ENTAO
indice_classe ← obter_indice_classe(deteccao)
caixa_delimitadora ← obter_caixa_delimitadora(deteccao, largura, altura)
inicioX, inicioY, fimX, fimY ← converter_caixa_para_inteiros(caixa_delimitadora)

    rotulo ← formatar_rotulo(CLASSES[indice_classe], confianca * 100)
    IMPRIMIR "[INFO] " + rotulo

    desenhar_retangulo(imagem, inicioX, inicioY, fimX, fimY, CORES[indice_classe])
    y ← inicioY - 15 SE inicioY - 15 > 15 SENAO inicioY + 15
    desenhar_texto(imagem, rotulo, inicioX, y, CORES[indice_classe])

FIM SE
FIM PARA

// Salvar e exibir a imagem de saída
salvar_imagem("images/output/image.jpg", imagem)
exibir_imagem("Output", imagem)
esperar_tecla()

```

**FIM**
