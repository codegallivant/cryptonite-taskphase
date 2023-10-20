# OverTheWire

## Task 1

### Level 0
Use the credentials to ssh into the server
```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

### Level 0-1
Use cat command to get readme contents (```cat readme.md```)

### Level 1-2
1. use cd .. to go into home directory
2. ``find -name "-"`` to get file with name "-"
3. then use cat command to get contents of file. regular ``cat -`` will not work. use ``cat < -``. Because it is a dashed filename, it causes the terminal to confuse the hyphen with stdin and expect input.

### Level 2-3
navigate to /home parent and use find command  (``find /home -name "spaces in this filename"``). then use cat to get contents.

### Level 3-4
Just enter ``find`` with no parameters/options. this gives ALL filepaths in the directory. Then perform cat on the revealed filepath ``./.hidden``.

### Level 4-5
Navigate to /inhere and perform cat on all files until you get human readable one
Edit: could have used ``grep text data.txt`` or ``strings data.txt`` instead of finding it manually

### Level 5-6
Use ``du -all -b`` to get sizes of all(-a) files in bytes(-b). Find the file with 1033 byte size. Check contents for human readability. 
Edit: could have used ``find -size 1033`` instead of finding 1033 byte file manually. 

### Level 6-7
1. ``find ./* -user <user_owner> -group <group_owner> -size <size>`` in highest directory
2. perform ``cat``

### Level 7-8
Refer grep docs. Option -w returns line with matching expression. 
```
grep -w "millionth" data.txt
```

### Level 8-9
Use pipe character to pipe command output to another command
pipe from cat to sort to uniq -u
```cat data.txt | sort | uniq -u```

### Level 9-10
1. Extract human readable part (text) using strings command
2. pipe the strings output into the grep -w "=" command
```
strings data.txt | grep -w "="
```

### Level 10-11
pipe cat into base64 -d
```
cat data.txt | base64 -d
```

### Level 11-12
use tr for text rotation. google syntax.
1. pipe cat into tr once for lower case character rotation
2. pipe once again for upper case character rotation
```
cat data.txt | tr [a-z] [n-za-m] | tr [A-Z] [N-ZA-M]
```

<br><br>
**Password to enter level 12-13:** ``JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv``
