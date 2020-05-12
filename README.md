# gogverification

Verify your GOG offline installers' authenticity and integrity using BASH command line scripts.

- sigcheck: checks .exe files for valid digital signatures
- bincheck: checks if .bin files have valid checksums
- innocheck: test-extracts game files and verifies their checksums

Required packages:
- binutils
- osslsigncode
- innoextract
