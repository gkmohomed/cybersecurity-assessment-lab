# Autopsy Digital Forensics Notes

Case: Metasploitable2-Forensics-2026
Host: Metasploitable2-Portfolio

Image: Metasploitable.vmdk (~1.9 GB)

Partition 1:
- Type: Linux (0x83)
- Sector range: 63 - 481949
- Observed filesystem: ext

Partition 2:
- Type: Linux LVM (0x8e)
- Sector range: 482013 - 16771859
- Observed as a raw/LVM container in this Autopsy workflow

The ext filesystem was inspected in File Analysis mode. Autopsy displayed file metadata, including timestamps, file size, UID/GID and metadata identifiers. The Deleted Files view showed GRUB backup/temporary entries and older kernel-related entries.

Forensic interpretation: deleted/orphaned entries were treated as artifacts requiring context, not as automatic evidence of malicious activity.
