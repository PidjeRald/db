#!/bin/bash
# Переменная, хранящая путь к домашнему каталогу пользователя
home_dir="$HOME"
# Переменная, хранящая путь к файлу, в который будет записан результат
output_file="$home_dir/subdirs_size.txt"
# Переменная, хранящая общий размер подкаталогов
total_size=0
# Переменная, хранящая количество подкаталогов
subdirs_count=0
# Цикл, перебирающий все подкаталоги в домашнем каталоге пользователя
for dir in "$home_dir"/*/; do
  # Проверяем, является ли текущий элемент каталогом
  if [ -d "$dir" ]; then
    # Получаем размер текущего подкаталога в байтах и записываем в переменную
    size=$(du -sb "$dir" | awk '{print $1}')
    # Увеличиваем общий размер подкаталогов на размер текущего подкаталога
    total_size=$((total_size + size))
    # Увеличиваем счетчик подкаталогов на 1
    subdirs_count=$((subdirs_count + 1))
    # Выводим информацию о текущем подкаталоге в консоль
    echo "Подкаталог: $dir Размер: $size байт"
  fi
done
# Выводим общий размер подкаталогов и количество подкаталогов в консоль
echo "Общий размер подкаталогов: $total_size байт"
echo "Количество подкаталогов: $subdirs_count"
# Формируем таблицу с результатом и записываем в файл
echo -e "Подкаталог\tРазмер (байт)" > "$output_file"
echo -e "---------\t-------------" >> "$output_file"
for dir in "$home_dir"/*/; do
  if [ -d "$dir" ]; then
    size=$(du -sb "$dir" | awk '{print $1}')
    echo -e "$dir\t$size" >> "$output_file"
  fi
done



===========================================================================================================================

#!/bin/bash
# Скрипт для поиска файлов с заданным расширением и строкой в указанном каталоге
# и записи результатов в файл /var/log/фамилия.log

# Получаем от пользователя имя каталога, расширение файлов и строку поиска
read -p "Введите имя каталога: " dir_name
read -p "Введите расширение файлов: " file_ext
read -p "Введите строку для поиска: " search_str

# Проверяем, существует ли указанный каталог
if [ ! -d "$dir_name" ]; then
    echo "Каталог $dir_name не существует."
    exit 1
fi

# Проверяем, существует ли файл /var/log/фамилия.log
if [ ! -f "/home/student/var/log/фамилия.log" ]; then
    touch /var/log/фамилия.log
else
    # Очищаем файл перед записью новых результатов
    > /home/student/var/log/фамилия.log
fi

# Ищем файлы с заданным расширением и содержащие заданную строку в указанном каталоге
find "$dir_name" -type f -name "*.$file_ext" -exec grep -l "$search_str" {} \; >> /home/student/var/log/фамилия.log

echo "Поиск завершен. Результаты записаны в файл /var/log/фамилия.log."

===========================================================================================================================
