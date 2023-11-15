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
```python
print(hashlib.sha256(bUsername_trial).hexdigest())
indexes = [4,5,3,6,2,7,1,8]
key_middle_part = ""
for i in indexes:
    key_middle_part += hashlib.sha256(bUsername_trial).hexdigest()[i]
full_key = key_part_static1_trial + key_middle_part + key_part_static2_trial
print(full_key)
```
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/84c29319-c585-498f-b408-baba8e140bca)
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/ea97d16f-2093-4109-9eed-911bc88ed7f4)
<br><br>
The 2nd line is the flag.
<br>
**Flag obtained:** ``picoCTF{1n_7h3_|<3y_of_0d208392}``

### GDB Baby Step 1
When I opened the file in a text editor it just had random symbols and there were some occassional commands. To find out more I ran the ``file`` command on it.
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/0a97a4ff-ba7b-4269-920f-160ac2f24f26)
I had no idea what an ELF 64 bit executable was so I googled and found these 2 sources - 
<br>
Details on ELF files: https://medium.com/@ajmewal/basics-of-elf-executable-and-linkable-format-file-88a516877356
<br>
ELF files and disassembly: https://www.codementor.io/@packt/reverse-engineering-a-linux-executable-hello-world-rjceryk5d
```
ELF is short for Executable and Linkable Format. It's a format used for storing binaries, libraries, and core dumps on disks in Linux and Unix-based systems. Moreover, the ELF format is versatile. Its design allows it to be executed on various processor types.
```
So basically this ELF file was an executable. Its in binary format. Viewing it without first converting it to another format is futile unless you can read it. 
So I had to use certain commands to convert it to somewhat readable form (assembly language). This is called disassembling it.
```
objdump -d debugger0_a > disassembly.asm
```
```
cat disassembly.asm
```
The disassembly showed the sections of the file and the several functions within each section.
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/374f37d0-468c-4d0c-abd1-8d78f7ae786b)
<br>
The ``main`` function is visible as well. On line 1138, I saw the ``eax`` register, beside which ``0x86342`` was written.
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/693c92a1-ae03-4cce-b988-7dc9ec0dab92)
<br>
Converting ``0x86342`` to decimal, we obtain ``549698``.
<br>
**Flag obtained:** ``picoCTF{549698}``

### ARMssembly 0
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/70357b29-21c9-4ce1-8b66-85946a2c7b22)
<br>
I tried running it using -
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/3292d4e5-b1c6-4371-9d13-44e1c6d5612f)
<br>
So its a ARM-V8-a (32 bit) file and I can't run it this way because I'm using an x86_64 (64 bit) OS.
I thought of downloading a 32 bit gcc compiler for this but maybe that's not an optimal method. I tried reading through the assembly to see if I could understand what commands were being executed but I couldn't. I got stuck at this point but apparently I was half-right. We have to download a few libraries to run this ARM-V8 file. 
<br>
I followed this guide to run ARMV8 on x86_64 machines on linux command line - 
<br>
https://github.com/joebobmiles/ARMv8ViaLinuxCommandline
<br>
First, to convert the .S file (assembly) to .o file (machine code) and then .o to .exe (executable). I had to install (``sudo apt install``) ``binutils-aarch64-linux-gnu`` and ``aarch64-linux-gnu-gcc`` linux libraries for these conversions.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/58e68877-7e3a-4087-93ee-01ff9df703d5)
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/73b9cb91-687a-4075-9e4e-89d632a6ee5a)
<br>
However, even after this, if I try to run the file, I receive an error - 
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/f37332fb-2718-4d84-b955-49d3d95e6aed)
<br>
This is because this executable can still only be run on ARM-v8 architecture. In order to emulate this architecture, we install another library - ``qemu-user-static``. After installing this, we can run the executable successfully.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/eaa54c21-c44a-4b5b-8bd1-d3af7f4ea512)
<br>
Decimal 4134207980 in hexadecimal is F66B01EC.
<br>
**Flag obtained:** ``picoCTF{F66B01EC}``
<br><br>
Edit: Could have used ghidra for decompiling files and HxD as a hex editor to read assembly language from binary files.

## Binary Exploitation
### stonks
Looked through the file. It's not the complete code so it didn't say much. It's printing the token at some point though. I tried printing off the recent stack storage by adding a bunch of ``%p``'s in the input.
```
0x8e951800x10x8e963900x8e963b00x6f6369700x7b4654430x306c5f490x345f74350x6d5f6c6c0x306d5f790x5f79336e0x633432610x366134310xff94007d0xf7fb9af80xf7f8c4400xec450c000x1
```
Replaced every "0x" with a space. 

```
8e95180 1 8e96390 8e963b0 6f636970 7b465443 306c5f49 345f7435 6d5f6c6c 306d5f79 5f79336e 63343261 36613431 ff94007d f7fb9af8 f7f8c440 ec450c00 1
```

