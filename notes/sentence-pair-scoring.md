## Введение

Часто, в NLP рассматривают задачу `f(s_1) <- [0, 1]`, где s_1 - последовательность
токенов (например, предложение), f - может представлять собой тему, тональность и т.д.
Также существует большой класс задач `g(s_1, s_2) <- R`, где s_1, s_2 - последовательность
токенов, g - представляет собой семантическую близость между s_1 и s_2.

В данной статье предлагают единый фреймворк на основе нейронных сетей для решения задач класса `g(s_1, s_2)`.
Они рассмотрели 3 задачи этого класса.

## Задачи

### 1. Поиск предложения являющееся ответом на вопрос (Answer sentence selection)

Дан вопрос s_1. Есть несколько поддерживающих ответов (answer-bearing sentences, s_2) на этот вопрос.
Надо выдать top-k ответов на вопрос s_1.
В качестве метрики считают [Mean Average Precision](http://fastml.com/what-you-wanted-to-know-about-mean-average-precision/)
и [Mean Reciprocial Rank](https://en.wikipedia.org/wiki/Mean_reciprocal_rank).

Пример датасета: https://github.com/brmson/dataset-sts/blob/master/data/anssel/wang/dev.csv.

В статье рассматривают несколько датасетов, которые есть в открытом доступе.

### 2. Ранжирование следующего высказывания в диалоге (Next utterance ranking)

Задан конекст диалога s_1. Необходимо предсказать следующее высказывание s_2 для
этого конекста. Набора высказываний дан, т.е. нужно выдать top-k предсказаний,
но в отличие от предыдущей задачи, здесь только один правильный ответ.
В качестве метрики считают Recall at top-k и [Mean Reciprocial Rank](https://en.wikipedia.org/wiki/Mean_reciprocal_rank).

Используется датасет Ubuntu dialogue corpus v2.

### 3. Определение типа отношения для гипотезы и факта и определение семантической близости (Recognizing textual entailment and semantic textual similarity)

Здесь рассматриваются 2 подзадачи.
Первая - задана гипотеза (s_0) и факт (s_1). Требуется определить являются ли
они противоречивыми, нейтральными или следствием.
Пример: https://github.com/brmson/dataset-sts/blob/master/data/sts/sick2014/SICK_test_annotated.txt.

Вторая: заданы два предложение s_0 и s_1. Требуется определить их семантическую
близость (от 0 до 5, пример см. выше).

В качестве метрики использовалась корреляция Пирсона (Pearson's r correlation).

В качестве датасета использовалась дорожка из SemEval 2014 и SNLI (Stanford Natural Language Inference).

## Модели

Для решения описанных выше задачи были использованы различные архитектуры нейросетей.
Общая схема работы следующая:
на вход подавались векторные представления e_1, e_2, где e_1, e_2 - набор векторных
представлений для токенов из s_1, s_2. Промежуточным выходом нейросети являются
векторные представления последовательности токенов (предложений) E_1, E_2.
Затем E_1 и E_2 подавались на оценочный модуль (Scorer module), который сравнивает
E_1 и E_2 и возвращает скаляр.

Для получения промежуточных представлений E_1, E_2 они использовали следующие модели:
- Bidirectional GRU,
- CNN,
- RNN-CNN,
- RNN-CNN+attention.

Для оценочного модуля они использовали следующие функции:
- Ranknet (answer sentence selection),
- pearsons r formula (semantic text similarity),
- cross-entropy.

Стоит отметить, что кроме векторных представлений на вход нейросети
подавались несколько вручную составленных признаков, такие как BM-25 word overlap,
unigram overlap, bigram overlap, capitalization.

Более подробные схемы и рисунки по этому разделу см. здесь: https://dl.dropboxusercontent.com/u/30880202/sts-image.JPG.


## Эксперименты

Предложенные модели показывают хороший результат. State-of-the-art они установили
в задаче Next utterance ranking, где использовали RNN-CNN и побили 3-hop Memory network.
В остальных качество чуть хуже чем state-of-the-art. В статье рекомендуют использовать
RNN-CNN.

## Заключение

В данной статье авторы предложили единый подход для решения класса задач определения
семантической близости с использование нейронных сетей.

Результаты полученные в статье воспроизводимы, они выложили очень
хороший репозиторий на гитхабе вместе с данными и кодом: https://github.com/brmson/dataset-sts.