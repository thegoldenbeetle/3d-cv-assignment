# Задание по теме 3D компьютерное зрение

### Разработка простейшей системы помощи водителю

1. Скачайте датасет NuScenes Mini (https://www.nuscenes.org/nuscenes)

2. Выберите оттуда одну из понравихшихся дорожных сцен, и реализуйте для нее алгоритм, вычисляющий расстояние от ego vehicle до окружающих его движущихся объектов, по данным камеры и лидара.

   Описание алгоритма:

   - С помощью какого-либо 2D детектора (например, yolo из [ultralytics](https://docs.ultralytics.com/modes/predict/#inference-sources)) получите боксы для объектов, которые могут появиться на дороге, на изображении с камеры.
   - Спроецируйте лидарное облако точек на изображение с камеры. Код, осуществляющий проекцию, должен быть написан самостоятельно, а не просто откуда-то импортирован, разрешается использовать numpy/torch для матричных операций и scipy/pyquaternion для работы с углами.
   - Для каждого обнаруженного объекта оцените расстояние от ego vehicle до него по лидарным точкам, попавшим внутрь соответствующего бокса.

<details>
<summary> <b>Для чтения и визуализации лидарного облака точек можно воспользоваться функциями:</b> </summary>

```python
import plotly.graph_objects as go
import numpy as np

def read_lidar_pointcloud(file_path):
   scan = np.fromfile(file_path, dtype=np.float32)
   points = scan.reshape((-1, 5))[:, :4]
   return points

def show_lidar_pointcloud(points):
   """
   Visualize lidar point cloud interactively in 3D.

   Parameters:
       points (numpy.ndarray): Nx3 or Nx4 array of lidar points (X, Y, Z, [Reflectance])
   """
   if points.shape[1] == 4:  # use reflectance as color
       color = points[:, 3]
   else:
       color = points[:, 2]  # color scale based on height
   
   fig = go.Figure(
       data=[
           go.Scatter3d(
               x=points[:, 0],
               y=points[:, 1],
               z=points[:, 2],
               mode='markers',
               marker=dict(size=2, color=color, colorscale='Viridis', opacity=0.8)
           )
       ]
   )
   
   fig.update_layout(
       title="Lidar Point Cloud",
       scene=dict(xaxis_title="X", yaxis_title="Y", zaxis_title="Z", aspectmode="auto")
   )
   
   fig.show()
```
</details>

3. Соберите полученные результаты в демо-видео, на котором должны присутствовать изображение с камеры, найденные объекты, спроецированные лидарные точки, попавшие внутрь боксов объектов, полученные расстояния для найденных объектов, для каждого кадра выбранной сцены.
   Пример (https://github.com/thegoldenbeetle/3d-cv-assignment/blob/master/output.gif):

   ![Видео-демо с результатами](./output.gif)

4. По найденным 2D боксам и попавшим внутрь лидарным точкам придумайте способ получить 3D боксы объектов и оцените качество получившегося  3D детектора. Ничего страшного, если метрики получились невысокие и детектор не очень хорошо работает, главное разобраться в том, что представляет собой задача, в каком формате можно сохранить результат, и какие используются метрики оценки качества.
  <details>
  <summary> <b>Подсказки и идеи, которые могут помочь:</b> </summary>
  <ul>
      <li>Воспользуйтесь какой-либо моделью сегментации, чтоб точнее определить лидарные точки, принадлежащие объекту.
      <li>Воспользуйтесь какими-либо эвристиками, статистиками, методами кластеризации лидарных точек.
  	  <li>Воспользуйтесь методом поиска главных компонент (PCA) для нахождения угла yaw.
      <li>Для подсчета качества можно воспользоваться <a href="https://github.com/nutonomy/nuscenes-devkit/blob/master/python-sdk/nuscenes/eval/detection/evaluate.py">скриптом</a> из nuscenes-devkit.
  <ul>
  </details>


### Критерии оценивания

- Оценка отлично (5) ставится, если:
  - Студент выполнил все предложенные задания в установленный срок, ориентируется в коде и своем решении, может отвечать на вопросы по нему.
  - Студент разобрался в геометрии проецирования точек в разные системы координат, а также в том, что представляют собой внутренние и внешние параметры сенсоров.
  - Решение представляет собой набор скриптов, содержит файл с описанными зависимостями, воспроизводится.
  - Демо-видео с оценкой расстояния до объектов и результаты 3D детекции объектов получены с использованием всех камер (CAM_FRONT, CAM_FRONT_LEFT, CAM_FRONT_RIGTH, CAM_BACK, CAM_BACK_LEFT, CAM_BACK_RIGTH).
- Оценка хорошо (4) ставится если:
  - Выполнены все задания, но частично, например, демо-видео представлено только для передней камеры (CAM_FRONT).
  - Есть небольшие неточности при ответах на вопросы.
- Оценка удовлетворительно (3) ставится, если:
  - Выполнено только задание на нахождение расстояния до объектов.
  - В реализации методов и при ответе на вопросы есть ошибки.
- Оценка неудовлетворительно (2) ставится, если:
  - Код не запускается.
  - Нет ответов на вопросы.