Verifying the hexdump was translated fully (I had to do this a lot because of formatting the hexdump incorrectly many times) - 
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/2b137650-9640-4f4a-9b33-ad824363e954)
<br>
Copied the outputted key -
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/150c38a5-b8c7-46e7-8a36-e671d7c576bd)
<br>
Its still seems to need to be formatted though, as every 4 letters are reversed (visible in known part of the string ``pico{CTF``).
Created a script to do this - 
```py
s = "ocip{FTC0l_I4_t5m_ll0m_y_y3nc42a6a41}"

s2 = ""
for i in range(0, len(s), 4):
	substr = s[i:i+4]
	revsub = ""
	for i in range(0, len(substr)):
		revsub = revsub + substr[len(substr)-i-1]
	s2 = s2 + revsub

print(s2)
```
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/5da0136b-23ce-4a30-9713-9903005be50d)
<br>
**Flag obtained:** ``picoCTF{I_l05t_4ll_my_m0n3y_a24c14a6}``

### babygame01
Used ghidra to go through the downloaded file. Found that entering "p" goes to X immediately. However, after that it just says "You win!" without printing the flag. I checked for the flag in the places above the player's initial position, only to find it 4 tiles behind tile (0,0). Then I had to press "p" to get to destination X, after which it printed the flag as well.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/20a2848f-0844-4d81-87f3-cee485b73194)
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/17c4b523-61da-405a-9eaf-20f47aa32448)
<br>
**Flag obtained:** ``picoCTF{gamer_m0d3_enabled_f4f6ad7d}``

### buffer overflow 0
Looked through the code. Went through <signal.h> SIGSEGV docs. The flag will be printed whenever an invalid memory location is accessed. 
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/1db49924-0f37-4d8c-8958-9f4189f33f5a)
<br>
The 3rd hint tells me to look at the BUGS section in ``man gets``. I did so, and found that man gets tries to access characters past the end of the buffer. 
In the following snippet, buf1 is seen to have a max size of 100. So I inputted a string with more than 100 characters.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/0b97fcd9-af46-42c0-80c3-151286c6afb9)
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/68d22c1c-f030-4dc3-9182-d07778ead62c)
**Flag obtained**: ``picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}``

## Forensics
### Trivial File Transfer Protocol
Downloaded the .tftp file. Used wireshark to view it. It looked like some files were being transferred in a series or TFTP protocol requests. I downloaded all the transferred files, which looked as follows - 
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/4d58b171-79b1-4cb5-87b3-88097f9c89b2)
<br>
The pictures and the .deb file didnt have anything immediately noticeable in them so I decided to deal with the .txt files first. Their contents were ROT13 encoded. I used https://dcode.fr/ to detect this. 
<br>
Decrypted ``instructions.txt`` - 
```
TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN
```
Decrypted ``plan.txt`` - 
```
IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS
```
``instructions.txt`` clearly tells me to look at ``program.deb`` and so I did. I extracted the files using - 
```
dpkg -x program.deb debpack
```
Looking through the directory, I found ``debpack/usr/bin/steghide`` seemed to be of interest. I tried putting it through ghidra but that didn't really give me much information. Then I looked at ``debpack/usr/share/doc/steghide/README/README``. It clearly states - 
<br>
```
After you have embedded your secret data as shown above you can send the file
picture.jpg to the person who should receive the secret message. The receiver
has to use steghide in the following way:

  $ steghide extract -sf picture.jpg
  Enter passphrase:
  wrote extracted data to "secret.txt".

If the supplied passphrase is correct, the contents of the original file
secret.txt will be extracted from the stego file picture.jpg and saved
in the current directory.
```
Okay, so I have to use the command ``steghide extract -sf picture.bmp``. The passphrase for info extraction is indicated in ``plan.txt`` - ``DUEDILIGENCE``. 
Using the command on ``picture1.bmp`` and ``picture2.bmp`` didn't work, but when used on ``picture3.bmp``, the flag was written into ``flag.txt``.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/87d5ed5e-b6b6-4980-ac17-109ec5eace0a)
<br>
**Flag obtained:** ``picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}``

### Macrohard WeakEdge
First I opened the .pptm file in HxD. I checked the first two characters, which were ``PK``. I checked online about whether this was possibly a file header, which it was. It was a PKZip file. I was initially trying to find a PKZip extractor but decided to use 7zip. 
<br>
After extracting the file, I started navigating through the directories trying to find something that seemed relevant to the flag. I found ``ppt/vbaProject.bin`` and spent a quite a bit of time trying to decode it but turns out it wasn't the actual target.
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/5646e0cd-a281-445b-a0e4-6a81488ce9b1)
<br>
After searching for hidden files in the directory -
<br>
![image](https://github.com/codegallivant/cryptonite-taskphase/assets/27366422/9bfd4a61-0a48-438a-8f29-6879251e436f)
<br>
After removing the spaces and decoding using a base64 decoder - 
<br>
**Flag obtained:** ``picoCTF{D1d_u_kn0w_ppts_r_z1p5}``
