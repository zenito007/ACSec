Death Note
https://www.vulnhub.com/entry/deathnote-1,739/
1. Перша команда яку я використовую —

   sudo netdiscover -r 10.0.2.0/24
Визначаю IP адресу
<img width="889" height="269" alt="image" src="https://github.com/user-attachments/assets/5e2898c1-47b2-4051-87ab-f4a99494fa9d" />

   sudo nmap -Pn -n -p- -sC -sV --open 10.0.2.8
<img width="839" height="353" alt="image" src="https://github.com/user-attachments/assets/4a892854-406c-47a0-8062-cc9469e435c3" />

Бачу два відкритих порти: 22 та 80.

В браузері відкриваю 10.0.2.8 з метою розуміння, який саме сервіс працює на 80 порті.
<img width="1138" height="847" alt="image" src="https://github.com/user-attachments/assets/05ded86a-36e5-45fc-aca4-cdfb15fd831d" />

Адреса знаходить назву домену deathnote.vuln і видає помилку

2. По логіці, шукаю хости з допомогою команди whereis hosts з допомогою Kali Linux
<img width="550" height="250" alt="image" src="https://github.com/user-attachments/assets/8510ab00-ae48-4c0c-bd98-ecf251bf3579" />

Результат: hosts: /etc/hosts /usr/share/man/man5/hosts.5.gz

3. Далі роблю запис IP і домену в даній папці з допомогою команди sudo nano /etc/hosts щоб активувати домен
<img width="1610" height="857" alt="image" src="https://github.com/user-attachments/assets/28dbc771-e8d6-4fd9-a75a-e8d242903d6c" />

4. У HTML-коді, в хедері з метаданими, з допомогою F12 знаходжу посилання на директорію, де зберігається картинка, в назві яких міститься "kira". 
<img width="1580" height="744" alt="image" src="https://github.com/user-attachments/assets/fd36c5c5-ee3d-41ba-8bf5-84d64f264acc" />

Заходжу за цією адресою http://deathnote.vuln/wordpress/wp-content/uploads/2021/07 і бачу багато файлів, і два з них у форматі .txt з іменами notes та user.
<img width="1026" height="883" alt="image" src="https://github.com/user-attachments/assets/b6645225-9c10-4c90-8cbc-dfa873499333" />

5. Сканую директорії з допомогою команди dirsearch -u http://deathnote.vuln/ і знаходжу файли /robots.txt і /wordpress/wp-login.php
<img width="877" height="898" alt="image" src="https://github.com/user-attachments/assets/9959af5c-2909-4d43-bb00-9444ababb1dc" />

Заходжу на сторінку deathnote.vuln/robots.txt і бачу текст-підказку зайти на /important.jpg.
<img width="1132" height="340" alt="image" src="https://github.com/user-attachments/assets/aa2c472a-5589-4658-a643-035b20f472ff" />

Далі використувую curl http://deathnote.vuln/important.jpg щоб отримати вміст за вказаним URL, оскільки файл robots.txt не містить нічого крім підказки
<img width="506" height="327" alt="image" src="https://github.com/user-attachments/assets/6c228d01-2ed6-452b-97b0-e3105481b7eb" />

Переходжу на http://deathnote.vuln/wordpress/wp-content/uploads/2021/07 щоб знайти деталі по user.txt і знаходжу user.txt (користувач) і notes.txt (паролі), які можна буде брутфорснути
<img width="970" height="953" alt="image" src="https://github.com/user-attachments/assets/37cabee2-fef3-4179-b3e1-27eb1f7915cb" />
зберігаю notes.txt та user.txt, щоб їх брутфорсити.

6. Брутфорс
Використовую інструмент Hydra: hydra -L user.txt -P notes.txt 10.0.2.8 ssh знаходжу Login: l   password: death4me з домогою яких можу проникнути на сервер
<img width="1026" height="319" alt="image" src="https://github.com/user-attachments/assets/121bf676-dedf-4124-ab5c-c7038ba0c0c2" />

Успішно виконую вхід
<img width="738" height="339" alt="image" src="https://github.com/user-attachments/assets/c41bfeb3-a790-428b-b433-37900d47849c" />

В домашньому каталозі cеред файлів знаходжу файл user.txt
<img width="590" height="501" alt="image" src="https://github.com/user-attachments/assets/f238a7db-ec94-4ace-ade9-112329dd9279" />

В цьому файлі знаходжу зашифрофане послання на мові brainfuck
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>+++++.<<++.>>+++++++++++.------------.+.+++++.---.<<.>>++++++++++.<<.>>--------------.++++++++.+++++.<<.>>.------------.---.<<.>>++++++++++++++.-----------.---.+++++++..<<.++++++++++++.------------.>>----------.+++++++++++++++++++.-.<<.>>+++++.----------.++++++.<<.>>++.--------.-.++++++.<<.>>------------------.+++.<<.>>----.+.++++++++++.-------.<<.>>+++++++++++++++.-----.<<.>>----.--.+++..<<.>>+.--------.<<.+++++++++++++.>>++++++.--.+++++++++.-----------------.

Використовую https://md5decrypt.net/en/Brainfuck-translator/ щоб декодувати повідомлення і отримую текст: "i think u got the shell , but you wont be able to kill me -kira"

7. По логіці шукаю по всієї файловій системі файли та директорії, які мають в назві "kira": 
<img width="1174" height="69" alt="image" src="https://github.com/user-attachments/assets/13d39dc6-abee-4f9c-832d-df6d6f9b9968" />

Переміщуюсь по директоріях командами:
cd ..
cd kira
cd /opt
cd L
cd kira-case

Переглядаю файл "cat case-file.txt" і бачу підказку шукати в директорії fake-notebook-rule
<img width="539" height="290" alt="image" src="https://github.com/user-attachments/assets/47c4a30d-73f6-41a2-a800-02dacac02c93" />

Переходжу в цю директорію і бачу файл  case.wav з інструкційним кодом, перекодовую його у текст — отримую passwd : kiraisevil" 
<img width="2346" height="1271" alt="image" src="https://github.com/user-attachments/assets/0fa383dc-05ec-4755-af3f-8fc19bfd0a4a" />

Переходжу на користувача kira 
-su kira
<img width="591" height="144" alt="image" src="https://github.com/user-attachments/assets/3b2d4ac2-388e-4561-8b06-9c3ab6fe4288" />

та вводжу отриманий пароль: kiraisevil

Далі успішно переходжу на суперкористувача використовуючи цей самий пароль 
sudo -i

Успішно заходжу і змінюю директорію 
cd /root

Переглядаю файли
ls

Знаходжу в файл root.txt та виводжу повідомлення.
<img width="1033" height="418" alt="image" src="https://github.com/user-attachments/assets/1e051f7a-cea3-4704-9906-d812146b0e83" />

