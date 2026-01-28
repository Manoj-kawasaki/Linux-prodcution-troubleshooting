Disk Full Simulation and Resolution

 # Objective

- Simulate a disk full problem in Linux (WSL2) and document the investigation, mistakes, and resolution in a clear, professional way.

* Step 1: Create the Problem

- We created a large dummy file inside /var/log to simulate disk usage:

- sudo dd if=/dev/zero of=/var/log/big.log bs=2M count=5000

- This generated a 10 GB file.

- On a 1 TB disk, usage only went from ~4% → ~5%, so it didn’t look critical.

- Mistake faced: I expected the disk to look “full,” but because the disk was huge, the percentage barely changed.

* Step 2: Detect the Problem

- We checked disk usage:

- df -h

- Showed / usage increasing slightly.

- We tried to find which folder was large:

- sudo du -h --max-depth=1 /

- Initially ran without specifying / → scanned home (~) instead, showing .cache, Downloads, etc.

- Correct command:

- sudo du -h --max-depth=1 / 2>/dev/null | sort -h

- This revealed /var was large.

- Then drilled down:

- sudo du -sh /var/log
- sudo du -sh /var/log/big.log

- Confirmed big.log was 15 GB.

 - Mistakes faced:

 - Forgot to add / after --max-depth=1, so output was from home directory instead of root.

 - Got “Permission denied” errors when scanning /mnt/c (Windows folders). Fixed by adding 2>/dev/null to hide errors.

 * Step 3: Clean the Problem

- Options:

 - Delete file completely

 - sudo rm /var/log/big.log

 - Truncate file (empty but keep it)

 - sudo truncate -s 0 /var/log/big.log

 * Resize file to smaller size

- sudo truncate -s 100M /var/log/big.log

 - We used truncate:

- sudo truncate -s 0 /var/log/big.log

- File size became 0 bytes.

- Verified with:

- ls -lh /var/log/big.log
- sudo du -sh /var/log/big.log

- Both showed 0.

# Mistakes faced:

-Confused why ls showed 100G but du showed 11G → learned about sparse files (logical size vs physical disk usage).

-Finally understood that truncate sets logical size, but only real written data consumes disk space.

* Step 4: Verification

- After cleanup:

- df -h

- Disk usage dropped back down.

- /var/log no longer had oversized files.

# Key Learnings

- df -h → shows overall disk usage.

- du -h --max-depth=1 / → shows which top-level folders are big.
 
- du -sh /var/log/* → shows which files inside /var/log are big.

- ls -lh → shows logical file size.

- du -sh → shows actual disk space used.

- Sparse files: can look huge in ls but small in du.

- truncate → safely empties or resizes files without deleting them.

# Professional Summary
This incident demonstrates my ability to:
- Simulate real-world disk exhaustion scenarios in Linux
- Identify root causes using df and du
- Avoid common operational mistakes (incorrect paths, permission errors, ls vs du confusion)
- Perform safe remediation using truncate and rm
- Validate recovery through post-fix verification
  
  # This shows that I:
- Troubleshoot systematically rather than guessing
- Learn from operational mistakes
- Document clearly for team knowledge sharing
- Communicate technical issues in simple, professional terms

 # Incident 1 successfully completed and documented
