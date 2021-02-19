# obj2vec

Проект посвящён построению векторных представлений объектов по близостям между объектами.

Близость по-разному вычисляется для объектов разных типов. Далее включая, но не ограничиваясь рассматриваются слова, тексты и изображения. Важно отметить, что предлагаемый подход применим к любому типу объектов, что обосновывает название проекта obj2vec.

Оглавление:<br/>
[Задачи и приложения](#problems_and_applications)<br/>
[Идеи для статей](#ideas_for_papers)<br/>
[Теоретические вопросы](#theoretic_questions)<br/>
[План статей](#papers_plan)<br/>
[Литература](#literature)<br/>
[Полезные ссылки](#links)<br/>

<a name="problems_and_applications"/>
## Задачи и приложения

В разделе описаны следующие задачи:
- По векторным представлениям одной размерности получить векторные представления другой размерности
- По векторным представления одной размерности получить векторные представления той же размерности, но более высокого качества
- Не имея векторных представлений получить векторные представления по близостям из внешней информации
- Не имея векторных представлений получить векторные представления по части близостей из внешней информации
- (new!) Не имея векторных представлений получить векторные представления по порядкам близостей из внешней информации

### Существующие задачи

Изначально было необходимо по матрице близостей набора объектов получить векторные представления объектов, наилучшим образом соответствущие близостям между объектами — это известные задачи многомерного шкалирования: cMDS, mMDS, nMDS (https://en.wikipedia.org/wiki/Multidimensional_scaling, https://www.kaggle.com/c/nicn2/overview/approaches).

Очень важно, что задача nMDS имеет точное решение(ссылки ниже) - однако про размерность представлений ничего не сказано, предположительно, верхняя оценка не должна превосходить количество объектов(см. раздел Теоретические вопросы).

>Sameer Agarwal etc. “Generalized Non-metric Multidimensional Scaling”. Proceedings of the
>Eleventh International Conference on Artificial Intelligence and Statistics, PMLR 2:11-18, 2007 
>http://proceedings.mlr.press/v2/agarwal07a/agarwal07a.pdf

>Bronstein Alexander M. etc. «Generalized Multidimensional Scaling: A Framework for Isometry-
>Invariant Partial Surface Matching». Proceedings of the National Academy of Sciences, 2006,
>1168–72. https://www.pnas.org/content/pnas/103/5/1168.full.pdf

Также важно, что nMDS переводит близости(функция близости - неотрицательная _несимметричная_ функция двух аргументов) в расстояния(функция расстояния - неотрицательная _симметричная_ функция двух аргументов, равная нулю if and only if равны её аргументы) - т.е. объекты становятся элементами пространства(пространство это множество объектов и функция расстояния), при этом если выполнено неравенство треугольника, то пространство является метрическим, а расстояние метрикой. Это важно, поскольку есть гипотеза, что в метрических пространствах поиск ближайших соседей может осуществляться быстрее, чем в неметрических пространствах и не пространствах(см. раздел Идеи для статей). 

Наконец, существенным является замечание, что в метрическом простанстве матрица расстояний эквивалентна векторным представлениям, поскольку матрица и представления однозначно преобразуются друг в друга(тут будет ссылка).

Приложением nMDS является понижение размерности - для представлений высокой размерности строится матрица близостей, которая затем вкладывается в пространство меньшей размерности. Примером из жизни может служить задача, когда набор трёхмерных координат городов нужно отобразить на плоскую карту, сохранив монотоность расстояний(близости на сфере это расстояния?) между городами. Также интересно, что с помощью cMDS можно доказать, что Земля не плоская.

### Новые задачи

Задачи, описанные в этом и следующих пунктах, основаны на идее, что числа, составляющие матрицу близостей имеют смысл сходства(близость это математическое понятие, выражаемое численно; сходство это психологическое понятие, характеризующее похожесть образом в сознании: похож, не похож, похож больше, похож меньше). Для трёх объектов эта связь близости и сходства означает, что если близость между A и B меньше чем между A и C, то A больше похож по смыслу на B, чем на C. Для четырёх объектов это означает, что если близость между A и B меньше чем между C и D, то A больше похож по смыслу на B, чем C на D.

Очень важно прочувствовать различие между математической близостью и психологическим сходством. Так, в привёденном ранее примере с городами из того, что города находятся рядом географически не следует, что они похожи в сознании граждан, поскольку эти города могут находиться в разных странах, располагаясь возле границы. И наоборот, далекие в пространстве города могут быть похожи в сознании путешественников.

Содержательно, эта идея приводит к возможности добавлять внешнюю информацию. Например, эксперт, у которого в сознании есть понятие сходства может выразить его численно в виде близости(точно также как преподаватели оценивают знания студентов на экзаменах, только в данном случае речь идёт о парах объектов). Начиная отсюда и далее, если особо не сказано иное, считается, что близость и сходство взаимозаменяемые понятия(почему? сходство в сознании, а близость это число), поскольку вводится допущение, что **похожие по смыслу объекты близки**(нужна грамотная формулировка, учитывающая все четыре варианта).

(Картинка: мир внешней информации)

Используя внешнюю информацию можно менять значения в матрице близостей. Далее рассмотрены задачи, которые возникают когда меняется часть значений(Новая задача I), все значения(Новая задача II). Отдельно рассмотрен случай, когда в матрице близостей есть только часть значений, полученных из внешней информации, а остальные значения отсутствуют(Новая задача III). Наконец, особый интерес представляет случай, когда нет никаких значений, но из внешней информации получены сведения о порядках близостей(Новая задача IV)(возможно имеет смысл отдельно выделить случай, когда порядки близостей также известны частично).

<ins>Новая задача I</ins>

Используя предподсчитанные векторные представления объектов можно получить матрицу близостей. Затем можно взять экспертные значения близостей, т.е. внешнюю информацию, и заменить ими значения в соответствующих ячейках исходной матрицы близостей. Решив задачу ММШ для полученной матрицы, можно получить векторные представления более высокого качества. Для cMDS и mMDS качество представлений измеряется с помощью корреляции Пирсона с некоторыми эталонными значениями близостей. Для nMDS используется корреляция Спирмена, с рангами эталонных значений близостей(могут ли быть эталонные ранги?). Важно уточнить, что внешняя информации, используемая для повышения качества, не должна использоваться для оценки качества.

Приложением данной задачи является повышение качества предподсчитанных векторных представлений. Стоит отметить, что размерность представлений не меняется, что делает подход прозрачным для применения в существующих вариантах использования векторных представлений.

<ins>Новая задача II</ins>

Другим способом использования внешней информации является её применения для построения матрицы близостей без использования предподсчитанных векторных представлений объектов. Так, эксперименты, проведённые в [ссылка на репозиторий], с мешками контекстов слов показали, что качество расчёта близостей по внутренней информации недостаточное — корреляция Спирмена с эталонными значениями близостей оказалась порядка 0.2. В тоже время эксперименты, проведённые в [ссылка на репозиторий], с тезауросом слов показали, что качество расчёта близостей по внешней информации достаточное — корреляция Спирмена с эталонными значениями близостей оказалась порядка 0.4.

Таким образом, используя тезаурус, для слов возможно построить векторные представления сравнимого или более высокого качества, чем у векторных представлений, построенных другими способами. (Можно ли использовать близости из тезауруса для замены ячеек в Новой задаче I? Вроде нет, потому что числа из разных шкал.)

<ins>Новая задача III</ins>

Однако внешней информации может быть недостаточно для полного заполнения матрицы близостей без использования предподсчитанных векторных представлений объектов. Отличие от Новой задачи II заключается в том, что для НЗII нужна функция близости, а в данном случае достаточно таблицы(конечной функции). Также можно предположить, что для не полностью заполненной матрицы могут существовать более эффективные алгоритмы построения векторных представлений(почему?).



<a name="ideas_for_papers"/>
## Идеи для статей:

I. Быстрый поиск ближайших соседей при переходе в метрическое пространство(nMDS+VP-tree)

<a name="theoretic_questions"/>
## Теоретические вопросы:

I. Верхняя оценка на размерность представлений при точном решении задачи nMDS

<a name="papers_plan"/>
## План статей:

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

http://jalammar.github.io/a-visual-guide-to-using-bert-for-the-first-time/

<a name="literature"/>
## Литература

Знакомство с предметной областью — https://ru.wikipedia.org/wiki/Дистрибутивная_семантика
 
С чего всё началось — http://www.jmlr.org/papers/v3/bengio03a.html

Efficient Estimation of Word Representations in Vector Space - https://arxiv.org/abs/1301.3781

Как с этим работать — https://github.com/akutuzov/webvectors/blob/master/preprocessing/rusvectores_tutorial.ipynb

Примеры с кодом по оцениванию представлений (рагновая корелляция и решение задачи аналогий): https://nlp.gluon.ai/examples/word_embedding_evaluation/word_embedding_evaluation.html

GluonCV and GluonNLP: Deep Learning in
Computer Vision and Natural Language Processing - https://www.jmlr.org/papers/volume21/19-429/19-429.pdf

Ensembling Context-Free and Context-Dependent Word Representations:
https://arxiv.org/pdf/2005.06602.pdf


<a name="links"/>
## Полезные ссылки

https://habr.com/ru/post/317798/

https://arxiv.org/abs/1503.03832

https://arxiv.org/abs/1604.05417

Если вы захотите использовать TPE в своих проектах, не поленитесь прочитать оригинальные работы, так как я не осветил самый главный вопрос обучения триплетами — вопрос их отбора. Для нашей небольшой задачи хватит и случайного выбора, однако это скорее исключение, чем правило.


http://akira.ruc.dk/~keld/research/LKH/

Федоряка

Коммивояжер ето тоже поиск перестановки


https://research.google/tools/datasets/

https://code.google.com/archive/p/word2vec/

https://cs.stanford.edu/~quocle/triplets-data.tar.gz

https://github.com/dmorr-google/word_sense_disambigation_corpora


https://www.kaggle.com/datasets

https://www.kaggle.com/derryza/similarity-english-words-evaluation


https://link.springer.com/chapter/10.1007/978-3-540-30116-5_52

https://en.wikipedia.org/wiki/Isomap

https://en.wikipedia.org/wiki/Distance_matrix

https://en.wikipedia.org/wiki/Multidimensional_scaling

https://www.google.com/search?client=ubuntu&hs=irZ&channel=fs&sxsrf=ACYBGNQjT_XmjNXsBjfx3X9EnDeBjVyjfQ%3A1579286558383&ei=HgAiXof_FpPbmwXZ24eICA&q=wmd+algorithm&oq=wmd+al&gs_l=psy-ab.1.0.0i203l3j0i22i30l7.4291.4529..5979...0.2..0.126.261.2j1......0....1..gws-wiz.......0i71j0i20i263j0i67j0.djw0knQ4bCg
(Finding similar documents with Word2Vec and WMD


https://datasetsearch.research.google.com/search?query=similar%20words&docid=v1Eug52gahuxNCf3AAAAAA%3D%3D

https://figshare.com/articles/4_most_similar_words_of_the_target_word_in_diminishing_order_of_similarity_from_Word_1_to_Word_4_/7444310/1


https://en.wikipedia.org/wiki/Triplet_loss

https://en.wikipedia.org/wiki/Similarity_learning


https://en.wikipedia.org/w/index.php?title=Distance_geometry&oldid=961073925

https://en.wikipedia.org/w/index.php?title=Spearman%27s_rank_correlation_coefficient&oldid=963380347

http://www.frccsc.ru/sites/default/files/docs/ds/002-073-05/diss/27-apishev/ds05-27-apishev_main.pdf


https://peerj.com/articles/cs-357/#


https://cocodataset.org/#home


https://theaisummer.com/skip-connections/

http://akira.ruc.dk/~keld/research/LKH/

https://www.mathworks.com/help/stats/tiedrank.html

https://jekyllrb.com/

https://en.m.wikipedia.org/wiki/Speed_prior

https://www.google.com/search?q=knowledge+graph&oq=knowledge+gr&aqs=chrome.0.0j69i57j0l3.7094j1j7&client=tablet-android-samsung-ss&sourceid=chrome-mobile&ie=UTF-8


https://www.google.com/search?q=Semi-supervised&oq=Semi-supervised&aqs=chrome..69i57.280j0j7&sourceid=chrome&ie=UTF-8
