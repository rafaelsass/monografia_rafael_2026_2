# Roteiro Corrido da Apresentação de Defesa (20 Minutos)

**Título:** Efeitos de redução de dimensionalidade sobre algoritmos de clusterização: uma replicação sistemática  
**Autor:** Rafael Arocho | **Orientador:** Prof. Uriel Moreira  
**Arquivo de slides correspondente:** `apresentacao.tex` (25 slides numerados + 1 slide final de encerramento)  
**Ritmo estimado:** ~125–135 palavras por minuto, com breves pausas para apontar os elementos gráficos.

---

## Abertura

### Slide 1 — Capa `[00:00 – 00:30]`
> *[Aguardar a banca se acomodar, olhar para os avaliadores e iniciar com calma.]*

"Boa tarde a todos, cumprimento os professores membros da banca, meu orientador professor Uriel Moreira e os colegas presentes. Meu nome é Rafael Arocho e hoje apresento minha monografia de conclusão de curso em Estatística, intitulada *'Efeitos de redução de dimensionalidade sobre algoritmos de clusterização: uma replicação sistemática'*."

---

### Slide 2 — Roteiro da Apresentação `[00:30 – 01:00]`
> *[Passar para o Slide 2 e indicar brevemente as cinco seções.]*

"Organizei a apresentação na mesma lógica do texto escrito, dividida em cinco partes. Começo pela motivação do problema e pela fundamentação matemática da maldição da dimensionalidade. Em seguida, apresento a metodologia — os métodos avaliados e a métrica de comparação. Na terceira parte, que é o coração do trabalho, detalho o experimento sistemático com dados sintéticos e análise de custo computacional. Depois, trago a validação empírica em seis conjuntos de dados reais e, por fim, sintetizo as conclusões e recomendações práticas."

---

## 1. Introdução e Fundamentação Teórica

### Slide 3 — Motivação, Lacuna e Escopo do Trabalho `[01:00 – 02:20]`
> *[Apontar primeiro para o bloco da esquerda, depois para o da direita.]*

"O ponto de partida deste trabalho é o crescimento expressivo de bases de dados com dezenas ou centenas de variáveis — em áreas como bioinformática, processamento de sinais e ciências sociais. Quando não temos rótulos e queremos descobrir grupos naturais nesses dados, recorremos à clusterização, muitas vezes precedida por alguma redução de dimensionalidade.

O problema, apontado recentemente por Amate e colaboradores em 2026, é que a literatura historicamente avalia essas duas etapas de forma isolada, ou testa pares muito específicos em um único domínio. Falta uma avaliação controlada do efeito **conjunto** entre método de redução, nível de compressão e algoritmo de agrupamento.

Nesse contexto, propusemos uma replicação sistemática da metodologia de Amate e colaboradores em dados sintéticos de 50 dimensões, com mil réplicas de Monte Carlo por cenário — somando 256 mil execuções —, mas introduzindo três diferenciais importantes: primeiro, substituímos o autoencoder variacional pelo **UMAP** com semente fixa, avaliando justamente a técnica de aprendizado de variedade mais difundida hoje na prática e que o estudo original havia deixado de fora; segundo, cronometramos individualmente o custo computacional real de cada etapa para construir uma fronteira de eficiência; e terceiro, validamos os achados em seis bases clássicas do repositório UCI."

---

### Slide 4 — A Maldição da Dimensionalidade `[02:20 – 03:50]`
> *[Apontar para as equações à esquerda e, ao final, para a coluna de $d=50$ e $d=500$ na tabela à direita.]*

"Antes de ir aos algoritmos, vale formalizar por que a alta dimensão é tão hostil para a clusterização. Suponha uma amostra de vetores independentes $x_1$ até $x_n$ vindos de uma normal padrão $d$-dimensional. Para qualquer par de observações, cada diferença ao quadrado coordenada a coordenada segue duas vezes uma qui-quadrado com um grau de liberdade; logo, a distância euclidiana quadrática $d_{ij}^2$ segue $2\chi^2(d)$.

