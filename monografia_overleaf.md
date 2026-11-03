# Projeto de Monografia
## Efeitos de Redução de Dimensionalidade

---

# Motivação: A Maldição da Dimensionalidade

O avanço de pesquisas e trabalhos que utilizam dados com grande dimensionalidade, possibilitado pela facilidade da obtenção e armazenamento desses dados, como em genética e comportamentos sociais trouxe uma necessidade de simplificar o processo, compreender as variáveis que trazem informação ao estudo e agrupar casos similares para tornar a explicação mais clara.

O estudo de métodos de redução de dimensionalidade junto com técnicas de clusterização se tornou essencial, mas a literatura, porém, tem algumas lacunas de trabalhos abordando os efeitos da utilização conjunta dessas técnicas. É nesse sentido que desejamos investigar essas lacunas e testar algoritmos em conjunto para avaliar seus desempenhos.

## O Fenômeno

Em espaços de alta dimensão, a geometria quebra de formas contraintuitivas. A intuição construída em 2D ou 3D falha completamente — e os algoritmos de clustering, que dependem de noções de *proximidade* e *distância*, são as primeiras vítimas.

> *"Data points become nearly equidistant and classical notions of similarity lose discriminative power"* — Amate et al. (2026)

**O problema em espaços de alta dimensão:**

- Distâncias euclidianas entre pares de pontos convergem para o mesmo valor
- K-Means, AHC e GMM dependem de distância — estrutura de clusters desaparece
- A separação entre clusters fica indistinguível do ruído
- Redução não é pré-processamento opcional, é o que restaura o sinal

## Concentração de Distâncias

Para $x_1, \ldots, x_n \overset{iid}{\sim} \mathcal{N}(0, I_d)$, a distância quadrática entre dois pontos é:

$$d_{ij}^2 = \|x_i - x_j\|_2^2 = \sum_{k=1}^{d}(x_{ik} - x_{jk})^2$$

Cada parcela $(x_{ik} - x_{jk})^2$ segue distribuição $\chi^2(1)$. Pela **Lei dos Grandes Números**:

$$\frac{d_{ij}^2}{d} \xrightarrow{\;a.s.\;} \mathbb{E}[(x_{ik} - x_{jk})^2] = 2, \quad d \to \infty$$

Portanto $d_{ij} \approx \sqrt{2d}$ para todo par de pontos. O **coeficiente de variação** das distâncias:

$$\text{CV}[d_{ij}] = \frac{\text{SD}[d_{ij}]}{\mathbb{E}[d_{ij}]} \;\sim\; \frac{1}{\sqrt{d}} \;\xrightarrow{d \to \infty}\; 0$$

> **Interpretação:** Todas as distâncias concentram-se em torno de $\sqrt{2d}$. Com $d = 500$, a variação relativa é ≈ 3%. Pontos próximos e distantes tornam-se igualmente "distantes" — a noção de vizinhança colapsa.

Verificação empírica: o CV das distâncias cai com $1/\sqrt{d}$:

| $d$ | $\text{CV}[d_{ij}]$ empírico | $1/\sqrt{d}$ teórico |
|----:|:----------------------------:|:--------------------:|
|   2 |            0.4472            |        0.7071        |
|  10 |            0.2094            |        0.3162        |
|  50 |            0.0993            |        0.1414        |
| 100 |            0.0702            |        0.1000        |
| 500 |            0.0314            |        0.0447        |

## Teorema de Beyer et al. (1999)

**Condição formal para a instabilidade dos vizinhos mais próximos:**

Seja $D_{\max}(q)$ e $D_{\min}(q)$ as distâncias máxima e mínima de uma consulta $q$ a $n$ pontos. Sob condições gerais sobre a distribuição dos dados:

$$\lim_{d \to \infty} \Pr\!\left[\frac{D_{\max}(q) - D_{\min}(q)}{D_{\min}(q)} < \varepsilon\right] = 1, \quad \forall\,\varepsilon > 0$$

**Consequência direta:** o vizinho mais próximo torna-se tão distante quanto qualquer outro ponto — algoritmos como KNN, K-Means e DBSCAN perdem a capacidade discriminativa.

## Concentração de Volume na Hiperesfera

A fração do volume de uma bola $d$-dimensional de raio $R$ contida na casca exterior de espessura $\delta$:

$$F(d,\, \delta/R) = 1 - \left(1 - \frac{\delta}{R}\right)^{\!d}$$

Com $\delta/R = 0.01$ (apenas 1% do raio), a fração que fica **fora** do núcleo:

|   $d$ | $F(d,\; 0.01)$ |
|------:|:--------------:|
|     2 |      2.0%      |
|    10 |      9.6%      |
|    50 |     39.5%      |
|   100 |     63.4%      |
|   500 |     99.3%      |
| 1 000 |  **>99.99%**   |

