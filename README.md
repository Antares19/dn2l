# dn2l
dn2l — Dos Navigator Open Source Project linux port tryouts

To build and run (very experimental) dn2l, use this one-liner:
```
wget https://raw.githubusercontent.com/unxed/dn2l/main/linux/init.sh && chmod +x init.sh && ./init.sh
```

### Current state:
![](https://user-images.githubusercontent.com/1151423/97242979-4c113a80-1806-11eb-8b8a-b3419a738b48.png)

### How to build with IDE?

1. Run `wget https://raw.githubusercontent.com/unxed/dn2l/main/linux/init.sh && chmod +x init.sh && ./init.sh` to get all needed files and unpack them. dn2l will be run automatically on finish, you should just close it.
2. `cp -rf dn2l_tryouts/vp/vp* dn2l_tryouts/dn2l`
3. `chmod +x dn2l_tryouts/dn2l/vp`
4. `mkdir -p dn2l_tryouts/dn2l/out.lnx/units`
5. `cd dn2l_tryouts/dn2l`
6. Run `./vp`
7. Go to `Options`-`Compiler`-`Unit aliases` and remove `;Messages=Windows`
8. Go to `Options`-`Directories` and replace `{BASEDIR}` with `.` in `Output directory` and `Exe output directory`
9. Open `dn.pas`, `Compile`-`Build` (it generates .exe, don't be surprised, it has linux binary code inside)
10. Run `pe2elf` from `dn2l_tryouts/vp` on `dn2l_tryouts/dn2l/out.lnx/dn.exe` to get executable ELF file for linux
11. Do `chmod +x` on `dn` file generated by pe2elf

### Possible further improvements (no concrete plans on it all, though)

- Fix remaining bugs
- Switch to Free Pascal Compiler
- Add Unicode support

### Roadmap (WIP)

1) Add directives required by fp to stdefine.inc (inside fp-only conditional compilation block)
2) Rename collect.pas to objects.pas (so that the names of all tv units matchs fv ones — needed for step 4)
3) Move all inline functions to the implementation blocks
4) In a separate branch, remove everything from vp and tp at all and try to build with fp and fv. Write shims for all VP RTL specific functions. Use code from DN TV to add DN extensions to the FV only if that code does not come directly from the TP RTL sources, but is written by DN people and, as therefore, licensed under DN license. Mark all such code with a clear comment.
5) Switch fv to utf8 the same way it is done here: github.com/magiblot/tvision

### Playing with the code

- Some parts that are failing are commented out for now. You can search code by string "by unxed" to find out such disabled code paths if whishing to fix some of them.
- If you plan to try building dn2l with Free Pascal Compiler, don't forget to use -Sd switch and to add {$asmMode intel} directive to all files with assembly code. Also FPC does not supports "inline" functions having body before "implementation" part of a unit, AFAIK.
- dn2l internal screen rendering code currently supports only one-byte-per-character charsets (it is hardcoded cp866 for now, see vpsyslnx.patch). To switch to UTF-8 we possibly should move from "Char" to "array[0..3] of Char" in TScrCell structure defined in vpsyslnx.pas, change PScrBuffer/TScrBuffer (and other screen buffer arrays, you can search for "TAWordArray(" to find some) definitions also, and rewrite all code that works with such buffers as arrays of Words.
- Don't forget about assembly code inside views.vp and drivers.vp as it also works with screen buffers. We probably should consider rewriting all such assembly parts in Pascal as this also simplifies porting to FPC. Let's be guided by the principle "first we will make it work at all, and only then we will make it work quickly".
- Avoid removing currently unused code. Code paths for Win32/OS2/DPMI support may be commented out if they prevent linux port from working, but should stay in code base for someone wanting to make dn2l cross platform someday.

### Licensing and legal notices

License: modified BSD with an exception prohibiting re-licensing under another license, including, but not limited to, GPL. See LICENSE.txt for full text.

Currently dn2l is built using freeware Virtual Pascal compiler obtained from old-dos.ru (big thanks, old-dos.ru team!). It is redistributed with dn2l in it's original unmodified form as permitted by license agreement. See vp_dist/LICENCE.TXT for more information on Virtual Pascal distribution terms.

The original version of the source code taken from DN open source project (dnosp.com) contained some files from the RTL libraries of Virtual Pascal. The main developer of dn2l is a programmer, not a lawyer, and therefore not sure if modified versions of those files can be redistributed or not (the Virtual Pascal license agreement prohibits "distribution of a modified version of Virtual Pascal", and it's unclear if this applies to RTLs supplied in source code form, or not). Anyway, to avoid possible legal problems, all such files were removed from the source code tree. Patches with the necessary changes over the original sources from VP RTL are used instead, so there are no modified VP parts bundled with dn2l, just as VP license says. Those patches are:
1) sysutils.patch, applied upon sysutils.pas header file from VP. According to FSF, headers can not be copyrighted.
2) vpsyslnx.patch, applied upon vpsyslnx.pas from VP RTL src. Contains only minimal required quotations from original, so definitly a fair use.
3) vpsyslow.patch, vpsysd32.patch, vpsysos2.patch, vpsysw32.patch — not used by dn2l at all, provided for educational and historical purposes only, contain only minimal required quotations from originals, so should be considered a fair use also.