Pela Lei dos Grandes Números, $d_{ij}^2$ dividido por $d$ converge quase certamente para 2 quando a dimensão cresce, o que significa que todas as distâncias se aproximam de $\sqrt{2d}$. Aplicando o método delta para tomar a raiz quadrada, a variância assintótica de $d_{ij}$ estabiliza em torno de 1, enquanto a média cresce com $\sqrt{2d}$. Como consequência, o **coeficiente de variação** das distâncias cai na ordem de $1/\sqrt{2d}$ e converge para zero.

Na tabela à direita, ilustro isso simulando uma matriz de 400 pontos em cada dimensão, o que gera 79.800 pares de distâncias: em dimensão 2, a variação relativa é de cerca de 45%; em dimensão 50 — que é o regime do nosso experimento sintético — ela já cai para 10%; e em dimensão 500 cai para apenas 3%. Como demonstrado por Beyer e colaboradores em 1999, o vizinho mais próximo passa a ficar praticamente à mesma distância que o ponto mais distante. Sem contraste de distância, métodos baseados em centróide, ligação hierárquica ou densidade perdem o sinal discriminativo — e a redução de dimensionalidade deixa de ser estética para se tornar necessária."

---

## 2. Metodologia

### Slide 5 — Métodos de Redução e Algoritmos de Clusterização `[03:50 – 05:10]`
> *[Percorrer brevemente a coluna da esquerda e depois a da direita, destacando a diversidade de famílias.]*

"Para capturar diferentes pressupostos geométricos, cruzamos cinco métodos de redução de dimensionalidade com quatro algoritmos de clusterização.

Do lado da redução, temos o **PCA** como referência linear clássica, projetando os dados nas direções ortogonais de maior variância; o **Kernel PCA** com kernel gaussiano RBF, que mapeia implicitamente os dados para um espaço de características não-linear; o **Isomap**, que estima distâncias geodésicas por caminhos mínimos em um grafo de vizinhos; o **MDS clássico** por coordenadas principais, que sob distância euclidiana é algebricamente dual ao PCA; e o **UMAP**, que modela a variedade Riemanniana local preservando simultaneamente estrutura local e global.

Do lado da clusterização, escolhemos um representante de cada paradigma clássico: o **K-Means**, baseado em centróides e minimização da inércia intra-grupo; o **AHC**, agrupamento hierárquico aglomerativo com o critério de variância mínima de Ward; o **GMM**, modelo probabilístico de mistura de gaussianas ajustado via algoritmo EM; e o **OPTICS**, método baseado em densidade que constrói um gráfico de alcançabilidade sem exigir esfericidade dos grupos."

---

### Slide 6 — Métrica de Avaliação: Adjusted Rand Index (ARI) `[05:10 – 06:00]`
> *[Apontar para a expressão do ARI e para os dois blocos inferiores.]*

"Como em todos os nossos experimentos conhecemos os rótulos verdadeiros apenas para validação externa, medimos a qualidade dos agrupamentos pelo **Índice de Rand Ajustado**, o ARI, de Hubert e Arabie.

O índice de Rand simples conta a fração de pares de pontos em que duas partições concordam — seja colocando o par junto, contado em $a$, seja separando o par, contado em $b$. O ARI desconta o valor esperado dessa concordância sob atribuição aleatória e normaliza pelo máximo possível. Assim, ARI igual a 1 indica recuperação exata dos grupos verdadeiros, ARI próximo de zero indica desempenho indistinguível de um sorteio aleatório, e valores negativos indicam concordância pior que o acaso. Além disso, ele é simétrico, invariante à permutação dos nomes dos clusters e não sofre inflação artificial quando o número de grupos $K$ aumenta."

---

## 3. Experimento Sistemático (Dados Sintéticos)

### Slide 7 — Pipeline Experimental e Desenho Fatorial `[06:00 – 07:15]`
> *[Seguir o fluxograma da esquerda para a direita no topo do slide, depois comentar a tabela e a caixa vermelha.]*

"Entrando no experimento sistemático: toda matriz de dados em 50 dimensões passa primeiro por padronização via $z$-score variável a variável, para evitar que diferenças de escala dominem as distâncias. A partir daí, os dados seguem dois caminhos: ou vão diretamente sem redução para os quatro algoritmos de clusterização — formando nosso *baseline* —, ou passam por um dos cinco métodos de redução em três níveis de compressão recomendados na literatura.

