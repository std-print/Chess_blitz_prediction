<h1 align='center'> Применение моделей машинного обучения <br>в прогнозировании результатов <br>шахматных матчей </h1>

<p align='right'> <strong>Над проектом работали:</strong>
<br> Парамонов Всеволод
<br> Сидоров Иван
<br> Чубов Артем

---

<h3><strong>Раздел 1: Описание проекта</strong> </h3>
<br>
<p>
Целью нашего проекта является предсказание исхода предстоящего шахматного матча с помощью моделей машинного обучения на основании исторической статистики каждого из игроков. Нами был выбран сайт lichess.org, поскольку он является самой крупной площадкой в мире онлайн шахмат и предоствляет огромное количество информации, которая может быть полезна для нашей задачи.

Со стороны юзера итоговый функционал нашего проекта будет иметь следующий вид: пользователь знает о том, что в будущем какие-либо два шахматиста будут играть партию между собой, так же он знает формат этой партии и время, в которое она планирует начаться. Кроме того у него есть интерес в том чтобы сделать ставку на победу одного из игроков, и тут он решает довериться нашей модели, которая может сказать ему, на кого лучше поставить свои деньги, или же, если модель неуверена, может порекомендовать не ставить на данное конкретное противостояние.
<br>
<br>

<h3><strong>Раздел 2: Парсинг </strong> </h3>

<br>
После скачивания исходного файла и преобразования его в csv-файл был применен парсинг с помощью библиотеки BeautifulSoup. Для каждой партии мы достали дополнительные признаки, такие как: рейтинги игроков в разных режимах, количество сыгранных партий, побед, ничьих, поражений, решенных пазлов за все время и текущии серии побед и поражений. В ходе предварительного обучения модели было выяснено, что информативность у признаков низкая (рис.2) и поэтому было принято решение добавить еще несколько признаков: разброс рейтинга и результаты предыдущих партий, которые были сыграны игроками до партий, которые мы рассматриваем. Однако в процессе реализации возникли следующие проблемы: 

<br>
<br>

<ul>
  
<li> Во-первых, парсинг, реализованный нами, имел ограничение в 40 страниц, поскольку дальнейшие страницы lichess.com не предоставляет, объясняя это тем, что данные устарели. В ходе реализации было выявлено, что много людей играет довольно часто, поэтому интересующие нас игры (которые происходили до рассматриваемого матча) находятся за пределами 40 страниц, поэтому сделать парсинг дополнительных признаков становится проблематично. Данная проблема могла решиться использованием API lichess или же использование продвинутного поиска, реализованного на сайте lichess </li>
  
<li> Во-вторых, как было описано ранее, скрипт по парсингу проходит по всем 40 страницам, это занимает большое количество времени, что является не очень оптимальным. Проблема решается скачиванием более нового датафрейма, но это тоже занимает определенное время. Было бы удобно, если lichess.com предоставлял базу данных, обновляемую в реальном времени
  
<li> В-третьих, в ходе парсинга возникла проблема с тем, что мы получили бан на сайте lichess.com, что ограничивает наши дальнейшие. Проблема бы решалась с помощью добавления интервала между парсингами игр, что уменьшило бы шанс быть забаненым, но тогда это бы занимало еще больше времени </li>
  
</ul>
<br>
<br>
<img src='https://drive.google.com/uc?id=1MOgiBHEHPqdwiBDC-H7tl4a813zAxHyb' width='200' style='display: block; margin: 0 auto;'>
<br>
<p align='center'> Рис.2 Признаки </p>
<br>

<p> На рис.2 видно, что текушая серия побед лучше всего влияет на результаты, которые выдает модель, поэтому мы предполагаем, что парсинг последних трех игр улучшит качество будующей модели.

<br>
<br>


<h3><strong>Раздел 3: Предварительная обработка </strong> </h3>

