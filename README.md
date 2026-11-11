# Classificação e Mapeamento de Espécies Arbóreas Urbanas com Netflora e XGBoost

Repositório oficial da pesquisa voltada à identificação e classificação taxonômica de espécies arbóreas a partir de imagens RGB obtidas por VANT (drone) na **Casa Harry Schinke** (Foz do Iguaçu - PR).

## 📄 Sobre a Pesquisa
Este estudo aplicou a adaptação da metodologia **Netflora** combinada com atributos multimodais (espectrais RGB/HSV, textura GLCM e índices de vegetação) e o classificador de aprendizado de máquina **XGBoost**. O trabalho aborda os desafios de mapeamento em áreas urbanas de vegetação densa e discute a importância de correções planimétricas e séries temporais na análise florestal.

* **Área de Estudo:** Casa Harry Schinke, Foz do Iguaçu, PR.
* **Metodologia:** Extração de patches de copas, redução de dimensionalidade via PCA (a partir de embeddings de ResNet-18) e otimização de hiperparâmetros com `GridSearchCV` e validação cruzada espacial (`StratifiedGroupKFold`).

## 📂 Estrutura do Repositório
* `codigo_final.py`: Script completo em Python (desenvolvido originalmente para ambiente Google Colab) contendo o pipeline de pré-processamento, extração de atributos, modelagem preditiva, mapeamento em larga escala e geração de visualizações com caixas delimitadoras (*bounding boxes*).
* `relatorio_texto.pdf`: Relatório técnico e científico completo detalhando a fundamentação teórica, materiais, métodos, resultados (Métricas F1-Score Macro, Índice Kappa e Matriz de Confusão) e conclusões da pesquisa.

## 🛠️ Tecnologias e Ferramentas Utilizadas
* **Linguagem:** Python
* **Bibliotecas principais:** `scikit-learn`, `XGBoost`, `rasterio`, `geopandas`, `PyTorch` / `torchvision`, `scikit-image`, `pandas`, `numpy`, `matplotlib`, `seaborn`
* **Geotecnologias:** QGIS, Agisoft MetaShape (processamento de ortomosaicos)

## 🚀 Como Executar o Código
O script `codigo_final.py` foi estruturado para ser executado em notebooks interativos como o **Google Colab**, pois faz integração direta com o Google Drive para leitura dos rasters (.tif) e planilhas de campo (.xlsx). Certifique-se de ajustar os caminhos dos diretórios (`/content/drive/MyDrive/...`) para o seu ambiente de arquivos caso decida executá-lo localmente.