Esses três níveis aparecem na tabela: o primeiro é $k - 1$, motivado pelo limite teórico de subespaço de Ding e He para separação de $k$ centróides — adotando piso 2 na implementação computacional, seguindo o protocolo de Amate e colaboradores para evitar degenerescência unidimensional; o segundo retém 25% da dimensão original, isto é, 12 variáveis; e o terceiro retém 50%, ou 25 variáveis.

Combinando 16 representações, 4 algoritmos de clusterização, 4 bases sintéticas e 1.000 réplicas independentes com sementes distintas, o experimento totaliza **256 mil execuções**, cronometradas individualmente com `proc.time()`."

---

### Slide 8 — Geração dos Dados Sintéticos em $d = 50$ `[07:15 – 07:55]`
> *[Contrastar o bloco da esquerda (Manifolds) com o da direita (Gaussianos) e finalizar no bloco inferior.]*

"As quatro bases sintéticas foram desenhadas para contrastar duas geometrias bem distintas. À esquerda temos duas variedades não-lineares clássicas: **Moons**, com 400 pontos em duas semiluas entrelaçadas, e **Circles**, com 400 pontos em dois anéis concêntricos — um caso particularmente desafiador porque os dois grupos compartilham exatamente o mesmo centróide na origem. À direita temos dois cenários esféricos e linearmente separáveis: **Gaussiano-3k**, com 240 observações em 3 grupos, e **Gaussiano-5k**, com 300 observações em 5 grupos.

O ponto-chave para interpretar todos os resultados que vêm a seguir está no bloco inferior: tanto nas variedades curvas geradas em 2D quanto nos grupos gaussianos, a estrutura informativa verdadeira vive em um subespaço de baixíssima dimensão, enquanto a imersão até 50 dimensões adiciona ruído gaussiano ortogonal que dilui a razão sinal-ruído."

---

### Slide 9 — Como os Dados Sintéticos São Gerados `[07:55 – 08:30]`
> *[Apontar brevemente para o esquema à esquerda (2D para 50D), depois para o da direita (amostragem direta em 50D) e finalizar na réplica Monte Carlo abaixo.]*

"De forma resumida, construímos esses conjuntos em dois procedimentos distintos. Para **Moons e Circles**, à esquerda, geramos primeiro as curvas em duas dimensões com um ruído pequeno e aplicamos uma matriz aleatória gaussiana $P$ de dimensão $2 \times 50$ para mergulhar os dados no espaço de 50 dimensões, somando ainda ruído isotrópico independente em todas as coordenadas.

Já para os cenários **Gaussianos**, à direita, sorteamos diretamente $k$ centróides em $\mathbb{R}^{50}$ e amostramos nuvens normais esféricas ao redor de cada centro. Em ambos os casos, padronizamos as colunas em $z$-score e repetimos todo o processo mil vezes com sementes diferentes — trocando pontos, projeção $P$ e ruído em cada réplica para medir a estabilidade real dos algoritmos."

---

### Slide 10 — Recuperação Visual em 2D: Referência, Linear e Kernel `[08:30 – 08:55]`
> *[Apontar para a primeira linha (2D original), depois para a segunda (PCA) e a terceira (Kernel PCA).]*

"Antes das métricas numéricas, dividimos em dois slides a grade 6 por 4 que projeta os dados de 50 dimensões de volta em 2D. Neste primeiro quadro, a linha superior traz a geometria nativa em 2D antes da imersão ruidosa.

Reparem que o **PCA**, na linha do meio, recupera muito bem os grupos gaussianos nas duas colunas da direita, mas apenas achata *Moons* e *Circles* sem criar separação linear. Já o **Kernel PCA**, na linha inferior, sofre bastante dispersão sob o ruído em 50 dimensões, deixando as fronteiras difusas."

---

### Slide 11 — Recuperação Visual em 2D: Métodos de Variedade (Manifold) `[08:55 – 09:20]`
> *[Apontar para Isomap (topo), MDS (meio) e UMAP (base), destacando especialmente a segunda coluna (Circles).]*

"Passando para os métodos baseados em variedade: o **Isomap** no topo ainda sofre dispersão e perde conectividade em algumas configurações, enquanto o **MDS**, no meio, reproduz exatamente o mesmo padrão linear do PCA.

