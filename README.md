# TFM-BigData-DataScience-VIU-SHM-Classification-Sonkyo-Benchmark
Trabajo de fin de Máster de Big Data y Data Science de la VIU de Enrique González Morales. El trabajo consiste en el desarrollo de modelos de clasificación de daños a partir de un acelerómetro de los resultados expermientales del Sonkyo Benchmark

El primer código en ejecutar el a8_channel_reader.ipynb que lee los archivos lvm, para cada caso y temperatura genera un archivo _a8.npy que contiene la información del acelerómetro 8. Una fila para cada segundo de la grabación, y una columna para cada una de las muestras en el segundo (1666). Se ha usado un criterio de calidad sobre el sensor de fuerza para asegurarse de que el vibrador está funcionando.

Como se va a alimentar los modelos con los datos en el ámbito del tiempo y de la frecuencia, a continuación se ejecuta el freq_transform.ipynb, toma los archivos _a8.npy para cada caso y los transforma a la frencia generando archivos _a8_freq.npy.

Para los modelos basados en características se tiene que ejecutar antes features.ipynb que funciona tanto para el tiempo como para la frecuencia. La función features calcula 13 características para cada fila (muestra de 1 segundo) y genera archivos _feautres.npy para cada caso y cada temperatura. La función get_all_features_x_y() prepara X e Y para el entrenamiento de los modelos, añade la temperatura a la matriz X para usarla en el entrenamiento, Y contiene el nombre del caso correspondiente como etiqueta. Los archivos generados son "x_for_ml.npy" e "y_for_ml.npy".

ML_GaussianNB.ipynb, ML_KNC.ipynb, ML_RFC.ipynb, ML_SGD.ipynb ML_MLP.ipynb, son los códigos para crear los modelos basados en características. Consiguen las siguientes precisiones:
  - GaussianNB: tiempo 20% frecuencia 23%
  - KNClassifier: tiempo 33% frecuencia 43%
  - SGD: tiempo 26% frecuencia 31%
  - RFC: tiempo 35% frecuencia 43%
  - MLP: tiempo 36% frecuencia 49%

Como el modelo MLP obtiene buenos resultados, se ha probado a entrenar un nuevo modelo con las características del tiempo y la frecuencia combinadas. Para preparar el dataset se utiliza el código join_frec_and_time.ipynb. El modelo se entrena con ML_MLP_time&freq.ipynb y los resultados son:
 -MLP: tiempo + frecuencia 50%

Se han entrenado también en los códigos directamente con los datos temporales en ambos ámbitos, para preparar los datos se utiliza el código join_data.ipynb. Los modelos son entrenados en ltsm_freq.ipynb, ltsm_time.ipynb, Conv1d_freq.ipynb, Conv1d_time.ipynb:
  - LTSM: tiempo 24.55% frecuencia 96.6%
  - CNN1D: tiempo 91.7% frecuencia 97.74%

Finalmente se entrenan modelos a partir de espectrogramas generados por CWT de estas mismas muestras de 1 segundo. Para generar las imágenes se utiliza el código generate_cwt_images.ipynb. Los modelos se entrenan en los códigos, Conv_2d.ipynb, Conv_2d_transfer.ipynb, Conv_2d_transfer_fine_tuning.ipynb. Los resultados de precisión son los siguientes:
  - CNN2d: 92.02%
  - CNN2d transfer learning ResNet50V2: 66.9% 
  - CNN2d transfer learning EfficientNetV2B0: 79.48% 
  - CNN2d transfer learning EfficientNetV2B0 fine tuning: 85% 

A parte de entrenar a partir de imágenes, se decide crear modelos que se entrenan directamente con matrices. Para generar las matrices se usa generate_cwt_matrices.ipynb. Para entrenar el modelo se usa Conv_2d_matrix.ipynb
  - CNN2d matrices: 96.06%
