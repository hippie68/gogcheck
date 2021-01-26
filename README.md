Bash script for the purpose of scanning your GOG offline installer collection for valid digital signatures and correct checksums, making sure your downloads have not been modified by someone else.
The script accepts multiple .exe files and folders as arguments. No arguments: check the current folder. Type "gogcheck -h" to list available options.

The script consists of 3 functions, run in this order:
1. sigcheck: checks .exe files for valid digital signatures
2. bincheck: checks if .bin files' checksums (which the .exe contains) are valid (only means something if sigcheck succeeds)
3. innocheck: test-extracts game files from both .exe and .bin files and verifies their checksums (sometimes which the .exe contains)

Required programs:
- osslsigncode (https://github.com/mtrojnar/osslsigncode)
- innoextract (https://github.com/dscharrer/innoextract)

Optional:
- unrar to let innoextract test RAR archives (https://www.rarlab.com/rar_add.htm)

For Windows users: The script also works with the Windows Subsystem for Linux (WSL) on Windows 10  
-> https://docs.microsoft.com/en-us/windows/wsl/

You may need to download or compile the latest version of innoextract (at least 1.5 for RAR support) if your distro's package is outdated.  
-> https://constexpr.org/innoextract/#download

gogcheck may still have bugs. Please report issues at https://github.com/hippie68/gogcheck/issues. Any feedback is very welcome!

Q&A:
----

What does it mean if sigcheck's output goes green?

It means the string that went green is known to the script. The latter which contains a section in which you can put known-legit strings found in your purchased games. This pre-made string collection is not complete. However, as this optional feature is just there for visual convenience, to quickly spot both known and new strings, it does not affect osslsigncode's functionality.

Is RAR support required?

As innoextract does not know checksums for files stored inside RAR bin files (as opposed to Inno Setup bin files), the verification chain "valid .exe digital signature -> verified .bin checksums -> verified .bin archive contents" is broken at the final stage. You can still let innoextract use unrar/unar to check for regular CRC errors.