Na última linha, o **UMAP** não apenas preserva os grupos como comprime fortemente a vizinhança local. Olhem em especial a segunda coluna de *Circles* no UMAP: ele abre e separa os dois anéis concêntricos em duas ilhas espacialmente disjuntas. Isso explica visualmente por que até algoritmos lineares ou baseados em centróide, como o K-Means, passam a acertar quase 100% em *Circles* após o UMAP."

---

### Slide 12 — Distribuição do ARI: K-Means `[09:20 – 10:00]`
> *[Apontar primeiro para os painéis Circles e Moons (barras douradas à direita de cada painel), depois para Gaussiano-3k e 5k.]*

"Passando agora para a distribuição do ARI nas mil réplicas em cada algoritmo, começamos pelo **K-Means**. Os quatro painéis separam os datasets e, dentro de cada painel, a primeira barra azul à esquerda é o *baseline* sem redução, seguida pelos cinco métodos nos três níveis.

Em *Circles*, sem redução ou com PCA, KPCA e MDS, o ARI do K-Means é essencialmente zero; com o UMAP — as três últimas caixas à direita — o ARI salta para perto de 1 com variância mínima. O mesmo salto ocorre em *Moons*, saindo de cerca de 0,48 para quase 1,00.

Em compensação, olhem os painéis *Gaussiano-3k* e *Gaussiano-5k* no centro: o K-Means sem redução já atinge ARI médio de 0,942 e 0,909 diretamente em 50 dimensões. Aqui, aplicar Isomap ou mesmo UMAP **prejudica** o K-Means, reduzindo o ARI para 0,877 e 0,812, porque a deformação não-linear distorce grupos esféricos que já eram linearmente separáveis."

---

### Slide 13 — Distribuição do ARI: AHC (Ward) `[10:00 – 10:40]`
> *[Apontar para Circles e Moons, e depois para o degrau positivo entre a barra azul (Sem redução) e PCA/MDS k-1 nos Gaussianos.]*

"No agrupamento hierárquico com critério de **Ward (AHC)**, o ganho em variedades curvas se repete de forma contundente: em *Circles*, o ARI médio sai de 0,007 — puro acaso — para **0,952** com UMAP; em *Moons*, sobe de 0,486 para **0,990**, com dispersão quase nula entre réplicas.

Mas há uma diferença interessante nos dados gaussianos em relação ao K-Means: o AHC sem redução sofre mais com o ruído acumulado nas fusões sucessivas, partindo de 0,75 no Gaussiano-3k. Por isso, tanto uma compressão linear agressiva — como PCA e MDS em $k-1$ — quanto o próprio UMAP trazem ganho consistente de ARI também nos cenários gaussianos."

---

### Slide 14 — Distribuição do ARI: OPTICS `[10:40 – 11:25]`
> *[Apontar para a ausência da barra 'Sem redução' e de 'PCA 50% / MDS 50%' no eixo horizontal.]*

"O terceiro gráfico traz o **OPTICS**, que ilustra de maneira direta o efeito da concentração de distâncias sobre métodos de densidade. Em 50 dimensões sem redução, todos os pontos têm distâncias de alcançabilidade quase constantes; o método não encontra vales de densidade e simplesmente não extrai clusters coerentes — por isso não há *baseline* em 50D, e mesmo no nível de 50% de retenção ($d = 25$) o PCA e o MDS ainda falham em extrair grupos.

Quando comprimimos de forma mais agressiva, especialmente no nível $k-1$, o OPTICS volta a operar bem, alcançando seu melhor ARI médio — **0,669** — justamente com PCA e MDS. Já com o UMAP, a contração não uniforme das densidades locais gera maior variabilidade para a extração de clusters pelo OPTICS."

---

### Slide 15 — Distribuição do ARI: GMM (EM) `[11:25 – 12:10]`
> *[Apontar para a primeira coluna zerada em todos os 4 painéis e depois para as colunas verdes/douradas.]*

