# 🌳 Classificação de Espécies Arbóreas com Aprendizado de Máquina

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svg)](https://pytorch.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Projeto de Iniciação Científica que aplica a **metodologia Netflora** (Embrapa) adaptada com **XGBoost** para classificação automática de espécies arbóreas em área urbana, usando ortomosaico RGB de drone e dados dendrométricos de campo.

> **Local de estudo:** Casa Harry Schinke (Foz do Iguaçu – PR)  
> **Espécies classificadas:** 20  
> **F1-Score Macro:** 0,72 | **Kappa:** 0,5129

---

## 📌 Sumário

- [Sobre o Projeto](#-sobre-o-projeto)
- [Metodologia](#-metodologia)
- [Resultados Principais](#-resultados-principais)
- [Estrutura do Repositório](#-estrutura-do-repositório)
- [Como Reproduzir](#-como-reproduzir)
- [Limitações](#-limitações)
- [Trabalhos Futuros](#-trabalhos-futuros)
- [Referências](#-referências)
- [Autor](#-autor)

---

## 🎯 Sobre o Projeto

A identificação e o mapeamento de espécies arbóreas são essenciais para gestão da arborização urbana. Este projeto:

1. Coleta imagens RGB de alta resolução via drone (Mavic DJI Air 3 / Mini 3)
2. Gera ortomosaicos no MetaShape
3. Extrai atributos multimodais das copas:
   - **RGB** (médias, percentis, mín./máx.)
   - **HSV** (tonalidade, saturação, brilho)
   - **GLCM** (textura)
   - **Índices de vegetação visíveis** (ExG, VARI, GLI)
   - **Embeddings ResNet-18** reduzidos por PCA (20 componentes)
4. Classifica com **XGBoost** otimizado via `GridSearchCV`

---

## 🔬 Metodologia

### Pipeline

```
Planilhas de campo (áreas B e C)
        │
        ▼
Filtragem (>=10 indivíduos/espécie, remoção de "morta")
        │
        ▼
Reprojeção para CRS do ortomosaico
        │
        ▼
Extração de atributos (raster + patch)
        │
        ├── RGB/HSV/GLCM/índices
        └── ResNet-18 → PCA (20 dim)
        │
        ▼
XGBoost + GridSearchCV (StratifiedGroupKFold)
        │
        ▼
Avaliação (F1 Macro, Kappa, Matriz de Confusão)
        │
        ▼
Inferência em larga escala (mapa .tif)
```

### Estratégias adotadas

- **Raio de 1 m** centrado na árvore (reduz contaminação de borda)
- **20 patches manuais por espécie** (devido a discrepâncias posicionais sem GCPs)
- **StratifiedGroupKFold** com grupos por ID de árvore (evita vazamento)
- **Data Augmentation** para balancear espécies raras

---

## 📊 Resultados Principais

| Métrica | Valor |
|---------|-------|
| F1-Score Macro | **0,72** |
| Índice Kappa | **0,5129** |
| Acurácia Global | **0,63** |

### Melhores espécies (F1 > 0,90)
- *Acrocomia aculeata* (Macaúba – palmeira)
- *Syagrus romanzoffiana* (Jerivá – palmeira)
- *Peltophorum dubium* (Canafístula)

### Piores espécies (F1 < 0,45)
- *Albizia niopoides* (F1 = 0,17)
- *Tabernaemontana hystrix* (F1 = 0,31)
- *Lonchocarpus cultratus* (F1 = 0,42)

### Atributos mais importantes
1. **Banda vermelha (R)** – aparece em 4 das 10 primeiras posições
2. **Textura GLCM** (energy)
3. **Tonalidade HSV** (p50)

📄 Relatório completo: [`docs/relatorio_final.pdf`](docs/relatorio_final.pdf)

---

## 📂 Estrutura do Repositório

```
.
├── README.md
├── LICENSE
├── requirements.txt
├── .gitignore
├── docs/
│   └── relatorio_final.pdf
├── src/
│   └── codigo_final.py
├── assets/
│   ├── matriz_confusao.png
│   ├── importancia_variaveis.png
│   └── mapa_deteccoes.png
└── exemplos/
    └── planilha_exemplo.xlsx
```

---

## ⚙️ Como Reproduzir

### 1. Clonar o repositório

```bash
git clone https://github.com/SEU_USUARIO/monjolo-classificacao-especies.git
cd monjolo-classificacao-especies
```

### 2. Criar ambiente virtual

```bash
python -m venv venv
source venv/bin/activate      # Linux/Mac
venv\Scripts\activate         # Windows
```

### 3. Instalar dependências

```bash
pip install -r requirements.txt
```

### 4. Preparar dados

Você precisa de:
- Ortomosaico `.tif` (RGB, georreferenciado) → caminho definido em `RASTER_PATH`
- Planilhas `.xlsx` com colunas `Ind.`, `Nome Científico`, `UTM X`, `UTM Y`
- Pasta `patches/` com subpastas por espécie

> 💡 **Atenção:** o código original usa Google Colab com `google.colab.drive`. Para rodar localmente, **remova o bloco de `drive.mount()`** e ajuste os caminhos absolutos no topo do arquivo.

### 5. Executar

```bash
python src/codigo_final.py
```

Os resultados serão salvos em `resultados_RGB/`.

---

## ⚠️ Limitações

- **Discrepância posicional** entre pontos de campo e ortomosaico (ausência de GCPs)
- **Apenas 20 patches por espécie** — pode não capturar variabilidade intraespecífica
- **Desbalanceamento severo** (10 a 382 indivíduos por espécie)
- **Viés para a classe dominante** (*Leucaena leucocephala*)
- 72% das predições com **baixa confiança**

---

## 🚀 Trabalhos Futuros

- [ ] Ampliar base para 50+ patches por espécie
- [ ] Incluir imagens multitemporais (variação fenológica)
- [ ] Corrigir ortomosaico com GCPs
- [ ] Testar SMOTE / undersampling
- [ ] Validar com amostra independente de campo

---

## 📚 Referências

- SILVA, M. V. et al. **Metodologia Netflora**: classificação de espécies arbóreas por redes neurais convolucionais. SBSR, 2021.
- FASSNACHT, F. E. et al. Review of studies on tree species classification from remotely sensed data. *Remote Sensing of Environment*, v. 186, 2016.
- HAMAMURA, C. **Sensoriamento remoto para identificação taxonômica e mapeamento de espécies arbóreas em ambiente urbano**. Tese (Doutorado) – ESALQ/USP, 2020.
- SOPCHAKI, C. H. et al. Verificação da qualidade de ortomosaicos... *RA'E GA*, v. 43, 2018.
- LANDIS, J. R.; KOCH, G. G. The measurement of observer agreement for categorical data. *Biometrics*, 1977.

---

## 👤 Autor

**Cauã Maurici Reis** 
Estudante de Geografia Bacharelado – Unila 
🔗 [LinkedIn](https://www.linkedin.com/in/cau%C3%A3-maurici-reis-503b1b370/)

**Orientação:** Diego Moraes Flores **Orientador**
Noé Villegas Flores **Coorientador**

