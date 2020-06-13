Bash scripts for the purpose of scanning your GOG offline installer collection for valid digital signatures and correct checksums, making sure your downloads have not been modified by someone else.
The scripts accept multiple .exe files and folders as arguments. No arguments: check the current folder.

To make sure all files are original, use the scripts in the following order:
1. sigcheck: checks .exe files for valid digital signatures
2. bincheck: checks if .bin files' checksums (which the .exe contains) are valid (only means something if sigcheck succeeds)
3. innocheck: test-extracts game files from both .exe and .bin files and verifies their checksums (which the .exe contains)

Required packages (Debian/Ubuntu):
- sigcheck: osslsigncode
- bincheck: binutils
- innocheck: innoextract

For Windows users: The scripts also work with the Windows Subsystem for Linux (WSL) on Windows 10
-> https://docs.microsoft.com/en-us/windows/wsl/

You may need to download or compile the latest version of innoextract if your distro's package is outdated.
-> https://constexpr.org/innoextract/#download
Some GOG games' .bin files are RAR archives. Their content's checksums are not known to the .exe file, so in such a case the script skips testing to save time.
