Bash scripts to scan your whole GOG offline installer collection or single files for valid digital signatures and correct checksums. Make sure your downloads have not been modified by someone else.
The scripts accept multiple files and folders as arguments. No arguments: check the current folder.

To make sure all files are original, use the scripts in the following order:
1. sigcheck: checks .exe files for valid digital signatures
2. bincheck: checks if .bin files have valid checksums (only means something if sigcheck succeeds)
3. innocheck: test-extracts game files and verifies their checksums

Required packages (Debian/Ubuntu):
- sigcheck: osslsigncode
- bincheck: binutils
- innocheck: innoextract

You may need to compile the latest version of innoextract yourself if your distro's package is outdated. Innoextract 1.8 or later is required to extract all current GOG offline installers. -> https://github.com/dscharrer/innoextract
