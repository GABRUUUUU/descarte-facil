# Relatório de Machine Learning — Descarte Fácil

## 1. Problema e objetivo

O Descarte Fácil é uma proposta CitizenTech para simplificar o descarte correto de móveis, eletrodomésticos e outros objetos volumosos em São Paulo. A solução pretende informar o calendário do Cata-Bagulho, orientar sobre a destinação correta e incentivar a doação de itens reutilizáveis.

A pergunta investigada é: **é possível estimar a quantidade de solicitações de remoção de grandes objetos por região e mês, utilizando o histórico público do SP156?**

O problema é tratado como regressão. A previsão de demanda registrada pode ajudar a reconhecer regiões e períodos que merecem maior atenção em ações de comunicação e planejamento. O modelo não prevê o dia exato de coleta de uma rua nem mede diretamente todo o descarte irregular.

## 2. Dados e preparação

A fonte utilizada foi a base **Dados do SP156**, publicada no Portal de Dados Abertos da Prefeitura de São Paulo: https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156.

A extração carregada pelo notebook contém **484.232 registros e 15 colunas**. O campo geográfico reconhecido no arquivo foi **Distrito**. Portanto, a unidade efetivamente utilizada no experimento foi **distrito-mês**, e não subprefeitura-mês. Essa distinção é importante: o relatório e a interpretação dos resultados devem manter a granularidade realmente presente nos dados.

O filtro selecionou solicitações cujo serviço contém termos relacionados a “entulho e grandes objetos”. Os registros foram agregados por distrito e mês, e meses sem registros observados foram preenchidos com zero. Foram criadas variáveis de calendário (ano, mês e trimestre), defasagens de 1, 2 e 3 meses e média móvel dos dois meses anteriores.

**Tratamento da data:** a coluna `Data de Abertura` foi conferida no CSV. Valores como `07/25/2026` confirmam o padrão mês/dia/ano (MM/DD/YYYY), pois 25 não pode ser um mês. O notebook foi corrigido para interpretar explicitamente esse formato e interromper a execução se menos de 95% das datas forem reconhecidas.

O notebook compara três abordagens:
- **Baseline:** utiliza a quantidade do mês anterior como previsão;
- **Random Forest Regressor:** testa diferentes números de árvores, profundidades e tamanhos mínimos de folha;
- **XGBoost Regressor:** testa diferentes números de estimadores, profundidades e taxas de aprendizado.

A avaliação foi corrigida para separar os meses cronologicamente em treino, validação e teste. Os hiperparâmetros são escolhidos exclusivamente na validação; em seguida, cada modelo é reajustado com treino + validação e avaliado uma única vez no teste.

**Os resultados numéricos anteriores foram retirados desta versão do relatório:** eles vieram de uma execução com interpretação de datas incorreta e seleção de hiperparâmetros diretamente no teste, portanto não são comparáveis nem devem ser apresentados como desempenho do pipeline corrigido. O notebook foi atualizado e teve as saídas antigas removidas. É necessário executar todas as células novamente para gerar métricas válidas; depois disso, a tabela final e a interpretação devem ser inseridas aqui.

O experimento foi estruturado para comparar Random Forest e XGBoost com uma linha de base simples, utilizando avaliação temporal sem consultar o teste durante a seleção de hiperparâmetros. A conclusão sobre qual modelo é superior só deve ser feita após executar novamente o notebook corrigido e analisar as métricas finais.

No contexto do Descarte Fácil, uma previsão validada pode apoiar campanhas de orientação e planejamento em distritos com maior volume esperado de solicitações. A aplicação deve apresentar estimativas como indicadores de contexto, sem substituir o calendário oficial do Cata-Bagulho ou prometer que uma coleta ocorrerá em determinada data.

As principais limitações são a subnotificação, a possibilidade de múltiplas solicitações para um mesmo problema, alterações de nomenclatura dos serviços, o período histórico curto e a granularidade por distrito. Os resultados não representam todos os descartes irregulares e não devem ser interpretados como previsão individual por CEP ou endereço.

1. Executar todas as células do notebook atualizado, de cima para baixo.
2. Conferir o período mensal, a distribuição das solicitações e os tamanhos dos conjuntos de treino, validação e teste.
3. Registrar no relatório as métricas da avaliação final (MAE, RMSE e R²) e comparar os modelos com a linha de base.
4. Interpretar os resultados considerando que o histórico disponível é curto.
5. Manter a nomenclatura **distrito**, pois esse é o campo geográfico usado na análise.

## Reprodutibilidade

O notebook executado está em `ml/descarte_facil_ml.ipynb`. A fonte oficial e o código de preparação, treinamento e avaliação estão documentados nele. Registre a data de download e a versão do arquivo SP156 utilizado para facilitar a reprodução do experimento.