Praticamente **todo o volume** migra para a superfície da hiperesfera. Consequência imediata para clustering: não existem pontos "centrais", qualquer centróide $\mu_k$ está longe de quase todos os membros do seu cluster.

## Impacto Direto no Clustering

**Por que K-Means, AHC e GMM degradam com a dimensão?**

A inércia total do K-Means decompõe-se em variância *within* e *between* clusters:

$$\underbrace{\sum_{i=1}^n \|x_i - \bar{x}\|^2}_{\text{Inércia total}} = \underbrace{\sum_{k=1}^K \sum_{i \in C_k} \|x_i - \mu_k\|^2}_{\text{Within-cluster } (W)} + \underbrace{\sum_{k=1}^K n_k\|\mu_k - \bar{x}\|^2}_{\text{Between-cluster } (B)}$$

O K-Means maximiza implicitamente a razão $B/W$. Em alta dimensão, a razão **sinal/ruído** desta decomposição:

$$\text{SNR}_d = \frac{B - W}{W} \;\sim\; \frac{\Delta}{\sqrt{d}}$$

onde $\Delta$ é a separação intrínseca dos clusters no espaço original. Com $d = 50$ e separação $\Delta = 1$:

$$\text{SNR}_{2} = 1.00 \qquad \longrightarrow \qquad \text{SNR}_{50} \approx \frac{1}{\sqrt{50}} \approx 0.14$$

> **Por que redução de dimensionalidade é essencial:** A mesma separação que é *óbvia* em 2D torna-se *invisível* em 50D. A redução dimensional restaura o SNR ao eliminar as dimensões que contribuem apenas com ruído, permitindo que $B/W$ volte a discriminar os clusters.

---

# Réplica do Estudo de Amate et al. (2026)

## Escopo e Desenho Experimental

**Métodos de Redução Avaliados**

| Família  | Método            |
|----------|-------------------|
| Linear   | PCA               |
| Kernel   | Kernel PCA (RBF)  |
| Manifold | Isomap, MDS, UMAP |

**Algoritmos de Clusterização**

| Algoritmo | Tipo                 |
|-----------|----------------------|
| K-Means   | Centróide            |
| AHC       | Hierárquico (Ward)   |
| GMM       | Probabilístico (EM)  |
| OPTICS    | Baseado em densidade |

## Pipeline Experimental

```
Dados brutos
    │
    ▼
[Normalização z-score]
    │
    ├──────────── Sem redução ──────────────────────────────┐
    │                                                        │
    ▼                                                        │
[DR: PCA | Kernel PCA | Isomap | MDS | UMAP]               │
    │  Níveis: k−1  |  25% de d  |  50% de d               │
    ▼                                                        │
[Clustering: K-Means | AHC | GMM | OPTICS] ◄───────────────┘
    │
    ▼
[ARI vs. Ground Truth → comparação com e sem DR]
```

**Níveis de redução testados:**

| Nível         | Dimensões (d=50) | Racional                                                                         |
|---------------|:----------------:|----------------------------------------------------------------------------------|
| **k − 1**     |        1         | Limite teórico de Ding & He (2004): subspace bound para K-Means                  |
| **25% de d**  |       12         | Literatura de clustering textual                                                  |
| **50% de d**  |       25         | Prática comum com PCA aplicado                                                    |

## Métrica de Avaliação: ARI

O **Adjusted Rand Index** mede a concordância entre a partição obtida e o ground truth, corrigida pelo acaso:

$$\text{ARI} = \frac{\text{RI} - \mathbb{E}[\text{RI}]}{\max(\text{RI}) - \mathbb{E}[\text{RI}]} \in [-1,\; 1]$$

onde o Rand Index base é:

$$\text{RI} = \frac{a + b}{\binom{n}{2}}, \quad a = \text{pares no mesmo cluster em ambas as partições}, \quad b = \text{pares em clusters diferentes em ambas}$$

| Valor      | Interpretação                     |
|------------|-----------------------------------|
| **1.0**    | Partição perfeita                 |
| **≈ 0.0**  | Aleatório / não melhor que chance |
| **< 0.0**  | Pior que aleatório                |

**Vantagens do ARI:**

- Ajustado para chance (sem viés com $K$ grande)
- Labels não precisam corresponder entre partições
- Aplicável a todos os datasets com ground truth
- Simétrico: $\text{ARI}(U, V) = \text{ARI}(V, U)$

---

# Experimento Sistemático

Reprodução controlada da metodologia de Amate et al. (2026): cinco métodos de redução (PCA, Kernel PCA, Isomap, MDS, UMAP) × quatro algoritmos de clusterização (K-Means, AHC, GMM, OPTICS) × três níveis de redução (k−1, 25%, 50%) sobre quatro tipos de datasets sintéticos em 50 dimensões.

