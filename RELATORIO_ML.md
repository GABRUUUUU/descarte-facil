# Relatório de Machine Learning — Descarte Fácil

## 1. Problema e objetivo

O Descarte Fácil é uma proposta CitizenTech para simplificar o descarte correto de móveis, eletrodomésticos e outros objetos volumosos em São Paulo. A solução pretende informar o calendário do Cata-Bagulho, orientar sobre a destinação correta e incentivar a doação de itens reutilizáveis.

A pergunta investigada é: **é possível estimar a quantidade de solicitações de remoção de grandes objetos por distrito e mês, utilizando o histórico público do SP156?**

O problema é tratado como regressão. A previsão de demanda registrada pode ajudar a reconhecer regiões e períodos que merecem maior atenção em ações de comunicação e planejamento. O modelo não prevê o dia exato de coleta de uma rua nem mede diretamente a quantidade real de descarte irregular.

## 2. Dados e preparação

A fonte é a base **Dados do SP156**, publicada no Portal de Dados Abertos da Prefeitura de São Paulo: https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156.

O portal informa que as solicitações disponíveis abrangem o período de 1º de janeiro de 2012 a 30 de setembro de 2026 e são publicadas em arquivos trimestrais. Para este experimento, o notebook foi preparado para combinar oito arquivos trimestrais consecutivos, do 4º trimestre de 2024 ao 3º trimestre de 2026. Isso amplia o histórico em relação à primeira versão, que utilizava apenas o 3º trimestre de 2026.

A execução verifica se todos os oito arquivos foram carregados. Se algum trimestre falhar, o notebook interrompe o processamento para evitar que um período sem arquivo seja confundido com um mês sem solicitações. O número final de registros e a cobertura temporal efetiva devem ser anotados após a execução.

O campo geográfico utilizado é **Distrito**. Portanto, a unidade de análise é **distrito-mês**, e não subprefeitura-mês. O filtro seleciona serviços cuja nomenclatura contém termos relacionados a grandes objetos e entulho. A lista de serviços encontrados é exibida no notebook para permitir a conferência do recorte.

As datas são convertidas com validação da proporção de valores reconhecidos. O notebook tenta primeiro o padrão mês/dia/ano, observado nos arquivos recentes, e testa o padrão dia/mês/ano se a taxa de conversão inicial for baixa.

Os dados são agregados por distrito e mês. O painel mensal é balanceado dentro do intervalo carregado, e meses sem solicitações observadas são preenchidos com zero. São criadas variáveis de calendário (ano, mês e trimestre) e defasagens de 1, 2 e 3 meses.

A avaliação é temporal: os seis meses mais recentes são reservados para teste; os seis meses imediatamente anteriores são usados para validação; e os meses anteriores compõem o treino. Os hiperparâmetros são escolhidos somente na validação. Em seguida, os modelos são reajustados com treino e validação e avaliados uma única vez no teste.

## 3. Modelos e avaliação

O notebook compara três abordagens:

- **Baseline lag-1:** utiliza a quantidade do mês anterior como previsão.
- **Random Forest Regressor:** testa diferentes números de árvores, profundidades e tamanhos mínimos de folha.
- **XGBoost Regressor:** testa diferentes números de estimadores, profundidades e taxas de aprendizado.

As métricas utilizadas são MAE, RMSE e R². O MAE representa o erro absoluto médio em número de solicitações por distrito-mês. A comparação com o baseline é importante para verificar se os modelos de machine learning realmente acrescentam capacidade preditiva em relação a uma regra simples.

**Os resultados numéricos devem ser preenchidos após executar a versão atualizada do notebook.** Não devem ser reutilizadas as métricas da execução anterior: aquela versão utilizava somente três meses, compartilhava o mesmo mês entre treino e validação e não permitia uma avaliação temporal independente.

Após a execução, registrar:
- quantidade de registros e trimestres efetivamente carregados;
- período coberto e número de distritos;
- meses e linhas em treino, validação e teste;
- MAE, RMSE e R² dos três métodos;
- melhor conjunto de hiperparâmetros e atributos mais relevantes.

## 4. Aplicação no Descarte Fácil

Uma previsão validada pode apoiar campanhas de orientação e planejamento em distritos com maior volume esperado de solicitações. No aplicativo, a informação deve ser apresentada como indicador agregado de contexto, sem substituir o calendário oficial do Cata-Bagulho ou prometer que uma coleta ocorrerá em determinada data.

## 5. Limitações

Os dados do SP156 representam solicitações registradas pelos cidadãos, e não todos os descartes irregulares existentes. Uma pessoa pode abrir mais de uma solicitação para um mesmo problema, e a Prefeitura pode executar serviços sem que exista um protocolo correspondente.

A Prefeitura também informa que os serviços podem mudar de nome, ser reclassificados e receber protocolos incluídos em republicações. Essas alterações exigem cuidado ao combinar arquivos de períodos diferentes. O filtro textual de serviços deve ser conferido na saída do notebook.

A análise é feita por distrito, não por CEP ou endereço. Além disso, a avaliação representa uma previsão mensal de um passo à frente, usando os valores observados dos meses anteriores; não equivale a uma previsão de vários meses futuros sem atualização dos dados.

## Reprodutibilidade

O notebook está em `ml/descarte_facil_ml.ipynb`. Ele contém os links dos arquivos trimestrais, a coleta, a preparação, o treinamento e a avaliação. A fonte oficial deve ser consultada novamente caso a Prefeitura atualize os recursos ou altere os endereços dos arquivos.

Execute todas as células de cima para baixo e atualize este relatório com os resultados efetivamente obtidos.