"Fechando os boxplots, o **GMM** revelou ser o algoritmo mais vulnerável à dimensionalidade em todo o estudo. Observem a primeira posição de cada um dos quatro painéis: sem redução, em $d = 50$, o ARI médio do GMM é exatamente **0,000** em todos os cenários — inclusive nos próprios dados gaussianos! Isso ocorre porque o algoritmo EM precisa estimar matrizes de covariância em 50 dimensões dispondo de apenas 240 a 400 observações, gerando mau condicionamento numérico severo.

Quando reduzimos a dimensão, a recuperação é imediata: nos Gaussianos, PCA, KPCA e MDS em $k-1$ e 25% já restauram o modelo, embora voltem a degradar em 50% ($d=25$). E considerando todos os cenários, o **UMAP** recupera completamente o GMM, elevando o ARI médio global de zero para **0,876**."

---

### Slide 16 — Análise Agregada: Taxa de Vitória (WR%) e $\Delta$ARI `[12:10 – 12:50]`
> *[Apontar para a linha destacada do UMAP na tabela, para as linhas idênticas de PCA e MDS, e para a coluna com traços do OPTICS.]*

"Consolidando as 256 mil execuções nesta tabela, calculamos a **taxa de vitória** — a porcentagem de casos em que a redução supera o *baseline* sem redução — e o ganho médio $\Delta\text{ARI}$.

Três padrões saltam aos olhos. Primeiro, o **UMAP** é o único método com taxa de vitória acima de 50% para todos os algoritmos com *baseline*: 51% no K-Means (puxado pelos ganhos enormes nos manifolds), **97%** no AHC e **100%** de vitórias no GMM, com ganho médio de $+0{,}876$. Segundo, reparem que as linhas de **PCA e MDS** são numericamente idênticas em todos os algoritmos — consequência direta da equivalência algébrica demonstrada por Gower quando usamos distância euclidiana em dados padronizados. E terceiro, o OPTICS aparece sem valores comparativos porque, como vimos, não opera em 50 dimensões sem redução prévia."

---

### Slide 17 — Análise Agregada: ARI Médio por Par DR $\times$ Algoritmo `[12:50 – 13:20]`
> *[Apontar para a faixa verde superior do heatmap (UMAP) e depois para o bloco vermelho do GMM sem redução.]*

"Passando para o *heatmap* de ARI médio, temos a fotografia global de todas as combinações. A faixa verde no topo mostra que a melhor qualidade absoluta de todo o experimento pertence ao par **UMAP + K-Means (0,91)**, seguido muito de perto por **UMAP + AHC (0,89)** e **UMAP + GMM (0,88)**.

Também ficam evidentes dois contrastes importantes: o bloco vermelho embaixo confirma o colapso do **GMM sem redução (ARI = 0)**, enquanto as linhas de **PCA e MDS** permanecem exatamente iguais em todas as colunas — inclusive alcançando **0,66** no OPTICS."

---

### Slide 18 — Impacto do Nível de Compressão `[13:20 – 14:15]`
> *[Apontar para a curva azul (GMM) caindo em 50% no gráfico à esquerda e para os valores médios à direita.]*

"Mas quantas dimensões devemos reter? Este gráfico cruza o ARI médio por algoritmo ao longo dos três níveis: $k-1$, 25% e 50%.

De forma à primeira vista contraintuitiva, o nível **mais agressivo ($k-1$)** obteve o maior ARI médio global: **0,626**, contra 0,616 em 25% e uma queda para **0,539** em 50%. Olhando as curvas por algoritmo, K-Means e AHC são bastante estáveis, mas o **GMM** — na linha azul — despenca ao passar de 12 para 25 dimensões, porque o número de parâmetros livres de covariância volta a explodir.

A justificativa teórica vem diretamente de Ding e He (2004): como nossos grupos sintéticos têm estrutura intrínseca concentrada nas primeiras direções e ruído ortogonal puro no restante, comprimir até $k-1$ filtra o ruído máximo sem sacrificar o sinal discriminativo."

---

### Slide 19 — Custo Computacional por Etapa: Unidades Padronizadas `[14:15 – 14:55]`
> *[Apontar primeiro para o bloco superior (DR), depois para o inferior (Clusterização) e finalizar na linha do Total medido em horas.]*

"Qualidade de agrupamento, porém, não pode ser avaliada sem olhar para o custo de tempo. Nesta tabela padronizamos todas as médias por chamada em segundos e convertemos os totais acumulados em segundos, minutos e horas.

