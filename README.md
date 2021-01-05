# pycav
Repositório do trabalho da disciplina PROJ do curso BI MASTER - 2019.3

# BIMaster_ProjetoFinal

O presente projeto tem como objetivo gerar curvas da probabilidade de classificação do estado do processo de transferência de óleo em relação ao problema de cavitação transiente e gas-lock, por meio de redes neurais recorrentes (RNN, mais especificamente, LSTMs) treinadas a partir de um histórico de dados reais de processo. O modelo de estudo utilizado foi o processo de transferência de óleo em unidades de produção de óleo e gás offshore, composto por 3 bombas centrífugas operando em paralelo e com todo o trem de processamento primário a montante, ficando os sistemas de armazenamento e offloading a jusante. 

## Instalação e utilização

Não é necessário realizar nenhum procedimento de instalação. Todavia, o modelo possui as seguintes dependências:

- numpy
- pandas
- sklearn
- matplotlib
- seaborn
- keras
- tensorflow

O modelo foi treinado e testado com TensorFlow para cpu, não tendo sido avaliado o desempenho com o pacote tensorflow-gpu.

## Descrição

Foram fornecidas inicialmente registros de 8 eventos de cavitação ocorridos no ano de 2020, compostos por 20 parâmetros de processo (var1 a var20), tanto a montante quanto a jusante das bombas de transferência, amostrados em intervalos de 60 segundos. Como base de testes utilizou-se um evento adicional, também ocorrido em 2020 e temporalmente posterior aos eventos que compõem a base de treino. Todos esses dados encontram-se anonimizados no arquivo 'base_completa_v2.csv'. 

O principal objetivo deste trabalho é testar técnicas de Aprendizado de Máquina no intuito de obter uma ferramenta que apoie profissionais responsáveis pelo monitoramento remoto do processo de transferência de óleo offshore. Para isso foram testados modelos de redes neurais recorrentes com uma camada, duas camadas e com uma camada de convolução (CNN-LSTM). Para treinar as redes foi utilizada uma função que transformava a base completa de registros em uma série temporal para treinamento supervisionado, utilizando o registro do instante atual, t, e os 5 registros anteriores, t-1 a t-5. Os registros foram previamente tratados para remoção de valores inválidos (NaN) e normalização dos valores. Foram realizados 100 rodadas de treino, compostas por 3000 épocas cada e com batches de 64 amostras, para cada um dos modelos avaliados. Para comparação dos modelos testados foi utilizada a métrica F1-Score, a média harmônica das métricas Precision (número de rótulos ccorretamente identificados dividido pelo total de positivos identificados, incluindo os incorretamente classificados como positivos) e Recall (número de rótulos corretamente identificados dividido pelo total de rótulos que deveriam ter sido classificados como positivo). Os resultados são destacados a seguir.

## Resultados

i) LSTM:

 Uma rede simples, com uma camada LSTM composta por 64 unidades (ou células) e uma camada Dense, com 4 nós de saída, relativos a cada uma dos estados de operação da processo (classes).

 - <b>Sem dropout:</b> Este modelo teve F1-Score médio de 0,319229 com desvio padrão de 0,13055. Ainda, apresentou mínimo de 0,064516 e máximo de 0,702609. 
 
 - <b>Com dropout:</b> Um Dropout de 20% entre a camada LSTM e a camada Dense foi adotado visando avaliar o impactno na generalização do modelo, porém foi obtido um F1-Score médio de 0,273478 (desvio padrão de 0,094961 com mínimo de 0,03337 e máximo de 0,634101), caracterizando uma piora no desempenho.
 
ii) LSTM dupla:

 Similar ao anterior mas agora utilizando uma camada extra LSTM para tentar capturar melhor o comportamento temporal das features. A adição da camada extra impactou o tempo de treinamento, uma vez que mais parâmetros treináveis são adicionados ao modelo. Porém o resultado não foi melhor que o obtido anteriormente.
 
 - <b>Sem dropout:</b> Este modelo teve F1-Score médio de 0,282353 com desvio padrão de 0,105625. Ainda, apreentou mínimo de 0,031217 e máximo de 0,699149.
 
 - <b>Com dropout:</b> Foi adotado um Dropout de 20% entre as camadas LSTM e a camada Dense foi adotado, em semelhança ao modelo LSTM simples. Foi obtido um F1-Score médio de 0,313157 com desvio padrão de 0,111388. Ainda, apresentou mínimo de 0,247801 e máximo de 0,766464. Esse resultado sugere melhoria no desempenho tanto em relação ao modelo LSTM simples (com e sem Dropout) e ao modelo LSTM duplo sem Dropout.

iii) CNN-LSTM:

 Nos dois métodos anteriores foram utilizadas todas as features coletadas para entendimento do problema, sem qualquer tratamento em relação a redução de dimensionalidade ou engenharia de features. Para abordar essa questão foi adotada uma camada de convolução anterior à rede recorrente, composta por 64 filtros com kernel igual a 6, seguida de uma camada de pooling (MaxPooling) com 3 pools. Essa camada reduz a quantidade de dados apresentados à camada LSTM, tornando o problema mais leve do ponto de vista computacional.
 
 - <b>Sem dropout:</b> Este modelo teve F1-Score médio de 0,331152 com desvio padrão de 0,108398. Ainda, apresentou mínimo de 0,03337 e máxim de 0,827586.
 
 - <b>Com dropout:</b> Foi adotado um dropout de 20% entre a camada de convolução e a LSTM, bem como entre a camada LSTM e a camada Dense, em semelhança aos modelos anteriores. Foi obtido um F1-Score médio de 0,388284 com desvio padrão de 0,156331. Ainda, apresentou mínimo de 0,307918 e máximo de 0,857663 (maior valor obtido em todos os experimentos). Esse resultado ugere melhoria no desempenho do modelo convolucional ao adicionar-se o Dropout. Ainda, de forma geral, este foi o modelo com os melhores resultados no experimento.

## Conclusões

Como conclusão vimos que o uso de redes neurais é promissor, uma vez que o comportamento é capturado de forma correta em boa parte dos ensaios. Os padrões do evento de cavitação utilizado como teste foram, de certa forma, capturados pelos modelos e no caso específico do modelo convolucional com dropout, foi obtido o maior F1-Score do experimento.

## Próximos passos

Para verificar a viabilidade do método como erramenta de apoio a profissionais do monitoramento remoto do processo de transferência de óleo seria necessário apresentar ao modelo séries temporais ininterruptas dos dados utilizados como entrada (features). Fica, assim, registrado como próximos passos para aprofundamento do estudo a avaliação do modelo em relação a falsos negativos e falsos positivos em sequências longas de dados (por exemplo, 7 dias contínuos).
