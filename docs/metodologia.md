# Metodologia e decisões do experimento

## Formulação

Regressão supervisionada para estimar contagens mensais de solicitações SP156 por distrito. Cada linha representa uma combinação distrito-mês.

## Variável-alvo

Número de registros associados ao serviço de remoção de grandes objetos no mês. A classificação exata deve ser conferida na extração e no dicionário oficial.

## Atributos

Distrito e variáveis de calendário (ano, mês e trimestre). O recorte atual cobre somente três meses; por isso, não são incluídas defasagens. Com lag de um mês, o primeiro mês seria descartado, restando apenas dois meses e impossibilitando treino, validação e teste independentes.

## Validação

Separação cronológica sem sobreposição: primeiro mês para treino, segundo para validação e terceiro para teste. A seleção de hiperparâmetros ocorre somente na validação; os modelos são reajustados com treino + validação e avaliados uma vez no teste. Com apenas três meses, essa avaliação é exploratória e muito limitada. Para uso confiável, é necessária uma série histórica maior e validação temporal com múltiplas janelas.

## Modelos e métricas

Comparação de baseline (valor do mês anterior), Random Forest e XGBoost. Métricas: MAE (erro absoluto médio), RMSE (raiz do erro quadrático médio) e R². A comparação com o baseline é essencial para verificar se os modelos complexos agregam valor.

## Limitações

Solicitações SP156 não são uma contagem completa dos descartes irregulares. Pode haver subnotificação, duplicidade e mudanças de classificação. A agregação por distrito não permite previsão confiável por endereço ou CEP. O modelo não substitui o calendário oficial do Cata-Bagulho.