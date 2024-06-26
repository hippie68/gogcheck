# Table of Contents
- [The Script "gogcheck"](#gogcheck)
  - [Modify Script Variables](#modify-script-variables)
- [The Script "makecertfile"](#makecertfile)
- [For Windows Users](#for-windows-users)
  - [Cygwin and Git Bash](#cygwin-and-git-bash)
  - [WSL](#wsl)
- [Q&A](#qa)

# gogcheck

Bash script for the purpose of scanning your GOG offline installer collection for valid digital signatures and correct checksums, making sure your downloads have not been modified by someone else.

Usage: `gogcheck [options] [file/directory ...]`  
The script accepts multiple .exe files and directories as arguments. If no files or directories are specified, the current directory will be used. If neither the -s, -b/-B, nor -i/-I option is used, all checks will be run.

Options:
```
  -b  Enable bin files check
  -B  Same as -b, but disable checksum calculation
  -c  Compact mode: all output but filenames and results is suppressed
  -1  Same as -c
  -C  Disable colors
  -f  Force checks on all exe files (not just setup_*.exe and patch_*.exe)
  -h  Display this help
  -i  Enable Inno Setup check
  -I  Same as -i, but disable test-extracting
  -r  Traverse directories recursively
  -R  Disable RAR test-extracting
  -s  Enable exe digital signature verification
  -S  Silent mode: all output is suppressed; only the 1st exe file is checked
      (Used for exit code checks)
  --  Anything following this is considered a file/directory
```

The script consists of 3 functions, which run in this order:
1. sigcheck: checks .exe files for valid digital signatures
2. bincheck: checks if .bin files' checksums (which the .exe contains) are valid (only means something if sigcheck succeeds)
3. innocheck: test-extracts game files from both .exe and .bin files and verifies their checksums (sometimes which the .exe contains)

Sample output:
```
$ gogcheck setup_a_corrupted_game.exe 
[1] setup_a_corrupted_game.exe
Running signature check...
Current PE checksum   : 0E22C4AF
Calculated PE checksum: 0E22C4DF    MISMATCH!!!
Signature Index: 0  (Primary Signature)
Message digest algorithm  : SHA1
Current message digest    : C421540390F3ACE7D031A4D54F0E5CA539D866AD
Calculated message digest : EEF389073A91EE3470FCC7B5EE0CCDFF7A54BD22    MISMATCH!!!
Signature verification: failed
Number of verified signatures: 1
Failed
Running bin check...
Exe file claims not to have bin files.
No matching bin files found.
Running innoextract check...
117 files (404.23 MiB)
117 checksums (105 SHA-1, 12 MD5)
Test-extracting files...
Extraction successful.

1 file checked, 1 error

Files that produced errors:
[1] setup_a_corrupted_game.exe (digital signature)
```

Sample output (compact mode):
```
$ gogcheck -1
[1] ./setup_a_corrupted_game.exe Error
[2] ./setup_ftl_advanced_edition_1.6.13b_(36400).exe OK
[3] ./setup_terraria_v1.4.1.2_(42619).exe OK
[4] ./setup_the_witcher_adventure_game_1.2.5a_(12082).exe OK

4 files checked, 1 error

Files that produced errors:
[1] ./setup_a_corrupted_game.exe (digital signature)
```

Required programs:
- [osslsigncode](https://github.com/mtrojnar/osslsigncode)
- [innoextract](https://github.com/dscharrer/innoextract) (at least version 1.5 for RAR support)

Optional:
- [unrar](https://www.rarlab.com/rar_add.htm) to let innoextract test RAR archives

## Modify Script Variables
To optionally specify a Certificate Authority (CA) file (also see [makecertfile](#makecertfile)) or to override default program names, edit the script's "USER VARIABLES" section or pass them as command line prefixes:

    certfile=/etc/ssl/certs/ca-certificates.crt osslsigncode_binary=/usr/local/bin/osslsc_2.7 innoextract_binary=inno_1.9 unrar_directory=~/bin gogcheck ...

---
gogcheck may still have bugs. Please report issues at https://github.com/hippie68/gogcheck/issues. Any feedback is very welcome!

# makecertfile

Sometimes new GOG installers may be signed by new certificates that aren't included in the provided (or automatically used) certificate authorities file, causing the following error in osslsigncode/sigcheck:

```
Error: unable to get local issuer certificate
PKCS7_verify error
...
```
The optional "makecertfile" script can be used to create an up-to-date certificate file.  
The script downloads files from external servers: Mozilla's certificate list and additional certificates whose URLs can be added to the script (separated by newlines).  
Be aware the script downloads and executes the third party script "mk-ca-bundle.pl" from the cURL GitHub repository.

Usage: `makecertfile OUTPUT_FILENAME`  
The script will generate a new certificate file named "OUTPUT_FILENAME".  
See [Modify Script Variables](#modify-script-variables) for how to make gogcheck aware of the newly-generated file.

Required programs:
- [cURL](https://github.com/curl/curl)
- [Perl](https://github.com/Perl/perl5)

Optional:
- [sha256sum](https://www.gnu.org/software/coreutils) to let makecertfile verify mk-ca-bundle.pl's integrity

# For Windows Users

The script is confirmed to work with either of the following setups:

- [Cygwin](https://www.cygwin.com)
- [Git Bash](https://gitforwindows.org)
- [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl) (WSL) on Windows 10/11

## Cygwin and Git Bash
You can use the required programs' Windows versions (.exe files). Note that curl.exe is already included in Windows 10/11.

## WSL
You can install the required programs' Linux versions in WSL like this:
```
sudo apt install osslsigncode innoextract unrar curl perl coreutils
```
Mark the scripts as executable: `chmod +x gogcheck makecertfile`.  
To run gogcheck from anywhere inside WSL, for example put it in `/usr/local/bin`: `cp gogcheck /usr/local/bin`.  
When editing the scripts from within Windows, make sure the editor you are using is respecting the Unix/Linux "LF" newline character format. On a recent, fully updated Windows 10/11 build, notepad.exe can be used. To instead edit the scripts from within WSL, use a Linux editor (for example nano: `nano gogcheck`; save with Ctrl-x).  

# Q&A
What does it mean if sigcheck's output goes green?

- It means the string that went green is known to the script. The latter which contains a section in which you can put known-legit strings found in your purchased games. This pre-made string collection is not complete. However, as this optional feature is just there for visual convenience, to quickly spot both known and new strings, it does not affect osslsigncode's functionality.

Is RAR support required?

- As innoextract does not know checksums for files stored inside RAR bin files (as opposed to Inno Setup bin files), the verification chain "valid .exe digital signature -> verified .bin checksums -> verified .bin archive contents" is broken at the final stage. You can still let innoextract use unrar/unar to check for regular CRC errors.
