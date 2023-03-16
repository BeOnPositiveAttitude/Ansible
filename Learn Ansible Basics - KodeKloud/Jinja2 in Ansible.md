```
{{ "/etc/hosts" | basename }}   => hosts   #получить имя файла из полного пути, не работает для Windows из-за "\"
{{ "c:\windows\hosts" | win_basename }}   => hosts   #получить имя файла из полного пути для Windows
{{ "c:\windows\hosts" | win_splitdrive }}   => ["c:", "\windows\hosts"]   #сделает сплит и вернет список из буквы диска и пути
{{ "c:\windows\hosts" | win_splitdrive | first }}   => "c:"   #сделает сплит и вернет первый элемент списка
{{ "c:\windows\hosts" | win_splitdrive | last }}   => "\windows\hosts"   #сделает сплит и вернет последний элемент списка
```