Somando todo o experimento, medimos **297 mil segundos — equivalentes a 4.950 minutos, ou 82,5 horas de processamento de CPU** (cerca de 3,44 dias). E reparem como esse tempo é concentrado: na redução de dimensionalidade, o **UMAP** consome 5 segundos por chamada, acumulando **16,69 horas** (mais de 20% do total), enquanto o **PCA** consome apenas 5 milésimos de segundo — somando **1,1 minuto** em todas as 12 mil chamadas, uma razão de **1.001 para 1** — e o MDS custa 20 vezes mais que o PCA entregando o mesmo ARI.

Já na clusterização, o gargalo absoluto é o **GMM**: com média de 3,56 segundos por execução por causa de convergências lentas do EM, ele consumiu **63,01 horas** — **76,4% de todo o experimento**. Somados, **GMM e UMAP explicam 96,6% do tempo total**."

---

### Slide 20 — Cruzamento de Custos (DR $\times$ Algoritmo) e Eficiência `[14:55 – 15:35]`
> *[Apontar para a fórmula no topo, depois para o canto superior esquerdo da tabela (PCA + AHC) e por fim comparar UMAP + K-Means vs. UMAP + GMM na linha inferior.]*

"Cruzando os cinco métodos de redução com os quatro algoritmos — e atribuindo a cada par um quarto do tempo da redução compartilhada mais o tempo de clusterização — obtemos esta matriz completa em segundos por execução e horas acumuladas.

Ela torna a escolha prática muito nítida. Na fronteira de baixo custo, no topo à esquerda, **PCA ou KPCA combinados ao AHC** rodam em menos de 50 milésimos de segundo por execução — menos de 10 minutos acumulados — entregando ARI médio competitivo. E na busca por máxima qualidade, na última linha, **UMAP + K-Means** gasta **4,20 horas** acumuladas e atinge o maior ARI do trabalho (**0,91**), enquanto **UMAP + GMM** consome **24,12 horas** — quase seis vezes mais tempo — para um ARI ligeiramente inferior (**0,88**)."

---

## 4. Experimentos com Dados Reais

### Slide 21 — Validação em Dados Reais: Repositório UCI `[15:35 – 16:15]`
> *[Apontar para a progressão da coluna $d$ na tabela (de 4 até 90), destacando WDBC e Libras.]*

"Para verificar se essas conclusões se sustentam fora do ambiente controlado de simulação, aplicamos exatamente o mesmo pipeline a seis bases clássicas do repositório UCI.

Selecionamos esses conjuntos para cobrir um gradiente intencional de dimensionalidade: desde bases pequenas com $d = 4$ e $d = 8$ — *Iris*, *Blood Transfusion* e *Yeast* —, passando por dimensão intermediária em *Wine* ($d = 13$), até bases de mais alta dimensão que se aproximam do regime sintético: **WDBC** (diagnóstico de câncer de mama, com $d = 30$) e **Libras Movement** (sinais da língua brasileira de sinais, com $d = 90$ e 15 classes). Diferente dos dados sintéticos, aqui enfrentamos ruído não gaussiano, correlação real entre variáveis e forte sobreposição natural de classes."

---

### Slide 22 — Estrutura em 2D dos Dados Reais (PCA vs. UMAP) `[16:15 – 17:00]`
> *[Apontar da esquerda para a direita comparando a linha superior (PCA) com a inferior (UMAP), destacando Wine, WDBC e Libras.]*

"Inspecionando visualmente esses seis conjuntos projetados em 2D por PCA na linha superior e por UMAP na linha inferior, já percebemos onde a redução não-linear faz diferença. Em *Blood Transfusion* e *Yeast*, as classes são intrinsecamente sobrepostas em qualquer projeção. Já conforme a dimensão cresce — em *Wine*, *WDBC* e principalmente nos 15 movimentos de *Libras* à extrema direita —, o UMAP consegue isolar subgrupos muito mais nítidos e compactos do que a combinação linear do PCA."

---

