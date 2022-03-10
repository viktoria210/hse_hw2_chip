# hse_hw2_chip
Определение участков генома, где присутствует определенная гистоновая модификация в конкретном типе клеток с помощью анализа ChIP-Seq данных.

## Ссылки на google colab ноутбук
Основная часть: [ChIP-seq on histones]()

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

## Выравнивание на 13 хромосому
Проанализируйте выдачу bowtie. Почему процент выравниваний получился именно таким?
00
