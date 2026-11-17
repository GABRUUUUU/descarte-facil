# Metodologia e decisões do experimento

## Formulação

Regressão supervisionada para estimar contagens mensais de solicitações SP156 por subprefeitura. Cada linha representa uma combinação região-mês.

## Variável-alvo

Número de registros associados ao serviço de remoção de grandes objetos no mês. A classificação exata deve ser confirmada no dicionário e na extração utilizada.

## Atributos

Calendário (ano, mês, trimestre), defasagens de 1, 2, 3, 6 e 12 meses e média móvel dos três meses anteriores. As estatísticas históricas são deslocadas para evitar utilizar o valor do próprio mês como atributo.

## Validação

Separação temporal, mantendo os meses mais recentes para teste. O notebook inclui comparação exploratória de hiperparâmetros; para avaliação final robusta, o ajuste deve ocorrer em validação temporal interna ao conjunto de treino, reservando o teste final para avaliação única.

## Métricas

- MAE: erro absoluto médio em solicitações.
- RMSE: penaliza mais erros grandes.
- R²: proporção da variabilidade explicada.

## Limitações

Solicitações SP156 não são uma contagem completa dos descartes irregulares. Pode haver subnotificação, duplicidade e mudanças de classificação. A agregação por subprefeitura não permite previsão confiável por endereço ou CEP. O modelo não substitui o calendário oficial do Cata-Bagulho.