### Slide 23 — Resultados de Clusterização em Dados Reais `[17:00 – 18:15]`
> *[Apontar no gráfico de pirulitos para a linha inferior de painéis (Wine, WDBC e Libras) e depois para a linha superior (Iris, Blood, Yeast).]*

"Este gráfico de pirulitos resume o ARI obtido em cada base real, tomando o melhor nível de compressão para cada par método-algoritmo, em comparação à primeira faixa de baixo de cada painel ('Sem red.').

Os resultados confirmam diretamente a tendência dos experimentos sintéticos: **os ganhos relevantes concentram-se nos conjuntos de maior dimensão**. Na fileira inferior, em **WDBC ($d=30$)**, o par UMAP + AHC eleva o ARI de 0,575 para **0,773** — um ganho de quase 20 pontos —, e o OPTICS combinado ao Kernel PCA chega a ARI **1,000** nos pontos núcleo extraídos. Em **Libras ($d=90$)**, UMAP + AHC avança de 0,314 para **0,411**; e em **Wine ($d=13$)**, até a redução linear com PCA + AHC eleva o ARI de 0,790 para **0,896**.

Por outro lado, na fileira superior, onde a dimensão original já é baixa ($d = 4$ ou $8$), os ganhos são modestos, quase sempre abaixo de 0,06 — com exceção pontual do Isomap + OPTICS na base Iris. Ou seja, na prática real, reduzir dimensão traz retorno justamente quando a dimensionalidade observada é alta."

---

## 5. Conclusões e Recomendações

### Slide 24 — Síntese das Conclusões `[18:15 – 19:15]`
> *[Percorrer os 5 itens numerados com cadência firme, olhando para a banca.]*

"Caminhando para o encerramento, podemos sintetizar cinco conclusões principais do trabalho:

1. **O UMAP é dominante em estruturas de variedade não-linear**, sendo o único método capaz de destravar completamente cenários como *Circles* e *Moons*, com taxas de vitória entre 51% e 100%.
2. **Reduzir dimensão não é uma regra cega**: quando os grupos já são esféricos e bem separados — caso dos gaussianos com K-Means —, transformações não-lineares podem descartar sinal ou distorcer distâncias e reduzir o ARI, enquanto o AHC ainda se beneficia.
3. **PCA e MDS clássico são algebricamente e empiricamente equivalentes** em dados euclidianos padronizados; logo, o PCA deve ser sempre preferido por custar cerca de vinte vezes menos tempo.
4. **GMM e OPTICS são criticamente dependentes de redução prévia em alta dimensão**: sem ela, o GMM colapsa a ARI zero por mau condicionamento das matrizes de covariância no EM, e o OPTICS perde os gradientes de densidade.
5. **Os dados reais corroboram a teoria**: o benefício da redução cresce junto com a dimensionalidade observada do problema."

---

### Slide 25 — Recomendações Práticas e Considerações Finais `[19:15 – 20:00]`
> *[Apontar para o Guia Prático à esquerda e encerrar com as ressalvas à direita.]*

"Por fim, traduzindo esses resultados em um guia prático de decisão:
- Se estamos diante de **alta dimensão ou suspeita de não-linearidade** e buscamos qualidade máxima, a recomendação é **UMAP seguido de K-Means ou AHC** — evitando o GMM, que entrega ARI similar ao K-Means a um custo 356 vezes maior.
- Se há **restrição de tempo ou escala**, a fronteira mais eficiente é **PCA ou Kernel PCA combinado ao AHC**, rodando na casa dos milissegundos.
- E em **baixa dimensão**, deve-se sempre manter o agrupamento direto sem redução como *baseline* obrigatório.

Como ressalva metodológica honesta, destacamos que o sucesso do nível extremo $k-1$ nas simulações é favorecido pelo fato de o ruído sintético ser puramente ortogonal; em dados reais mais heterogêneos, testar níveis intermediários como 25% continua sendo prudente. Como trabalhos futuros, ficam a calibração automática de hiperparâmetros de vizinhança e a comparação direta com métodos profundos de *deep clustering*."

---

### Slide 26 — Encerramento `[20:00]`
> *[Avançar para o slide final, sorrir e agradecer.]*

"Agradeço muito a atenção de todos, em especial ao professor Uriel pela orientação ao longo deste trabalho e aos professores da banca pela leitura e pelas contribuições. Fico à disposição para as perguntas e para a arguição. Muito obrigado!"

