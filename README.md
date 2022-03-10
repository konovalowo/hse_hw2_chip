# hse_hw2_chip

Colab: https://colab.research.google.com/drive/1FgBQ-WpH8p0jZo74Y5eQGnGXQKtrtPto?usp=sharing

Клеточная линия OCI-LY1, гистоновая метка H3K9ac

## FastQC

Полные отчеты для двух реплик, одной подрезанной реплики и контроля в папке fastqc

### Реплика 1 ENCFF002BCF

На графиках Per base sequence quality, Per tile sequence quality, Per base N content ухудшение качества к последней позиции рида, FastQC выделил красным.

![image](https://user-images.githubusercontent.com/20297250/157728072-2d606ed7-c9d5-446e-bff6-a67376619012.png)
![image](https://user-images.githubusercontent.com/20297250/157728195-b70b6fb0-2399-4700-9b24-d2a218e92cd3.png)
![image](https://user-images.githubusercontent.com/20297250/157728133-97daf5f4-d106-4435-80b9-5d2e017c9ec9.png)

### Реплика 2 ENCFF002AVE

Аналогично ухудшение качества к последней позиции рида на графиках Per base sequence quality, Per tile sequence quality, Per base N content.

![image](https://user-images.githubusercontent.com/20297250/157728505-15b3049b-ee35-4a32-b645-6ef3ff054352.png)
![image](https://user-images.githubusercontent.com/20297250/157728536-983418c9-00ae-43f9-b62d-68f2b41e1866.png)
![image](https://user-images.githubusercontent.com/20297250/157728550-32526714-a3df-4052-b27c-44a216368824.png)

### Подрезание

Попробуем улучшить качество, подрезав риды:

    !trimmomatic SE -phred33 ENCFF002BCF.fastq ENCFF002BCF_trimmed.fastq  \
      LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

    !trimmomatic SE -phred33 ENCFF002AVE.fastq ENCFF002AVE_trimmed.fastq  \
      LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
      
После подрезания качество на последней позиции рида улучшилось на графиках Per base sequence quality и Per base N content. Из отчета FastQC для подрезанного ENCFF002BCF:

![image](https://user-images.githubusercontent.com/20297250/157729082-9cf77962-705e-4b24-ae7d-85886a8ecd48.png)
![image](https://user-images.githubusercontent.com/20297250/157729103-ccb9170d-ec5c-4b22-8672-a6ab8f821356.png)

### Контроль ENCFF002BBQ

В отчете FastQC для контроля всё ок, фильтрации или подрезания ридов не требуется.

![image](https://user-images.githubusercontent.com/20297250/157729475-763fcea6-47d9-4c74-9517-3c16dc759ece.png)

## Выравнивание

Выравниваем на 14 хромосому.

Статистика по выравниванию ридов:

|                     | всего    | выравнилось уникально | выравнилось не уникально | не выравнилось    |
|---------------------|----------|-----------------------|--------------------------|-------------------|
| ENCFF002BCF_trimmed | 45174401 | 1960078 (4.34%)       | 5165560 (11.43%)         | 38048763 (84.23%) |
| ENCFF002AVE_trimmed | 17257379 | 750895 (4.35%)        | 1863886 (10.80%)         | 14642598 (84.85%) |
| ENCFF002BBQ         | 21124914 | 939278 (4.45%)        | 3174644 (15.03%)         | 17010992 (80.53%) |

### Почему процент выравниваний получился именно таким?

Скорее всего, низкий процент выравненных ридов связан с тем, что выравнивание производилось только на одну хромосому, а не целый геном.

## Сравнение результатов поиска пиков

### ENCFF002BCF на ENCODE

![image](https://user-images.githubusercontent.com/20297250/157736741-81ae0185-48ce-46f2-9c2f-5f27a434bff7.png)

~76% участков ENCFF002BCF пересекается с участками из ENCODE. 

### ENCODE на ENCFF002BCF

![image](https://user-images.githubusercontent.com/20297250/157737016-f7602216-b1d3-4503-ab19-7ecb6ca36dad.png)

Тут только 271 участок из ENCODE пресек участок из ENCFF002BCF. Скорее всего из-за того, что ENCODE содержит пики по всему геному, а ENCFF002BCF по одной хромосоме.

### ENCFF002AVE на ENCODE

![image](https://user-images.githubusercontent.com/20297250/157737078-6572369f-afdb-444b-b441-d4932d2ae99e.png)

Результат хуже, чем у ENCFF002BCF, пересечения примерно у половины пиков.

### ENCODE на ENCFF002AVE

![image](https://user-images.githubusercontent.com/20297250/157737111-09879395-d3a4-4c40-a584-79d37a519f14.png)

Как и в случае с ENCFF002BCF, тут меньше пересечений, чем у реплики на ENCODE. Cкорее всего по той же причине.

### Как можно объяснить различия в количестве пересечений?

Первая диаграмма показывает число участков ENCFF002BCF, которые пересеклись с ENCODE. Вторая число участков ENCODE, которые пересеклись с ENCFF002BCF. 
Отсюда различие в пересечении на даиграммах.

При этом участков ENCFF002BCF на ENCODE больше, т. к. все участки ENCFF002BCF находятся на одной хромосоме, а участки ENCODE распределены по всему геному. Аналогично для ENCFF002AVE и ENCODE.
