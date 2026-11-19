# Efeitos de Redução de Dimensionalidade sobre Algoritmos de Clusterização

Monografia de graduação em Estatística — UFMG, 2026.

**Autor:** Rafael Arocho  
**Orientador:** Uriel Moreira  
**Departamento:** Departamento de Estatística, Instituto de Ciências Exatas, UFMG

---

## Sobre o trabalho

Este trabalho investiga os efeitos da redução de dimensionalidade sobre o desempenho de algoritmos de clusterização, por meio de uma replicação controlada da metodologia de Amate et al. (2026) aplicada a dados sintéticos e reais.

**Métodos de redução avaliados:** PCA, Kernel PCA (RBF), Isomap, MDS, UMAP  
**Algoritmos de clusterização:** K-Means, AHC (Ward), GMM (EM), OPTICS  
**Métrica:** Adjusted Rand Index (ARI)  
**Experimento:** 5 métodos × 3 níveis × 4 algoritmos × 4 datasets sintéticos × 1000 réplicas = 256.000 execuções

---

## Estrutura do repositório

```
.
├── monografia.tex              # Documento principal (LaTeX)
├── monografia.pdf              # Versão compilada mais recente
├── analise.qmd                 # Código completo da análise (Quarto/R)
│
├── figuras/                    # Figuras PDF geradas pelo analise.qmd
│   ├── proj_2d.pdf             # Projeção 2D por método de redução
│   ├── boxplot_kmeans.pdf      # ARI boxplots — K-Means
│   ├── boxplot_ahc.pdf         # ARI boxplots — AHC
│   ├── boxplot_optics.pdf      # ARI boxplots — OPTICS
│   ├── boxplot_gmm.pdf         # ARI boxplots — GMM
│   ├── heatmap_ari.pdf         # Heatmap ARI médio DR × algoritmo
│   ├── niveis_reducao.pdf      # Efeito do nível de redução
│   ├── eficiencia_custo.pdf    # Fronteira custo × ARI
│   ├── heatmap_tempo.pdf       # Heatmap de tempo computacional
│   ├── vis_reais_2d.pdf        # Visualização 2D dos dados reais
│   └── lollipop_reais.pdf      # ARI em dados reais (landscape)
│
├── dados/
│   └── reais/                  # Datasets reais do UCI Machine Learning Repository
│       ├── iris/
│       ├── wine/
│       ├── breast+cancer+wisconsin+diagnostic/   # WDBC
│       ├── blood+transfusion+service+center/
│       ├── yeast/
│       ├── libras+movement/
│       └── ...                 # demais datasets baixados do UCI
│
└── referencias/
    ├── AmateEtAl2026.pdf       # Referência principal (arXiv:2604.22099)
    └── NicolasMonteiro2026.pdf # Monografia de referência (UFMG 2026-1)
```

---

## Como compilar a monografia

Requer uma distribuição LaTeX com os pacotes: `babel`, `geometry`, `amsmath`, `booktabs`, `setspace`, `graphicx`, `pdflscape`, `titlesec`, `hyperref`, `natbib`.

```bash
pdflatex monografia.tex
pdflatex monografia.tex   # segunda passagem para referências cruzadas
```

As figuras em `figuras/` precisam existir antes da compilação. Para gerá-las, execute `analise.qmd`.

---

## Como reproduzir a análise

### Dependências (R)

```r
install.packages(c(
  "tidyverse", "cluster", "mclust", "dbscan",
  "kernlab", "umap", "dimRed",
  "aricode", "patchwork", "ggplot2"
))
```

### Executar

Abra `analise.qmd` no RStudio e clique em **Render**, ou via terminal:

```bash
quarto render analise.qmd
```

O script gera automaticamente todas as figuras em `figuras/` e produz um relatório HTML com os resultados intermediários.

> **Atenção:** o bloco de execução principal (`execucao-experimento`) leva aproximadamente 3 dias em uma máquina convencional com 1000 réplicas. O cache do Quarto (`*_cache/`) é criado localmente após a primeira execução e reutilizado nas seguintes.

---

## Referência principal

Amate, Bakhtyari, Roy & Makarenkov (2026). *Assessing the impact of dimensionality reduction on clustering performance — a systematic study.* arXiv:[2604.22099](https://arxiv.org/abs/2604.22099).