<br>
На сайте lichess.org представлены базы данных по всем играм за определенный месяц: https://database.lichess.org/#standard_games. Нами было принято решение взять данные за март 2023, последний доступный месяц на момент начала создания проекта. В ходе скачивания базы данных возникла проблема с загрузкой файла, а конкретно отсутствие такого большого количества памяти на компьютерах и довольно экзотичный формат файла (PGN-файл), который смогут разахривировать лишь некоторые программы. После длительного промежутка времени удалось устранить эту проблему и в конце концов скачать файл полностью.
<br>
<br>
В итоговом файле хранилась следующая информация: никнеймы шахматистов, цвета, за которые играли игроки, режим игры, длительность игры, дата, когда была сыграна партия, и ее результат. В файле эти данные хранились в формате Json (рис. 1) и были преобразованы в Pandas-датафрейм с помощью различных методов EDA, в частности: применили LabelEncoding для целевой переменной, преобразовали даты игр, а также временные форматы игр разделили на добавленное и основное время. В ходе исследования данных мы поняли, что нужно удалять дубликаты, чтобы разнообразить выборку, потому что, как оказалось, одни и те же игроки играют друг с другом в один и тот же временной формат очень часто, что может вызвать некоторое переобучение, так как выгруженных партий у нас достаточно много, удаление "дубликатов" не окажет негативного влияния. 
<br>
<br>
<img src='https://drive.google.com/uc?id=1pyanHNfq5t2W1xZ5CGCYULQkacowyVfr' width='400' style='display: block; margin: 0 auto;'>
<br>
<p align='center'> Рис.1 PGN-файл </p>
<br>

Также возникла проблема с репрезентативностью выборки, потому что в изначальном файле брались игры, которые были сыграны игры за один день. Чтобы избежать этого было принято решение брать игры с помощью цикла с шагом, но на данном этапе с этим возникают проблемы. Стоит упомянуть, что каждый из этих процессов занимает длительное время из-за большого размера файлов, поэтому было принято решение взять небольшое количество игр для демонстрации.

 Update: проблема решилась, все игры за март были выгружены
<br>
<br>


<h3><strong>Раздел 4: Визуализация </strong> </h3>

<br>
<p> Визуализация была разделена на три подчасти:

</ul>

1) Интересные связи между непосредственными соперниками, например, как распределено отличие рейтингов соперников, насколько влияет win-streak на победы в будущих матчах и так далее, чтобы пощупать связь между опонентами, характеристики партий
  
2) Интересные заокномерности в режимах игр, по всем игрокам вместе, не учитывая соперничество, например, рейтинги игроков в Blitz или насколько популярны экзотические режимы среди шахматистов и так далее, чтобы посмотреть на генеральную совокупность игроков, посмотреть на шахматистов по отдельности.

3) Определение 10 наиболее важных признаков при помощи методов машинного обучения и методов сжатия признаков, такие как PCE, RandomForest importance и другие, затем мы агрегируем важности признаков, полученные разными методами в одну итоговую метрику.

<br>


<h3><strong>Раздел 5: Создание новых признаков </strong> </h3>

<br>
<p> В ходе работы над признаками были отобраны несколько признаков:
  
<br>
<br>
  
<ul>
  <li> <strong>Отношение побед к поражениям.</strong> Этот признак позволяет оценить стратегические или же тактические навыки игрока, что непосредственно сказывается на качестве игры. Поскольку мы считаем показатель для двух игроков, то можно сказать, что мы составили из одного показателя сразу два, для других показателей аналогично</li>
  
  <br>
  
  <li> <strong> Индикатор, что рейтинг в режиме Blitz больше среднего. </strong> Этот показатель отражает наличие высокой игровой эффективности в режиме Blitz, что свидетельствует об опыте и навыках игрока </li>
  
  <br>
  
  <li> <strong> Доля побед. </strong> Игроки с высокой долей побед могут быть стратегически сильнее соперника, что непосредственно влияет на результаты матча </li>
  
</ul>


  
<br>


<h3><strong>Раздел 6: Гипотезы </strong> </h3>

<br>
<p>
Шахматы довольно любопытная, богатая историей игра, в которой возникает много парадоксов и неизведанных тем, которые до сих пор изучают при помощи статистики. Мы выбрали некоторые из них, которые показались наиболее интересными и подходящими для нашего датасета:
<br>

