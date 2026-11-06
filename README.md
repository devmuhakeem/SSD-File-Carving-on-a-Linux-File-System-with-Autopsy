# SSD File Carving on a Linux File System with Autopsy

A hands-on forensics lab performing file carving — recovering files from unallocated disk space purely by recognizing their internal byte structure, with no file system metadata available at all — against a forensic SSD image using Autopsy.

## Scenario
File carving is the recovery technique used when a file's metadata (name, directory entry, timestamps) is completely gone, leaving only raw, unallocated disk space. This lab worked through that process against a Linux SSD image where TRIM (which proactively erases deleted blocks on SSDs) had been disabled, preserving the underlying bytes even though all file system records of them were gone.

## What I did

### 1. Set up a documented case
- New case (named for this task), dedicated base directory, examiner details and case number recorded in Optional Information — same chain-of-custody discipline as a standard disk-image analysis case

### 2. Added the SSD image as a data source
- Selected **Disk Image or VM File**, browsed to and selected `Linux_Evidence_SSD.dd` (a raw `.dd` image)
- Configured ingest modules and began analysis
- Noted Autopsy consumes significant system resources during a carving scan — the lab specifically recommended not interacting with the machine until scanning completed

### 3. Waited for carved files to populate
- Selected the evidence file in the left pane and waited roughly 10–15 minutes for a **Carved Files** folder to appear in the right pane — the scan working through unallocated space looking for recognizable file signatures
- Carved files appeared with generic, auto-generated names (e.g. `f0203056.jpg`) rather than any original file name, since that information lived in metadata that no longer exists

### 4. Inspected and extracted a carved file
- Selected a carved image file and confirmed it displayed real, viewable image content in the Application tab — confirming successful reconstruction, not a corrupted fragment
- Reviewed the same Hex, Text, and File Metadata tabs used in a prior full-image-analysis lab for additional inspection angles
- Right-clicked the file → **Extract File(s)**, saved to the case's auto-organized `Export` subfolder
- Navigated to that folder directly and confirmed the recovered image was present and usable as a standalone file

## Key takeaways
- File carving recovers files with zero reliance on file system metadata — it scans raw unallocated space directly for recognizable file signatures (e.g. a JPEG's distinctive header structure)
- TRIM is an SSD-specific consideration: when enabled, it proactively erases deleted blocks for performance reasons, which can make carving impossible if the underlying bytes have already been wiped — this lab's premise specifically depended on TRIM being disabled
- Carving recovers content, not identity — a carved file has real, viewable content but no original name, folder path, or creation timestamp, since none of that metadata survives when the file system record is gone
- This is a computationally heavier technique than hashing, comparison, or standard file-system browsing — scanning an entire disk's unallocated space for byte patterns is resource- and time-intensive, which is why wait times and resource usage were explicitly called out
- Carving is a last-resort recovery method, used precisely when every other technique (which assumes at least some surviving metadata or record) has nothing left to work with

## Tools
Autopsy 4.14.0

---
*Completed in an isolated training lab environment using a provided forensic SSD image.*
