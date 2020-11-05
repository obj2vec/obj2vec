# obj2vec

План статей:

I. Схема валидации и понижение размерности

У эмбеддингов есть 4 важных свойства: семантической близости, аналогий, интерпретируемость, метричность
 
Для каждого свойства описывается способ, как оценить величину соблюдения этого свойства для заданных эмбеддингов
 
Затем говорится, что есть средство понижения размерности Ivis, которое принимает на вход некоторые эмбеддинги
 
Мы находим такие настройки параметра средства Ivis, что для них валидационная схема выдаёт максимальные показатели качества выбранных свойств
 
 
II. Свой способ построения эмбеддингов методами MDS
 
Идея в том, что обычно берут сырые тексты, строят на них представления для слов и затем считают матрицу попарных расстояний, а можно из сырых текстов посчитать матрицу попарных расстояний и затем построить представления по ней
 
Кажется, что это новый подход к построению эмбеддингов, но если такая же последовательность шагов уже известна, то в любом случае реализация каждого из них может быть новой — обзор подходов к построению эмбеддингов нужен в любом случае
 
Первый шаг предлагается делать так — для каждого слова берётся множество его контекстов, получает мешок контекстов, псевдодокумент слова, для каждый двух мешков считается коэффициент Жаккара, так получается матрица попарных близостей, для неё тоже нужна схема валидации, предлагается использовать размеченный экспертами набор близостей пар слов и смотреть отклонение к.к.Спирмена от 1
 
Второй шаг — точное nMDS, затем понижение размерности силами результатов из I.


Подходы:

Педставляет интерес перестановочный метод, но также интересен и графовый метод, когда в пространстве размещается граф, полученный по матрице близостей


III. Использование результатов II. как первый слой в контекстно-зависимых моделях



Литература:

Знакомство с предметной областью — https://ru.wikipedia.org/wiki/Дистрибутивная_семантика
 
С чего всё началось — http://www.jmlr.org/papers/v3/bengio03a.html

Efficient Estimation of Word Representations in Vector Space - https://arxiv.org/abs/1301.3781

Как с этим работать — https://github.com/akutuzov/webvectors/blob/master/preprocessing/rusvectores_tutorial.ipynb

Примеры с кодом по оцениванию представлений (рагновая корелляция и решение задачи аналогий): https://nlp.gluon.ai/examples/word_embedding_evaluation/word_embedding_evaluation.html
