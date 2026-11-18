# Relatório de Machine Learning — Descarte Fácil

## 1. Problema e objetivo

O Descarte Fácil é uma proposta CitizenTech para simplificar o descarte correto de móveis, eletrodomésticos e outros objetos volumosos em São Paulo. A solução pretende informar o calendário do Cata-Bagulho, orientar sobre a destinação correta e incentivar a doação de itens reutilizáveis.

A pergunta investigada é: **é possível estimar a quantidade mensal de solicitações de remoção de grandes objetos por distrito, utilizando os registros públicos do SP156?**

O problema é formulado como regressão supervisionada. A estimativa de demanda registrada pode apoiar a identificação de regiões que merecem atenção em ações de comunicação e planejamento. O modelo não prevê o dia exato de coleta de uma rua e não mede diretamente todo o descarte irregular.

## 2. Dados e preparação

A fonte é a base **Dados do SP156**, publicada no Portal de Dados Abertos da Prefeitura de São Paulo: [dados.prefeitura.sp.gov.br/dataset/dados-do-sp156](https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156).

A extração utilizada contém **484.232 registros e 15 colunas**. O campo geográfico disponível no arquivo é **Distrito**; portanto, a unidade efetivamente analisada é distrito-mês, e não subprefeitura-mês. O filtro seleciona serviços cujo nome contém termos relacionados a “entulho e grandes objetos”. Os registros são agregados por distrito e mês, e combinações sem solicitações observadas recebem zero.

A coluna `Data de Abertura` foi conferida e segue o padrão mês/dia/ano (MM/DD/YYYY), confirmado por valores como `07/25/2026`. O notebook converte as datas com esse formato explícito e interrompe a execução se menos de 95% delas forem reconhecidas.

## 3. Variáveis e metodologia

A variável-alvo é a contagem de solicitações registradas em cada distrito durante o mês. Os atributos utilizados são o distrito e variáveis de calendário (ano, mês e trimestre).

A extração atualmente configurada no notebook corresponde ao terceiro trimestre de 2026 e, na execução analisada, contém somente julho, agosto e setembro. Por isso, **não são utilizadas variáveis defasadas**: aplicar um lag de um mês eliminaria julho e deixaria somente agosto e setembro, impossibilitando separar treino, validação e teste independentes.

A divisão é cronológica:
- **Treino:** julho de 2026;
- **Validação:** agosto de 2026;
- **Teste:** setembro de 2026.

São comparados três métodos:
- **Baseline:** utiliza, para cada distrito, a quantidade observada no mês imediatamente anterior;
- **Random Forest Regressor:** seleção de hiperparâmetros na validação;
- **XGBoost Regressor:** seleção de hiperparâmetros na validação.

Após a seleção, os modelos são reajustados com treino e validação e avaliados uma única vez no teste. As métricas são MAE (erro absoluto médio), RMSE (raiz do erro quadrático médio) e R². O baseline é indispensável: modelos mais complexos só demonstram utilidade se apresentarem desempenho superior a essa referência simples.

## 4. Resultados e interpretação

**Os resultados numéricos precisam ser gerados pela execução integral da versão corrigida do notebook.** As métricas da execução anterior não são reutilizadas, pois aquela versão compartilhava o mesmo mês entre treino e validação, tornando a escolha de hiperparâmetros inválida. As saídas do notebook foram limpas para evitar que resultados antigos sejam confundidos com a nova avaliação.

Após executar todas as células, preencher a tabela abaixo com os valores exibidos pelo notebook:

| Modelo | MAE | RMSE | R² |
|---|---:|---:|---:|
| Baseline (mês anterior) | [preencher] | [preencher] | [preencher] |
| Random Forest | [preencher] | [preencher] | [preencher] |
| XGBoost | [preencher] | [preencher] | [preencher] |

O melhor modelo deve ser identificado principalmente pela comparação do MAE com o baseline, considerando também RMSE e R². Se nenhum modelo superar o baseline, a conclusão correta é que, neste recorte, a complexidade adicional não demonstrou benefício.

## 5. Aplicação no Descarte Fácil

Caso a avaliação indique desempenho superior ao baseline, a estimativa poderá ser usada como indicador exploratório para apoiar a priorização de campanhas informativas e o planejamento de ações em distritos com maior volume esperado de solicitações. A informação deve ser apresentada como estimativa agregada, nunca como garantia de coleta em determinada data ou endereço. O calendário oficial do Cata-Bagulho continua sendo a referência para o cidadão.

## 6. Limitações e próximos passos

A principal limitação é a cobertura temporal: três meses permitem apenas um mês em cada partição, com teste de um único mês e aproximadamente 96 observações distritais. Essa amostra é pequena e sensível a eventos pontuais; portanto, não sustenta uma conclusão forte sobre generalização para meses futuros. A seleção de hiperparâmetros também é pouco estável com apenas um mês de validação.

Além disso, solicitações SP156 dependem de conhecimento e iniciativa do cidadão, podem existir registros duplicados e a classificação dos serviços pode mudar. O campo Distrito é menos específico que CEP ou endereço. Assim, os dados não representam a totalidade dos descartes irregulares nem permitem afirmar onde um descarte ocorrerá.

**Próximo passo recomendado:** obter uma série histórica de vários anos, padronizar as mudanças de nomenclatura do serviço e realizar validação temporal com múltiplas janelas, mantendo um período final independente para teste. Só depois disso seria adequado discutir uso operacional do modelo.

## Reprodutibilidade

O código, a URL da fonte, as etapas de preparação e a avaliação estão no notebook `ml/descarte_facil_ml.ipynb`. Para reproduzir: instalar as dependências listadas em `requirements.txt`, abrir o notebook no Google Colab ou Jupyter e executar todas as células em ordem. A tabela de resultados desta seção deve ser preenchida com a execução efetiva da versão corrigida.