The main developer of dn2 is not sure about exact licensing terms for the DN code changes that were made by the DN OSP team (including the listed patches). Some of them do not belong to the original DN source code files published by Ritlabs and do not contain any licensing information inside. He assumes the distribution terms for that code can be considered to be the same as for the entire project, since this code was written specifically for this project and was distributed in the same archive with it.

dn2l also contains "English.DLG" and "English.LNG " resouce files. Those files should be generated using rcp.pas from config files in RESOURCE folder, but rcp.pas is currently broken, so ready to use resource files are taken from DN binary distribution. The source scripts for those files are licensed under the same conditions as entire DN.

#### 28-10-2020 update

It turned out that some of the source code files originally published by Ritlabs and therefore inherited by dn2l were based on the source code of Turbo Vision for Turbo Pascal, never published by Borland under any permissive license. As long as we have no reason to doubt the good faith of Ritlabs, we can assume that they had the right to use this code in their open source product. We also still can consider usage of that code in dn2l as fair use, since no profit is being made from dn2l, dn2l does not pose any threat to the business of Embarcadero Technologies, dn2l is using only the minimum necessary parts from original Turbo Vision sources, and the entire dn2l project has historical and educational value. Yet it's better to consider use of this code in dn2l only a temporary solution: we should gradually replace such code with Free Vision from the Free Pascal project, this should be a first priority task as solid legal base is required to secure any further development of dn2l. Here is a full list of such files: collect.pas, colorsel.pas, dialogs.pas, drivers2.pas (not sure about it, possibly a mistake), drivers.pas, histlist.pas, memory.pas, menus.pas, tvhc.pas, validate.pas and views.pas.

### NDN already works on Linux, shouldn't we just ask its devs to open the code?

Opening the NDN code would be great, but we shouldn't expect that we could gain so much from opening its source. The author of dn2l examined the NDN debug symbols (they are published with the freeware binary distribution), and found the following:

1) ndn.dbg contains both the line "Free Pascal 3.2.0 2020/10/18" and the line "VPSYSLOW.PP", so it seems to be built by Free Pascal Compiler, but on the top of Virtual Pascal RTL. VPSYSLOW module originates from the Virtual Pascal distribution (its modified version is present in DN OSP sources also). That is, NDN is still somethat based on the VP, which dn2l plans to get rid of over time.

2) The ndn.dbg contains the line "COLLECT.PAS", which indicates the continued use of the custom version of Turbo Vision from DN (the corresponding file from the Free Vision kit is called differently: objects.pp), and DN's TV, as we now know, is based on code from Turbo Pascal, the possibility of using which in the FOSS project is still not unambiguous.

Conclusions: NDN seems to have been created without concern for license clarity. It looks like it have code borrowings from both Borland and Virtual Pascal. In a public project, in any case, such code would have to be cut with transition to free licensed analogs, and this is almost equivalent in time costs to reworking a project from DN OSP sources.

So, perhaps you shouldn't pester NDN developers with a proposal to open the code: most likely, dn2l will not be able to get so much benefit from it, because we are striving for 100% license purity. Some components, maybe, could be reused, but one should not expect that NDN will open the code, we will merge it, and get completely FOSS DN with no legal problems.

**The main work to be done is TV->FV transition, and this has to be done regardless of whether we will develop our own sources or switch to hypothetical open sources of NBN. By the way, this also applies to the ANSI->UTF8 transition, which is definitely unpromising for the DN TV code (what is the point of investing in code inherited from proprietary software and licensed under incomprehensible conditions?), but quite promising for FV (the entire open source community could benefit from it).**

And, if we are talking about transition to Unicode, I want to note that there is a successful example of switching Turbo Vision to UTF-8, and, although it's a version for the C++17 language, this proves that the transition with moderate time costs is possible: github.com/magiblot/tvision

Anyway we should thank NDN devs for usable Linux DN version and for proofing that such port is even possbile. Guys, you are great!

### Acknowledgments

Based on Dos Navigator by RIT Research Labs, with many thanks for opening the code. Also many thanks to DN OSP team for making old style DOS source code 32-bit and multi-platform friendly. Thanks to old-dos.ru team for hosting Virtual Pascal distribution — dn2l, perhaps, would never have been born without the ability to find and download VP. Thanks to VP developers for making their product available as freeware — without permission to use their compiler, dn2l might never have been built. And also thanks to habr.com community for inspiration!
