# PicoCTF

## Web Exploitation

### CaaS
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/5fbb2dc5-3a06-4028-a3f3-aa62e80031ec)
<br>
Made an XML HTTP request to the URL.
We can do this inside the console itself.

```javascript
const xhr = new XMLHttpRequest();
xhr.open("GET", "https://caas.mars.picoctf.net/cowsay/somerandommessage");
xhr.send();
xhr.responseType = "json";
xhr.onload = () => {
  if (xhr.readyState == 4 && xhr.status == 200) {
    const data = xhr.response;
    console.log(data);
  } else {
    console.log(`Error: ${xhr.status}`);
  }
};
```
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/914122ac-6492-43b6-83db-f089519cc5f9)
<br>
The request is visible to be successful with a status 304.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/b1365e04-0da6-4222-8e3a-bf88e1bc9214)