## Geração dos Datasets

Quatro tipos de dados sintéticos embebidos em 50 dimensões via projeção gaussiana aleatória, replicando as famílias do artigo:

- **Moons** ($n = 400$, $d = 50$, $k = 2$): variedade curvilínea em forma de duas luas.
- **Circles** ($n = 400$, $d = 50$, $k = 2$): dois círculos concêntricos de raios diferentes.
- **Gaussiano-3k** ($n = 240$, $d = 50$, $k = 3$): três clusters gaussianos esféricos.
- **Gaussiano-5k** ($n = 300$, $d = 50$, $k = 5$): cinco clusters gaussianos esféricos.

## Execução do Grid de Experimentos

O experimento avalia todas as combinações de:

- 6 configurações de DR (sem redução + 5 métodos) × 3 níveis = 16 variantes
- 4 algoritmos de clusterização
- 4 datasets
- 10 repetições com sementes distintas

Total: $16 \times 4 \times 4 \times 10 = 2.560$ execuções de clusterização por grid.

## Resultados — Boxplots por Algoritmo

A distribuição do ARI (10 réplicas) para cada combinação de método de redução e nível, separada por dataset e algoritmo de clusterização, reproduz as Figuras 3–6 de Amate et al. (2026). Ver figuras geradas pelo código R.

## Análise Agregada — Taxa de Vitórias

Para cada combinação DR × nível, calcula-se a proporção de datasets em que a redução supera o baseline (sem redução) e o ganho médio de ARI.

## Heatmap — ARI Médio por Combinação DR × Algoritmo

O heatmap consolida o ARI médio por método de redução (melhor nível selecionado) e algoritmo de clusterização para uma visão comparativa direta entre todas as combinações.

## Efeito do Nível de Redução

Verifica se o nível de compressão (k−1, 25%, 50%) impacta o ARI de forma consistente entre algoritmos e datasets.

## Custo Computacional

O experimento com 1000 réplicas levou aproximadamente 3 dias. A atribuição de custo por par (DR, algoritmo) utiliza a lógica:

$$\text{Custo atribuído} = \frac{t_{DR}}{4} + t_{clustering}$$

pois o tempo de DR é compartilhado entre os 4 algoritmos que consomem a mesma projeção. A extrapolação para o experimento completo multiplica por $1000 \text{ reps} \times 4 \text{ datasets} \times 3 \text{ níveis} = 12.000$ chamadas por par.

---

# Experimentos com Dados Reais

Para validar as conclusões obtidas nos cenários sintéticos, estendemos a análise para seis conjuntos de dados reais provenientes do UCI Machine Learning Repository.

A seleção cobre intencionalmente uma faixa ampla de dimensionalidades, de $d=4$ (Iris, Blood Transfusion) até $d=90$ (Libras Movement), incluindo dois datasets de alta dimensão (WDBC com $d=30$ e Libras com $d=90$) que se aproximam do regime estudado nos experimentos sintéticos. Ao contrário dos cenários sintéticos controlados, esses conjuntos apresentam desafios reais como ruído não gaussiano, sobreposição natural de classes e diferentes escalas de variância.

## Datasets Utilizados

| Dataset           |  $n$ | $d$ | $k$ |
|-------------------|-----:|----:|----:|
| Iris              |  150 |   4 |   3 |
| Blood Transfusion |  748 |   4 |   2 |
| Yeast             | 1484 |   8 |  10 |
| Wine              |  178 |  13 |   3 |
| WDBC              |  569 |  30 |   2 |
| Libras Movement   |  360 |  90 |  15 |

Utilizamos o PCA (projeção linear) e o UMAP (projeção manifold não-linear) para inspecionar visualmente a separabilidade das classes antes da clusterização.

## Visualização dos Resultados com Dados Reais

O heatmap do ARI por dataset e método de redução, facetado por algoritmo, apresenta os datasets ordenados por dimensionalidade crescente ($d=4$ a $d=90$). Células mais claras indicam maior ARI.

---

# Referências

- Amate, Bakhtyari, Roy & Makarenkov (2026). *Assessing the impact of dimensionality reduction on clustering performance — a systematic study.* arXiv:2604.22099

- Beyer, Goldstein, Ramakrishnan & Shaft (1999). When is "nearest neighbor" meaningful? *ICDT*, LNCS 1540, 217–235.

- Ding & He (2004). K-means clustering via principal component analysis. *ICML*, 225–232.

- McInnes & Healy (2018). UMAP: Uniform Manifold Approximation and Projection for dimension reduction. *arXiv:1802.03426*
