# Árvore, floresta ou boosting? Prevendo a degradação de uma rede 12 minutos antes

Comparação de **Árvore de Decisão**, **Random Forest** e **XGBoost** num problema real de redes: prever se um caminho da internet estará **OK**, em **RISCO** ou em **FALHA** daqui a 12 minutos, a partir de medições de ping do [RIPE Atlas](https://atlas.ripe.net/). Os três modelos são comparados entre si e com a regra mais simples possível, a **persistência** ("daqui a 12 minutos vai estar como está agora").

**Artigo:** <https://iedahirari.github.io/artigo-preditor-redes/> (arquivo [`index.html`](index.html))

## Estrutura

| Pasta / arquivo | Conteúdo |
|---|---|
| `index.html` | O artigo (página estática, publicada pelo GitHub Pages) |
| `notebooks/01_preparacao_dados.ipynb` | Baixa os dados do projeto integrador, remove os IPs, cria o alvo "classe daqui a 12 minutos" e separa treino, validação e teste |
| `notebooks/02_modelos.ipynb` | Ajusta os três modelos com o mesmo esforço, compara com a persistência, mede no teste e gera as figuras |
| `img/` | Figuras geradas pelo notebook 02 e fotos dos autores |
| `resultados/` | Métricas de teste, histórico do ajuste, F1 por tipo de caminho e a árvore escolhida |
| `dados/` | Dados preparados pelo notebook 01. Fica fora do Git (veja o `.gitignore`) |
| `requirements.txt` | Bibliotecas Python usadas |

## Como reproduzir

```bash
python -m venv .venv
.venv\Scripts\activate        # no Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

Rode os notebooks **em ordem**:

1. `01_preparacao_dados.ipynb` baixa o arquivo `dataset_rotulado_B.csv.gz` do repositório do projeto integrador ([ZorahSai/Projeto-WebAgriculture-](https://github.com/ZorahSai/Projeto-WebAgriculture-)) e grava `dados/dataset_artigo.csv.gz`.
2. `02_modelos.ipynb` treina, avalia e grava as figuras e os resultados.

Com a semente 42 as métricas saem iguais. Os tempos (treino, predição e latência) variam um pouco de uma execução para outra.

**Ambiente usado:** Python 3.14.7, numpy 2.5.4, pandas 3.0.6, scikit-learn 1.9.1, xgboost 3.4.1, matplotlib 3.11.2.

## Os dados

- **Origem:** medições públicas de ping IPv4 entre Anchors do RIPE Atlas, de 13/09/2026 04:32 a 20/09/2026 04:32 UTC (Período B do projeto). São 92 fluxos (sonda de origem → servidor de destino), com RTT normal de 1 ms a 315 ms.
- **Rótulo:** a classe de cada medição (OK, RISCO ou FALHA) vem da regra do projeto, que compara a medição com o normal do próprio fluxo, estimado na semana anterior (mediana e MAD do RTT).
- **Divisão:** treino, validação e teste em ordem de tempo, iguais às do projeto.
- **Privacidade:** o notebook 01 troca cada fluxo por um código (`F01` a `F92`) e remove os endereços IP antes de salvar qualquer arquivo. Nenhum IP é publicado neste repositório nem na página.

Os dados seguem os [RIPE Atlas Terms and Conditions](https://www.ripe.net/about-us/legal/ripe-atlas-service-terms-and-conditions/): uso para pesquisa é permitido, com crédito ao RIPE NCC.

## Acessibilidade e responsividade da página

- Layout de uma coluna que se adapta de 320 px (celular) a telas largas, sem rolagem lateral. Testado em 375 px (celular), 768 px (tablet) e 1440 px (desktop), nos temas claro e escuro.
- Figuras largas (árvore, matrizes de confusão, importâncias, métricas) deslizam para o lado no celular e abrem em tamanho original com um toque. Tabelas largas rolam dentro da própria caixa, com sombra indicando que há mais conteúdo.
- Link "Pular para o conteúdo", foco visível no teclado, alvos de toque de pelo menos 44 px nos menus e botões, textos alternativos em todas as figuras e respeito a "reduzir movimento".
- Escrita para funcionar em Chrome, Edge, Firefox e Safari, inclusive no iPhone (os testes acima foram feitos num navegador baseado em Chromium): as faixas de largura total não usam `100vw`, há alternativa para navegadores sem `overflow: clip`, e o texto não é ampliado sozinho no iPhone.
- Imagens com largura e altura declaradas e carregamento sob demanda; fontes Roboto e Roboto Mono do Google Fonts; ícone, cor da barra do navegador e prévia para compartilhamento (Open Graph).

## Resultados no teste (classe daqui a 12 minutos)

| Modelo | F1 macro | Recall de FALHA | Precisão de FALHA | Alarmes falsos de FALHA | Recall de início de FALHA |
|---|---|---|---|---|---|
| Árvore de Decisão | 0,759 | 0,711 | 0,913 | 650 | 0,002 |
| Random Forest | 0,763 | 0,698 | 0,937 | 446 | 0,002 |
| XGBoost | 0,765 | 0,697 | 0,937 | 446 | 0,002 |
| Persistência | 0,753 | 0,792 | 0,791 | 2.008 | 0,000 |

Os três modelos ficam pouco acima da persistência e nenhum antecipa o início de uma FALHA. A discussão completa, os tempos e a recomendação estão no artigo.

## Autores

- Apollo Moura De Sousa ([@apollomoura](https://github.com/apollomoura) · [LinkedIn](https://www.linkedin.com/in/apollo-moura-215306320/))
- Icaro Luiz Dellalo Silva ([@clicaro](https://github.com/clicaro) · [LinkedIn](https://www.linkedin.com/in/icaro-dellalo/))
- Ieda de Oliveira ([@iedahirari](https://github.com/iedahirari) · [LinkedIn](https://www.linkedin.com/in/ieda-oliveira/))
- João Pedro C. de Morais ([@ZorahSai](https://github.com/ZorahSai) · [LinkedIn](https://www.linkedin.com/in/jo%C3%A3o-pedro-06724430b/))
- Lucas Matteus Baptista de Godoy ([@SuperLucas2000](https://github.com/SuperLucas2000) · [LinkedIn](https://www.linkedin.com/in/lucasmatteus/))

## Referências e créditos

- Breiman, L. (2001). Random Forests. *Machine Learning*, 45(1), 5–32.
- Chen, T.; Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System. *KDD '16*. [arXiv:1603.02754](https://arxiv.org/abs/1603.02754)
- Friedman, J. H. (2001). Greedy Function Approximation: A Gradient Boosting Machine. *The Annals of Statistics*, 29(5).
- Leys, C. et al. (2013). Detecting outliers: Do not use standard deviation around the mean, use absolute deviation around the median. *Journal of Experimental Social Psychology*, 49(4), 764–766.
- Sakai, A. O. (2026). Preditor de Qualidade de Redes com Machine Learning. [github.com/andreaono/preditor_redes](https://github.com/andreaono/preditor_redes)
- Documentação do [XGBoost](https://xgboost.readthedocs.io/en/stable/tutorials/model.html), do [scikit-learn](https://scikit-learn.org/stable/modules/tree.html) e da [API do RIPE Atlas](https://atlas.ripe.net/docs/apis/rest-api-manual/).
- Dados: [RIPE Atlas](https://atlas.ripe.net/) (RIPE NCC). Coleta e rótulo: projeto integrador [Projeto-WebAgriculture-](https://github.com/ZorahSai/Projeto-WebAgriculture-).
- Figuras geradas pelos autores a partir dos notebooks. Texto e figuras sob [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.pt-br). Código sob licença MIT (arquivo `LICENSE`).
