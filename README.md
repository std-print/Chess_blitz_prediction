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


<h3><strong>Раздел 4: Визуализация </strong> </h3>

<br>
## здесь писать
<br>


<h3><strong>Раздел 5: Создание новых признаков </strong> </h3>

<br>
## здесь писать
<br>


<h3><strong>Раздел 6: Гипотезы </strong> </h3>

<br>
<p>
Шахматы довольно любопытная, богатая историей игра, в которой возникает много парадоксов и неизведанных тем, которые до сих пор изучают при помощи статистики. Мы выбрали некоторые из них, которые показались наиболее интересными и подходящими для нашего датасета:
<br>

1) Пусть $p_w$ - доля побед белых, $p_b$ - доля побед черных
$$
H_0: p_w = p_b
\\
H_1: p_w > p_b
$$
Мы предполагаем, что белые должны выигрывать чаще, чем черные, из-за преимущества первого хода. Если множество значений {0, 1} (победа или поражение, ничьи в учет не берутся), то можно использовать z-test для долей, так как количество рассматриваемых партий достаточно большое для ЦПТ. 
<br>
<br>
<img src='https://drive.google.com/uc?export=view&id=1hFhqbZ3AkWG9SWixQGO81v5Ci1fxkohx' width='500' style='display: block; margin: 0 auto;'>
<br>
<p align='center'> Рис.3 Гистограмма результатов белых</p>
<br>

2) Влияет ли опыт игрока на результат матча. На данном этапе мы выбираем метрику опыта игрока: количество сыгранных игр, его рейтинг или же их комбинация. Наше предположение - более опытный игрок склонен выигрывать чаще в партиях с менее опытными игроками. Предположительно, мы будем не отвергать основную гипотезу, если более сильные игроки выигрывают более чем в 70% игр.
<br>

<br>
<img src='https://drive.google.com/uc?export=view&id=1VJ8C61Fvu32dyv7W5aU1rNTIfxbulKpA' width='500' style='display: block; margin: 0 auto;'>
<br>
<p align='center'> Рис.4 Зависимость количества побед игрока от рейтинга </p>
<br>

3) Мы хотим исследовать влияние временного формата шахматной партии в режиме Blitz на стандартное отклоение его рейтинга. Мы предполагаем, что чем более короткий режим игры, тем больше разброс рейтинга игрока, потому что более короткие партии содержат "элемент рандома".

<br>
<img src='https://drive.google.com/uc?export=view&id=1w2CR-nVX-5tmt_HrfQsq2jaKMT-hUwXG' width='500' style='display: block; margin: 0 auto;'>
<br>
<p align='center'> Рис.5 Совместное распределение основного времени и стандартного отклонения </p>
<br>
</p>

<br>

<h3><strong>Раздел 7: Обучение моделей </strong> </h3>
<br>
<p>
Наша модель должна решить задачу многоклассовой классификации (победа белых, победа черных, ничья), что оставляет нас с определенным пулом моделей, которые подходят для этой задачи, например: SVM, RandomForestClassifier, LogisticRegression и тд. Предварительно для этих моделей мы будем масштабировать данные с помощью StandartScaler либо MinMaxScaler, чтобы использовать регуляризацию для избежания переобучения и отбора признаков (в случае Lasso-регуляризации). Нами уже были опробованы методы ребалансировки классов (oversampling и undersampling), однако мы отказались от их использования, т.к. доля побед и поражений сопоставимы, а количество ничей слишком мало, чтобы их искусственно сэмплить. 
<br>
<br>
В качестве метрики для нашей задачи мы будем использовать AUC-ROC и F-beta. Так как шахматы тоже являются разновидностью спорта, то мы не стремимся получить 100% точности предсказаний нашей модели и рассчитываем примерно на AUC-ROC = 0.6-0.7. Что касается F-beta меры, то мы планируем сместить ее в сторону precision нежели recall, поэтому коэффициент β будет меньше 1, так как в ставках лучше иногда перестраховаться и делать их лишь при большой уверенности в своих предсказаниях. Таким образом, когда наша модель все-таки будет делать уверенное предсказание, оно должно быть в большинстве случаев правильным.
</p>

