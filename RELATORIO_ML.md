# Relatório de Machine Learning — Descarte Fácil

## 1. Problema e objetivo

O Descarte Fácil é uma proposta CitizenTech para simplificar o descarte correto de móveis, eletrodomésticos e outros objetos volumosos em São Paulo. A solução pretende informar o calendário do Cata-Bagulho, orientar sobre a destinação correta e incentivar a doação de itens reutilizáveis.

A pergunta investigada é: **é possível estimar a quantidade de solicitações de remoção de grandes objetos por região e mês, utilizando o histórico público do SP156?**

O problema é tratado como regressão. A previsão de demanda registrada pode ajudar a reconhecer regiões e períodos que merecem maior atenção em ações de comunicação e planejamento. O modelo não prevê o dia exato de coleta de uma rua nem mede diretamente todo o descarte irregular.

## 2. Dados e preparação

A fonte utilizada foi a base **Dados do SP156**, publicada no Portal de Dados Abertos da Prefeitura de São Paulo: https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156.

A extração carregada pelo notebook contém **484.232 registros e 15 colunas**. O campo geográfico reconhecido no arquivo foi **Distrito**. Portanto, a unidade efetivamente utilizada no experimento foi **distrito-mês**, e não subprefeitura-mês. Essa distinção é importante: o relatório e a interpretação dos resultados devem manter a granularidade realmente presente nos dados.

O filtro selecionou solicitações cujo serviço contém termos relacionados a “entulho e grandes objetos”. Os registros foram agregados por distrito e mês, e meses sem registros observados foram preenchidos com zero. Foram criadas variáveis de calendário (ano, mês e trimestre), defasagens de 1, 2 e 3 meses e média móvel dos dois meses anteriores.

**Atenção à data:** os exemplos exibidos no CSV incluem valores como `07/01/2026` e `07/25/2026`, compatíveis com o formato mês/dia/ano. O notebook atualmente utiliza `dayfirst=True` na conversão. Antes de tratar as métricas como definitivas, é necessário corrigir a leitura para o formato confirmado no arquivo (provavelmente `format="%m/%d/%Y"` ou `dayfirst=False`) e conferir o intervalo mínimo e máximo após a conversão. Uma interpretação incorreta das datas pode alterar a agregação mensal e os resultados.

## 3. Modelagem e resultados registrados

O notebook compara três abordagens:
- **Baseline:** utiliza a quantidade do mês anterior como previsão;
- **Random Forest Regressor:** testa diferentes números de árvores, profundidades e tamanhos mínimos de folha;
- **XGBoost Regressor:** testa diferentes números de estimadores, profundidades e taxas de aprendizado.

A divisão registrada é cronológica, com corte em novembro de 2026: 672 observações no treino e 192 no teste. As métricas registradas no notebook são:

| Modelo | MAE | RMSE | R² |
|---|---:|---:|---:|
| Random Forest | **3,873** | 6,608 | 0,605 |
| XGBoost | 4,070 | **6,496** | **0,618** |
| Baseline (mês anterior) | 4,813 | 8,645 | 0,323 |

O Random Forest obteve o menor MAE, com erro absoluto médio de aproximadamente **3,87 solicitações por distrito/mês**. O XGBoost apresentou o menor RMSE e o maior R². Ambos tiveram métricas melhores que a linha de base nesta execução.

### Ressalva metodológica importante

Na versão executada do notebook, os hiperparâmetros foram escolhidos comparando o MAE diretamente no conjunto de teste. Isso significa que o teste foi consultado durante a seleção dos modelos e deixou de ser uma avaliação final totalmente independente. Assim, os números acima devem ser apresentados como **resultados exploratórios desta execução**, não como uma estimativa imparcial de desempenho futuro. Para uma avaliação final mais robusta, a seleção de hiperparâmetros deve ocorrer em uma validação temporal interna ao treino, mantendo os meses finais intocados até a avaliação final.

Além disso, a extração cobre um intervalo curto e a conversão de datas precisa ser validada. Essas limitações podem afetar a estabilidade das métricas.

## 4. Conclusão e impacto para o cidadão

Nesta execução exploratória, Random Forest e XGBoost superaram a previsão simples baseada no mês anterior. O Random Forest foi o melhor segundo o MAE, enquanto o XGBoost apresentou RMSE ligeiramente menor e R² maior. Isso sugere que os atributos temporais e regionais podem conter informação útil para estimar a demanda registrada no SP156.

No contexto do Descarte Fácil, uma previsão validada pode apoiar campanhas de orientação e planejamento em distritos com maior volume esperado de solicitações. A aplicação deve apresentar estimativas como indicadores de contexto, sem substituir o calendário oficial do Cata-Bagulho ou prometer que uma coleta ocorrerá em determinada data.

As principais limitações são a subnotificação, a possibilidade de múltiplas solicitações para um mesmo problema, alterações de nomenclatura dos serviços, o período histórico curto e a granularidade por distrito. Os resultados não representam todos os descartes irregulares e não devem ser interpretados como previsão individual por CEP ou endereço.

## 5. Próximas etapas para consolidar a entrega

1. Corrigir e validar a interpretação das datas no notebook.
2. Confirmar que o período mensal e a quantidade de registros por mês correspondem ao arquivo oficial.
3. Ajustar a seleção de hiperparâmetros usando validação temporal dentro do treino, sem consultar o teste.
4. Executar novamente o notebook e substituir a tabela exploratória pelos resultados da avaliação final.
5. Manter a nomenclatura **distrito** no relatório, pois esse é o campo geográfico usado na execução atual.

## Reprodutibilidade

O notebook executado está em `ml/descarte_facil_ml.ipynb`. A fonte oficial e o código de preparação, treinamento e avaliação estão documentados nele. Registre a data de download e a versão do arquivo SP156 utilizado para facilitar a reprodução do experimento.
