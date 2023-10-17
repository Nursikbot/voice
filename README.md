# Voice AI Research for Vk_lab
## Содержание
1) audio_mood.ipynb основной файл с обученной моделью WAV2Vec2 и анализом результатов. Также приведено сравнение с базовой моделью и анализом сигналов + выгрузка результатов

## Файл содержит в себе ссылки на диск на загрузку датасетов и обученной модели, также привожу их тут:
1) RAVDESS -> https://drive.google.com/file/d/1gn79SrzmBDclSP6q75UUs2hZ42cIeqkf/view?usp=sharing
2) Тестовый датасет -> https://drive.google.com/file/d/1--3dUqmNRJyhj2Ht1gl9ktoRaafbuHhw/view?usp=sharing
3) Веса и состояние оптимизатора модели (.pth) -> https://drive.google.com/file/d/1ZZPWhGp5fd3VrtBNI4EXo8QF5ogQIUy4/view?usp=sharing

### Плюсы Wav2Vec 2.0:
1) Энкодер модели извлекает признаки из самого аудиосигнала без привязки к языку, его транскрипции. Он также сжимает признаки в вектор фиксированной длины, тем самым модель учиться предсказывать сигнал используя только часть спектра.
2) Предобученная модель Wav2Vec 2.0 может быть использована для ряда задач: распознавание и классификация аудиосигналов, распознавание голоса, извлечение текста. Причем точность перевода сигнала в текст высокая.
3) Можно использовать модель для перевода аудио в текст, а далее работать с готовыми классификаторами(сеентимент анализ и тд)

### Минусы Wav2Vec 2.0:
1) Обучение модели 'jonatasgrosman/wav2vec2-large-xlsr-53-english' требует больших ресурсов(файнтюнинг гиперпараметров занимает какое-то время).Требуется более аккуратный подбор параметров для конкретной задачи, учитывая особенность архитектуры(заморозка/разморозка слоев).
2) Требуется дообучение на огромных данных(пробовал уменьшать размер обучающей выборки, качество стремительно падало).
   
## Особенность выбора wav2vec2 для задачи классификации эмоций
Удобство работы, модель имеет встроенный автоэнкодер, падающий уже взвешанные признаки на вход модели. Нет необходимости иметь глубокие знания в анализе аудиосигналов(harmonics, mel_spectrogram, chroma, mfcc, spectral contrast, pitches and magnitudes и тд) Важных для анализа эмоций. В этом случае есть готовый интерфейс, дающий хорошие показатели. Ссылки на использованные ресурсы:
1) Познавательные видео: https://youtube.com/playlist?list=PL-wATfeyAMNqIee7cH3q1bh4QJFAaeNv0&si=EhmZAE-tJuD9PQos
2) Стаья по распознаванию эмоций на греческом языке: https://github.com/m3hrdadfi/soxan/blob/main/notebooks/Emotion_recognition_in_Greek_speech_using_Wav2Vec2.ipynb?ysclid=lnu8h22nnc158063470
3) Работа с базовой моделью и анализом сигналов была мотивирована работой: https://www.kaggle.com/code/ejlok1/audio-emotion-part-3-baseline-model/notebook
4) Предобученная модель для перевода аудио в текст: https://huggingface.co/jonatasgrosman/wav2vec2-large-xlsr-53-english
5) Cсылка на Wav2Vec2: https://huggingface.co/docs/transformers/model_doc/wav2vec2
6) Работа с использованием CNN: https://github.com/huaiyukhaw/speech-emotion-recognition/blob/master/CNN.ipynb

## Особенности RAVDESS в файле(audio_speech_actors_01-24) и разделения train\test
* Имеются две фразы
* 24 Актера (мж и ж) с разными тембором и высотой голоса
* Каждая фраза воспроизводится дважды с различным тоном(или немного отличным, кому как), важно иметь небольшие возмущения сигналов, либо генерировать шумы исскуственно чтобы нейросеть училась предсказывать с учетом аугментацией, как, например делалось в той же работе -> https://www.kaggle.com/code/pranavbelhekar/audio-emotion-classification-part-3 
* В каждом файле по 60 аудиосигналов, распределение по эмоциям таково: по 4 сигнала для neutral, по 8 для остальных эмоций, в сумме для всех актеров 96 сигналов для neutral и 192 по остальным семи эмоциям
* Использовалось случайное разделение 20/80 с помощью random_split, а не по актерам, позволяет избежать переобучения конкретным фразам или актерам 
* Длина сигналов примерно одинаковая(была выровнена до signal_length = 60000)
* Можно визульно разделить яркие эмоции от пассивных на основе анализа mcfc(The Mel-frequency cepstral coefficients )или энергии(амплитуды^2 сигналов)

### График функции потерь и accuracy на train и test
<figure>
  <img
  src="train_val_loss_accuracy.png"
  >
</figure>    

### Таблицы с метриками  
<figure>
  <img
  src=""
  >
  <figcaption>Таблица 1 - Метрики, посчитанные на val_dataset</figcaption>
</figure>  
Функция потерь на валидационной выборке начинает расти, причем классы распределены не равномерно(neutral вдвое реже), важно обратить внимание на f1 метрику

### Confusion matrix on validation
<figure>
  <img
  src="confusion_matrix.png"
  >
</figure>    

### Results on test dataset using wav2vec
<figure>
  <img
  src="emotionswav2vec2.png"
  >
</figure>  

### Results on test dataset using RandomForest
<figure>
  <img
  src="emotionsRandomForest.png"
  >
</figure>  

  
