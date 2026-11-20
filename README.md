# EXT2/EXT3 file system simulator — Go + Next.js

A file system simulator in Go. It creates virtual disks as binary files, partitions them with MBR/EBR, formats partitions as **EXT2 or EXT3**, and manages folders, files, users, groups and permissions inside them. The web UI has a terminal for commands and scripts, a login, and a graphical file explorer.

Course project for *Manejo e Implementación de Archivos* (Universidad de San Carlos de Guatemala, 1st semester 2025). Individual work. The last commit prepares the backend for deployment on AWS EC2.

## What it implements

- **Disks and partitions:** `mkdisk`, `rmdisk`, `fdisk` (primary, extended and logical partitions; create, grow, shrink, delete), `mount`, `unmount`, `mounted`
- **File system:** `mkfs` for EXT2 or EXT3. It handles the superblock, inode and block bitmaps, inodes with direct and indirect pointers, and folder, file and pointer blocks, all written byte by byte into the disk file.
- **EXT3 journaling:** creating folders and files (`mkdir`, `mkfile`) writes an entry to the journal. `loss` simulates a crash by wiping the superblock, bitmaps, inodes and blocks of a partition, and `recovery` rebuilds the file system by replaying the journal. `journaling` shows the log.
- **Files and folders:** `mkdir`, `mkfile`, `cat`, `edit`, `rename`, `copy`, `move`, `remove`, `find`
- **Users and permissions:** `login`, `logout`, `mkgrp`, `rmgrp`, `mkusr`, `rmusr`, `chgrp`, `chown`, `chmod` (UGO permissions stored in `/users.txt` inside the partition)
- **Reports** rendered with Graphviz: MBR, disk, inode, block, bitmaps, superblock, tree, file and `ls`

## Architecture

```text
Next.js (terminal · file explorer · login) ──POST /execute──► Go Fiber API ──► analyzer ──► commands
                                                                                    │
                                                    structures (MBR, EBR, superblock, inode, journal…)
                                                                                    │
                                                                 binary .mia disk files ──► Graphviz reports
```

## Run it

Requirements: Go 1.23+, Node.js with pnpm, and Graphviz (`dot`) for the reports.

```bash
cd backend && go run .                          # API on :8000
cd frontend && pnpm install && pnpm dev         # UI on :3000
```

There is also a `backend/dockerfile`.

## Docs (Spanish)

[Technical manual](docs/ManualTecnico_ext2.pdf) · [User manual](docs/ManuaUsuario_ext2.pdf)

The first version (EXT2 only, without journaling or the file explorer) is in [ext2-filesystem-simulator](https://github.com/AbdielOtzoy/ext2-filesystem-simulator).
