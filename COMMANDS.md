# Hashcat Command Reference

This guide documents the command patterns relevant to the project report. Use a controlled lab and hashes you own or have explicit permission to audit. The examples below use **MD5** (`-m 0`) because that is the hash mode described in the report.

## 1. Install Hashcat on Kali Linux

Hashcat is available in Kali's package repositories:

```bash
sudo apt update
sudo apt install hashcat
```

Verify the installation and inspect the built-in help:

```bash
hashcat --version
hashcat --help
```

## 2. Prepare the RockYou wordlist

On Kali, the RockYou wordlist is commonly installed compressed. Extract it if needed:

```bash
sudo gzip -dk /usr/share/wordlists/rockyou.txt.gz
```

If the file has already been extracted, do not run the extraction command again. The wordlist path used below is:

```text
/usr/share/wordlists/rockyou.txt
```

## 3. Dictionary (straight) attack

**Purpose:** compare each candidate in a wordlist with the supplied hashes.

```bash
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

- `-m 0`: MD5 hash mode.
- `-a 0`: straight/dictionary attack mode.
- `hashes.txt`: input file containing one authorized test hash per line.
- `rockyou.txt`: candidate-password wordlist.

View recovered results afterward:

```bash
hashcat -m 0 hashes.txt --show
```

## 4. Mask attack

**Purpose:** test candidates matching a known character pattern. The report describes a 10-character pattern consisting of one uppercase letter, one lowercase letter, and eight digits. Its corresponding mask is:

```bash
hashcat -m 0 -a 3 hashes.txt '?u?l?d?d?d?d?d?d?d?d'
```

Mask symbols used in this example:

| Symbol | Character set |
|---|---|
| `?u` | Uppercase letters (A–Z) |
| `?l` | Lowercase letters (a–z) |
| `?d` | Digits (0–9) |
| `?s` | Special characters |
| `?a` | All supported character classes combined |

The mask above is a targeted search pattern, not a universal password pattern. Use it only when the structure is known from the authorized test case.

## 5. Brute-force example

In Hashcat, brute-force is performed with attack mode `-a 3` and a mask. For a small four-digit test space:

```bash
hashcat -m 0 -a 3 hashes.txt '?d?d?d?d'
```

This tests all 10,000 four-digit combinations. Increasing the mask length or character set can make the candidate space grow very quickly.

## 6. Useful status and result commands

Show recovered entries:

```bash
hashcat -m 0 hashes.txt --show
```

Show remaining/unrecovered hashes:

```bash
hashcat -m 0 hashes.txt --left
```

Show available compute devices:

```bash
hashcat -I
```

## Notes on the original report's command examples

The report includes a dictionary command using `password.txt` while its explanation refers to `hashes.txt`; this guide uses the consistent filename `hashes.txt` for clarity. The brute-force line in the report does not include a mask and redirects output with `>>`; Hashcat's mask mode requires a candidate mask, so this guide provides a complete illustrative command instead. The commands here are documented examples and are not claimed to have been rerun as part of this packaging update.

## Safety

Only use Hashcat with your own test hashes or with written authorization. Never use these commands against third-party accounts, services, or data.
