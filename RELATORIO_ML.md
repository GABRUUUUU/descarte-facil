# Relatório de Machine Learning — Descarte Fácil

## 1. Problema e objetivo

O Descarte Fácil é uma proposta CitizenTech para simplificar o descarte correto de móveis, eletrodomésticos e outros objetos volumosos em São Paulo. O aplicativo pretende informar o calendário do Cata-Bagulho, orientar sobre a destinação correta e incentivar a doação de itens reutilizáveis.

A pergunta de Machine Learning é: **é possível prever a quantidade de solicitações de remoção de grandes objetos por subprefeitura e mês, utilizando o histórico público do SP156?**

O problema é tratado como regressão. A previsão de demanda registrada pode ajudar a reconhecer regiões e períodos que merecem maior atenção em ações de comunicação e planejamento. O modelo não prevê o dia exato de coleta de uma rua nem mede diretamente todo o descarte irregular.

## 2. Dados e preparação

A fonte selecionada é a base **Dados do SP156**, publicada no Portal de Dados Abertos da Prefeitura de São Paulo: https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156.

O conjunto reúne solicitações registradas pelos canais SP156. Para este estudo, devem ser selecionados os registros cujo serviço/assunto corresponda à remoção de grandes objetos. A extração utilizada, a data de download e a versão do dicionário de dados devem ser anotadas durante a execução.

A preparação consiste em:
1. carregar o arquivo CSV oficial;
2. conferir e padronizar os campos de data, serviço/assunto e subprefeitura;
3. filtrar o serviço de remoção de grandes objetos;
4. agregar as solicitações por subprefeitura e mês;
5. construir atributos temporais e históricos.

A unidade de análise é **subprefeitura-mês** e a variável-alvo é a contagem mensal de solicitações. Entre os atributos explicativos estão mês, trimestre, ano, valores defasados (1, 2, 3, 6 e 12 meses) e média móvel dos três meses anteriores.

## 3. Modelagem e avaliação

O notebook compara três abordagens:
- **Baseline:** utiliza a quantidade do mês anterior como previsão;
- **Random Forest Regressor:** testa combinações de número de árvores, profundidade e tamanho mínimo das folhas;
- **XGBoost Regressor:** testa combinações de número de estimadores, profundidade e taxa de aprendizado.

A divisão entre treino e teste é cronológica: os períodos mais antigos são utilizados no treinamento e os meses finais são reservados para teste. Essa estratégia é mais adequada a uma previsão temporal do que embaralhar aleatoriamente os registros.

As métricas são MAE (erro absoluto médio), RMSE (raiz do erro quadrático médio) e R². O MAE informa, em média, quantas solicitações separam a previsão do valor observado. O RMSE penaliza mais os erros grandes. O R² resume quanto da variação observada é explicada pelo modelo.

### Resultados experimentais

> **Preencher somente após executar o notebook com a base oficial. Não inserir métricas estimadas ou fictícias.**

| Modelo | MAE | RMSE | R² | Hiperparâmetros |
|---|---:|---:|---:|---|
| Baseline (mês anterior) | A preencher | A preencher | A preencher | — |
| Random Forest | A preencher | A preencher | A preencher | A preencher |
| XGBoost | A preencher | A preencher | A preencher | A preencher |

**Modelo selecionado:** a preencher.

**Interpretação dos resultados:** informar se os modelos superaram a linha de base, qual apresentou menor MAE no teste e quais atributos tiveram maior importância. Analisar também o gráfico de valores reais versus previstos e eventuais erros elevados em regiões específicas.

## 4. Conclusão e impacto para o cidadão

Caso o modelo apresente desempenho superior à linha de base no período de teste, isso será um indício de que o histórico do SP156 contém padrões temporais e regionais úteis para estimar a demanda futura por remoção de grandes objetos.

No contexto do Descarte Fácil, essa informação pode apoiar campanhas de orientação e priorização de comunicação em regiões com maior demanda registrada. A aplicação deve apresentar essas estimativas como indicadores de contexto, sem substituir o calendário oficial do Cata-Bagulho ou prometer que uma coleta ocorrerá em determinada data.

As principais limitações são a subnotificação, a possibilidade de múltiplas solicitações para um mesmo problema, alterações de nomenclatura dos serviços e a granularidade por subprefeitura. Por isso, os resultados não devem ser interpretados como contagem de todos os descartes irregulares nem como previsão individual por CEP ou endereço.

Uma evolução possível é combinar o histórico do SP156 com calendário oficial do Cata-Bagulho, dados georreferenciados de pontos viciados e indicadores territoriais do IBGE. Isso permitiria análises mais granulares e uma integração mais direta com as funcionalidades do aplicativo.

## Reprodutibilidade

O notebook em `ml/descarte_facil_ml.ipynb` documenta o carregamento, a preparação, o treinamento e a avaliação. Para concluir a entrega, execute todas as células, registre a origem e a data da extração, confira os campos com o dicionário oficial e transcreva os resultados reais nesta seção.