Гипотеза 1: Влияние преимущества первого хода на результативность игроков выше среднего.

Гипотеза: Существует связь между правом первого хода(белые фигуры) и результатами игроков с рейтингом выше среднего (1800 и выше) на платформе Lichess.

Описание гипотезы: Предполагается, что игроки с рейтингом выше среднего обладают определенным преимуществом первого хода и чаще побеждают. При этом предполагается, что это преимущество проявляется более явно при более длительных временах контроля. Также учитывается предпосылка о том, что игроки с низким рейтингом могут не обладать достаточным опытом или мотивацией для полной реализации преимущества первого хода.

Для проверки гипотезы о матожидании воспользуемся Z - тестом и тем, что среднее по выборке примерно нормально.

Гипотеза 2: Влияние времени суток на результативность игроков.

Гипотеза: Существует связь между временем суток (утро, день, вечер, ночь) и результативностью игроков на платформе Lichess.

Описание гипотезы: Предполагается, что время суток может оказывать влияние на результаты партий. Например, игроки могут быть более активными и сфокусированными в определенное время суток, что может отразиться на их результативности. Также возможны различия в стиле игры или предпочтениях стратегий в зависимости от времени суток.

Метод проверки гипотезы: Для проверки данной гипотезы можно использовать критерий Хи-квадрат Пирсона. Критерий Хи-квадрат Пирсона позволяет оценить, существует ли статистически значимая связь между временем суток и долей побед игроков. Если результаты теста будут статистически значимыми, можно сделать вывод о наличии связи между временем суток и результативностью игроков выше среднего.

Гипотеза 3: Различие в результативности игроков за белых и черных фигуры

Гипотеза: Существует различие в результативности игроков выше среднего рейтинга, игравших за белых и черных фигуры на платформе Lichess.

Описание гипотезы: Предполагается, что игроки выше среднего рейтинга, играющие за белых фигур, имеют преимущество и показывают более высокую результативность по сравнению с игроками выше среднего рейтинга, играющими за черных фигур. Это может говорить о довольно неоднородной выборке.

Метод проверки гипотезы: Для проверки данной гипотезы можно использовать критерий Уэлча для независимых выборок. Критерий Уэлча позволяет сравнить средние значения рейтингов игроков за белых и черных фигур, учитывая возможное различие в их дисперсиях. Если результаты теста будут статистически значимыми, можно сделать вывод о наличии различия в результативности игроков за белых и черных фигур.

<br>

<h3><strong>Раздел 7: Обучение моделей </strong> </h3>
<br>
<p>
Наша модель должна решить задачу многоклассовой классификации (победа белых, победа черных, ничья), что оставляет нас с определенным пулом моделей, которые подходят для этой задачи, например: SVM, RandomForestClassifier, LogisticRegression и тд. Предварительно для этих моделей мы будем масштабировать данные с помощью StandartScaler либо MinMaxScaler, чтобы использовать регуляризацию для избежания переобучения и отбора признаков (в случае Lasso-регуляризации). Нами уже были опробованы методы ребалансировки классов (oversampling и undersampling), однако мы отказались от их использования, т.к. доля побед и поражений сопоставимы, а количество ничей слишком мало, чтобы их искусственно сэмплить. 
<br>
<br>
В качестве метрики для нашей задачи мы будем использовать AUC-ROC и F-beta. Так как шахматы тоже являются разновидностью спорта, то мы не стремимся получить 100% точности предсказаний нашей модели и рассчитываем примерно на AUC-ROC = 0.6-0.7. Что касается F-beta меры, то мы планируем сместить ее в сторону precision нежели recall, поэтому коэффициент β будет меньше 1, так как в ставках лучше иногда перестраховаться и делать их лишь при большой уверенности в своих предсказаниях. Таким образом, когда наша модель все-таки будет делать уверенное предсказание, оно должно быть в большинстве случаев правильным.
</p>

