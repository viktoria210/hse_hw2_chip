# hse_hw2_chip
Определение участков генома, где присутствует определенная гистоновая модификация в конкретном типе клеток с помощью анализа ChIP-Seq данных.

## Ссылки на google colab ноутбук
Основная часть: [ChIP-seq on histones]()

## Выбор образцов 
[Ссылка на эксперимент](https://www.encodeproject.org/experiments/ENCSR999WHE/)
|"Клеточная линия"|"Гистоновая Метка"|"Реплика 1 (ID fastq файла)"|"Реплика 2 (ID fastq файла)"|"Контроль (ID fastq файла)"|
|:----------------|:-----------------|:---------------------------|:---------------------------|:--------------------------|
|MCF-7            |H3K27me3          |ENCFF250WAH                 |ENCFF023EPH                 |ENCFF640MKX                |

## FastQC (MultiQC) анализ 
Отчеты по качеству чтений для каждого из образцов, а также сводная статистика, находятся в разделе **html_reports**.

Анализируя результат работы MultiQC видим, что для образца `ENCFF250WAH` наблюдается повышенное количество дуплицированных чтений. Для другого ChIP-seq образца на гистоновой метке и для ChIP-seq, используемого в качестве контрольного образца, качество более чем удовлетворительное. 
![fastqc_sequence_counts_plot](https://user-images.githubusercontent.com/60792064/157739763-11b7686f-5924-419e-a4b3-8d7ece79d07b.png)

Если обратить внимание на отдельные отчеты FastQC, то мы также наблюдает, что все основные параметры в пределах допустимых значений для всех чтений, заисключением параметра "Per base sequence quality" для образца `ENCFF250WAH`.

|`ENCFF250WAH`|`ENCFF023EPH`|`ENCFF640MKX`|
|:--------------------|:-----------------|:------------------|
|![WAH_quality](https://user-images.githubusercontent.com/60792064/157741052-12aa4d30-6068-44e9-96fe-0f634241ae99.png)|![EPH_quality](https://user-images.githubusercontent.com/60792064/157741096-0342de3c-9962-478f-a2a9-72eca3b4e375.png)|![MKX_quality](https://user-images.githubusercontent.com/60792064/157741141-3f935413-d223-4724-a6c0-2b8c76d5e54b.png)|

Из этого делаем вывод, что необходимо провести подрезаниче чтений для файла `ENCFF250WAH.fastq`. Сделаем это с помощью `trimmomatic`. 
Посмотим вновь на "Per base sequence quality" характеристику для подрезанного fastq-файла: 

![WAH_trimmed_quality](https://user-images.githubusercontent.com/60792064/157741952-3bb6c6e7-98a9-491e-87b3-168f13265c97.png)

Видим, что после подрезания качество чтений нормализовалось.

## Анализ выдачи bowtie
**Таблица по количеству ридов для для каждого из 3 образцов**

|   |  all reads  |  unique alligned  |  not unique alligned |  not alligned    |
|:--|:------------|:------------------|:---------------------|:-----------------|
|WAH|33975967     |1333012 (3.92%)    |3801619 (11.19%)      |29941336 (84.89%) |
|EPS|37715935     |1409469 (3.74%)    |4437146 (11.76%)      |31869320 (84.50%) |
|MKX|40520604     |1620377 (4.00%)    |5084838 (12.55%)      |33815389 (83.45%) |

WAH $\rightarrow$ `ENCFF250WAH_trimmed.fastq`

"Проанализируйте выдачу bowtie. Почему процент выравниваний получился именно таким?"

**Ответ:**  Мы получили следующие "overall allignment rate": 15.11%, 15.50%, 16.55%; для WAH, EPS и MKX образцов соответственно , т.к. картировали не на всю ДНК, а на отдельную хромосому. Поэтому получили такой сравнительно маленький процент закартировавшихся последовательностей. 

Заметим, что в процентном соотношении длина 13 хромосомы составляет от 3,5% до 4%. Мы же получили примерно в 4 раза большее посравнению с этими числами проценты. 
Это происходит вследствии диплоидности большинства клеток человека, а также благодаря их постоянному делению. Поэтому, раздив днк человека ультрозвуком, мы получаем четыре кусочка, покрывающих одну и ту же позицию. Постоянное же деление клеток вносит дополнительный вклад в количество ридов, покрывающих ту или иную позицию выбранной нами 13 хромосомы (как и для любой другой позиции в геноме). 