---

## Guia de Bolso para a Arguição da Banca (Perguntas Prováveis)

1. **"No artigo de Amate et al. (2026), eles usaram VAE e disseram que não incluíram UMAP por ser estocástico. Por que você trocou o VAE pelo UMAP?"**
   - **Resposta segura:** *"Exatamente por isso essa escolha foi deliberada. No artigo original, o VAE teve desempenho instável e frequentemente inferior aos métodos clássicos, enquanto na prática aplicada — como bioinformática e ciência de dados — o UMAP é hoje o método não-linear mais utilizado antes de clusterizar. Para contornar o problema da estocasticidade apontado por Amate et al., fixamos a semente (`random_state`) e os hiperparâmetros padrão (`n_neighbors = 15`, `min_dist = 0,1`) ao longo das 1.000 réplicas controladas, garantindo total reprodutibilidade e mostrando que o UMAP supera amplamente os métodos testados no artigo base em variedades curvas."*

2. **"Por que no seu trabalho o nível $k-1$ foi o melhor nos dados sintéticos, enquanto em Amate et al. (2026) eles recomendaram 25%–50%?"**
   - **Resposta segura:** *"Há duas razões complementares. Primeiro, a arquitetura do ruído: nossos dados sintéticos concentram 100% do sinal discriminativo em 2 ou $k-1$ dimensões e adicionam ruído gaussiano isotrópico nas demais coordenadas até $d=50$; pelo teorema de Ding & He (2004), comprimir até o subespaço de dimensão $k-1$ (com piso computacional 2, `max(k-1, 2)`, igual ao protocolo de Amate et al.) elimina o máximo de ruído sem perder sinal. Já Amate et al. incluíram também geradores anisotrópicos e injeção heterogênea de ruído em 75% das features (incluindo $k$ até 50). Segundo, nos nossos próprios dados reais, selecionamos o melhor nível por combinação e observamos que níveis intermediários como 25% também aparecem com destaque quando o ruído não é puramente ortogonal — exatamente como ressaltamos nas limitações do Slide 25."*

3. **"Como o OPTICS + Kernel PCA conseguiu ARI = 1,000 (perfeito) no dataset real WDBC?"**
   - **Resposta segura:** *"Ótima pergunta — isso decorre do funcionamento do `extractXi` no OPTICS. Diferente de K-Means, AHC e GMM, que forçam a partição de 100% das observações, o OPTICS classifica pontos de fronteira/baixa densidade como ruído (rótulo zero). Seguindo o protocolo experimental implementado no código, o ARI do OPTICS é calculado sobre os pontos efetivamente atribuídos a clusters válidos (desde que haja ao menos 10 pontos agrupados). No WDBC projetado via Kernel PCA, os núcleos densos benigno e maligno ficaram perfeitamente puros, resultando em ARI = 1,000 nos pontos núcleo."*

4. **"Por que na tabela de custo computacional o desvio padrão do UMAP (296.791 ms) é muito maior que a média (5.007 ms)?"**
   - **Resposta segura:** *"Porque em algumas poucas execuções entre as 12.000 chamadas em paralelo no Windows (cluster PSOCK), a otimização estocástica em nível C do UMAP apresentou caudas pesadas / travamentos pontuais próximos ao limite de `timeout` configurado (`safe_call` de 120s), inflando o desvio padrão amostral, embora a mediana e a imensa maioria das execuções fiquem na casa de poucos segundos."*

5. **"Como funciona o piso `max(k-1, 2)` no nível $k-1$?"**
   - **Resposta segura:** *"Para $k=2$ grupos (como Moons, Circles, Blood Transfusion e WDBC), $k-1$ seria 1 dimensão. Seguindo exatamente a Seção 3.2 de Amate et al. (2026), aplicamos `max(k-1, 2)` na função `n_comp_nivel` do código para evitar projeções unidimensionais onde métodos baseados em grafos/variedades (como Isomap e UMAP) ou visualização bidimensional degeneram. Para $k=3$ usa-se 2 dimensões, para $k=5$ usam-se 4 dimensões, e para Libras ($k=15$) usam-se 14 dimensões."*
