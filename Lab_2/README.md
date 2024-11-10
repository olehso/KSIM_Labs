## Комп'ютерні системи імітаційного моделювання
## СПм-23-3, **Соболь Олег Русланович**
### Лабораторна робота №**2**. Редагування імітаційних моделей у середовищі NetLogo

<br>

### Варіант 7, модель у середовищі NetLogo:
[Wolf Sheep Predation](http://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/Sample%20Models/Biology/Wolf%20Sheep%20Predation.nlogo).

<br>

### Внесені зміни у вихідну логіку моделі, за варіантом:
**Прибрати "зграйність" вовків**.

Процедура для руху та оцінки оточення вовками. Перевіряючи оточення, та обирати напрямок руху виходячи з наявності вівець та відсутності інших вовків. Якщо немає іншої можливості – переміщається випадково. 
<pre>
to move-and-assess-surroundings
  let nearby-sheep sheep in-radius 1  
  let nearby-wolves wolves in-radius 1 
  
  if any? nearby-sheep [ 
    face one-of nearby-sheep ; Пересування до найближчої вівці
    fd 1
  ] else if not any? nearby-wolves [
    rt random 50  ; Випадкове відхилення при відсутності овець та вовків
    lt random 50
    fd 1
  ] else [
    fd random 2  ; Якщо немає іншої можливості – переміщається випадково
  ]
  handle-wolf-collision  ; Ситуація, коли на одній ділянці поля два вовки
end
</pre>

**Обробка ситуації (колізії), вовків на одній ділянці поля**
<pre>
to handle-wolf-collision
  if any? other wolves-here [
    let other-wolf one-of other wolves-here  ; Вибір іншого вовка на цій клітинці
    ask other-wolf [ die ]  ; Видалення одного вовка
  ]
end
</pre>

**Процедура зміни напрямку овець**. 

При виявленні вовка на одній із клітин поруч змінюють напрямок на протилежний.
<pre>
to change-direction-if-near-wolf
  let nearby-wolves wolves in-radius 1
  if any? nearby-wolves [
    face one-of nearby-wolves  ; повернуться к ближайшему волку
    rt 180  ; изменить направление на противоположное
    fd 1
  ]
end
</pre>

### Внесені зміни у вихідну логіку моделі, на власний розсуд:
**Вовки оцінюють рівні енергії**.

Вовки тепер приймають рішення про пересування не лише на основі наявності овець або інших вовків, але й враховують свій рівень енергії. Якщо енергія низька, вони агресивніше шукають їжу (овець).
<pre>
  ; Якщо енергія низька, вовки активно шукають овець
  if energy < wolf-gain-from-food [ 
    --
  ]
  if energy >= wolf-gain-from-food [
    fd random 2 ; Звичайна поведінка за нормального рівня енергії
  ]

</pre>
<pre>
to move-and-assess-surroundings 
  let nearby-sheep sheep in-radius 1
  let nearby-wolves wolves in-radius 1
  
  if energy < wolf-gain-from-food [
    if any? nearby-sheep [
      face one-of nearby-sheep ; Пересування до найближчої вівці
      fd 1
    ]
    if not any? nearby-sheep and not any? nearby-wolves [
      rt random 50 ; Випадкове відхилення при відсутності овець та вовків
      lt random 50
      fd 1
    ]
    if not any? nearby-sheep and any? nearby-wolves [
      fd random 2 ; Якщо немає іншої можливості – переміщається випадково
    ]
  ]
  if energy >= wolf-gain-from-food [
    fd random 2 ; Звичайна поведінка за нормального рівня енергії
  ]
              
  handle-wolf-collision ; Ситуація, коли на одній ділянці поля два вовки
end
</pre>

**Вівці стають менш передбачуваними**.

Щоб уникнути надмірної передбачуваності в русі овець при наближенні вовка, вони змінюють напрямок випадковим чином (у межах 90-180 градусів), а не завжди на 180 градусів.
<pre>
to change-direction-if-near-wolf  
  if any? wolves in-radius 1 [
    rt random 90 + 90  ; Змінено кут повороту для більшої непередбачуваності
    fd 1
  ]
end
</pre>

**Енергетичний рівень впливає на народжуваність**.

Тепер і вівці, і вовки можуть розмножуватись лише за достатнього рівня енергії, що унеможливлює перевищення популяції за межі ресурсоємності моделі.
<pre>
to reproduce-sheep  
  if energy > 2 * sheep-gain-from-food and random-float 100 < sheep-reproduce [ ; Умова на енергію
    set energy (energy / 2)
    hatch 1 [ rt random-float 360 fd 1 ]
  ]
end

to reproduce-wolves  
  if energy > 2 * wolf-gain-from-food and random-float 100 < wolf-reproduce [ ; Умова на енергію
    set energy (energy / 2)
    hatch 1 [ rt random-float 360 fd 1 ]
  ]
end
</pre>

