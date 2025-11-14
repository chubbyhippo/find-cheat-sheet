# find-cheat-sheet
### Find using wildcards
```bash
find /path -name "*.txt"          # All .txt files
find /path -name "file*"          # Files starting with "file"
find /path -name "*config*"       # Files containing "config"
```

## Search by Type

```bash
find /path -type f                # Regular files
find /path -type d                # Directories
find /path -type l                # Symbolic links
find /path -type b                # Block devices
find /path -type c                # Character devices
find /path -type p                # Named pipes (FIFOs)
find /path -type s                # Sockets
```

## Search by Size

```bash
find /path -size 50M              # Exactly 50 MB
find /path -size +50M             # Larger than 50 MB
find /path -size -50M             # Smaller than 50 MB
find /path -size +1G              # Larger than 1 GB
```

**Size units:**
- `b` - 512-byte blocks (default)
- `c` - bytes
- `k` - kilobytes
- `M` - megabytes
- `G` - gigabytes

## Search by Time

### Modified time
```bash
find /path -mtime 0               # Modified in last 24 hours
find /path -mtime +7              # Modified more than 7 days ago
find /path -mtime -7              # Modified within last 7 days
find /path -mmin -60              # Modified within last 60 minutes
```

### Access time
```bash
find /path -atime -7              # Accessed within last 7 days
find /path -amin -60              # Accessed within last 60 minutes
```

### Change time (metadata)
```bash
find /path -ctime -7              # Changed within last 7 days
find /path -cmin -60              # Changed within last 60 minutes
```

### Newer than a specific file
```bash
find /path -newer reference.txt   # Modified more recently than reference.txt
```

## Search by Permissions

```bash
find /path -perm 644              # Exact permissions
find /path -perm -644             # At least these permissions
find /path -perm /644             # Any of these permissions
find /path -perm /u=w,g=w         # Writable by owner OR group
```

### Common permission searches
```bash
find /path -perm 777              # World-writable files
find /path -type f -perm 0777     # World-writable files only
find /path -type d -perm 0755     # Directories with 755
```

## Search by Owner

```bash
find /path -user username         # Owned by username
find /path -group groupname       # Owned by groupname
find /path -uid 1000              # Owned by UID 1000
find /path -gid 1000              # Owned by GID 1000
find /path -nouser                # No matching user
find /path -nogroup               # No matching group
```

## Search by Depth

```bash
find /path -maxdepth 2            # Search max 2 levels deep
find /path -mindepth 2            # Start searching from 2 levels deep
find /path -maxdepth 1 -type f    # Only files in current directory
```

## Combining Conditions

### AND (default)
```bash
find /path -name "*.txt" -size +1M    # .txt files larger than 1MB
```

### OR
```bash
find /path -name "*.txt" -o -name "*.pdf"    # .txt OR .pdf files
find /path \( -name "*.txt" -o -name "*.pdf" \) -size +1M
```

### NOT
```bash
find /path -not -name "*.txt"     # Everything except .txt files
find /path ! -name "*.txt"        # Same as above
```

## Actions on Results

### Print (default)
```bash
find /path -name "*.txt" -print   # Print full path (default)
find /path -name "*.txt" -print0  # Null-separated (for xargs -0)
```

### Execute command
```bash
find /path -name "*.txt" -exec cat {} \;              # Execute for each file
find /path -name "*.txt" -exec cat {} +               # Execute with multiple files
find /path -name "*.txt" -execdir cat {} \;           # Execute in file's directory
```

### Delete files
```bash
find /path -name "*.tmp" -delete                      # Delete matched files
find /path -name "*.tmp" -exec rm {} \;               # Alternative way
```

### Confirm before action
```bash
find /path -name "*.txt" -ok rm {} \;                 # Prompt before each deletion
```

### List details
```bash
find /path -name "*.txt" -ls                          # Detailed listing
```

## Practical Examples

### Find and remove empty files
```bash
find /path -type f -empty -delete
```

### Find and remove empty directories
```bash
find /path -type d -empty -delete
```

### Find files modified in last 24 hours
```bash
find /path -type f -mtime 0
```

### Find large files (over 100MB)
```bash
find /path -type f -size +100M
```

### Find and copy files
```bash
find /path -name "*.jpg" -exec cp {} /destination/ \;
```

### Find and count files
```bash
find /path -type f | wc -l
```

### Find duplicate files by name
```bash
find /path -type f -printf "%f\n" | sort | uniq -d
```

### Find files with specific extension and change permissions
```bash
find /path -name "*.sh" -exec chmod +x {} \;
```

### Find files excluding a directory
```bash
find /path -path /path/exclude -prune -o -name "*.txt" -print
```

### Find files with multiple extensions
```bash
find /path \( -name "*.jpg" -o -name "*.png" -o -name "*.gif" \)
```

### Find and compress old files
```bash
find /path -name "*.log" -mtime +30 -exec gzip {} \;
```

### Find recently modified files with details
```bash
find /path -type f -mtime -7 -ls
```

### Find files by content (using grep)
```bash
find /path -type f -name "*.txt" -exec grep -l "search_term" {} \;
```

### Find broken symbolic links
```bash
find /path -type l ! -exec test -e {} \; -print
```

### Find files with setuid bit
```bash
find /path -perm /4000
```

### Find world-writable files (security risk)
```bash
find /path -type f -perm -002
```

## Performance Tips

1. **Specify the starting path** - Don't search from `/` unless necessary
2. **Use `-maxdepth`** - Limit search depth when possible
3. **Put `-name` or `-type` first** - More efficient filtering
4. **Use `-print0` with `xargs -0`** - Handle filenames with spaces
5. **Use `-prune`** - Skip directories you don't need

## Common Options Summary

| Option | Description |
|--------|-------------|
| `-name` | Search by name (case-sensitive) |
| `-iname` | Search by name (case-insensitive) |
| `-type` | Search by type (f, d, l, etc.) |
| `-size` | Search by size |
| `-mtime` | Modified time in days |
| `-atime` | Access time in days |
| `-perm` | Search by permissions |
| `-user` | Search by owner |
| `-group` | Search by group |
| `-exec` | Execute command on results |
| `-delete` | Delete matched files |
| `-maxdepth` | Maximum search depth |
| `-mindepth` | Minimum search depth |
| `-empty` | Find empty files/directories |
| `-newer` | Newer than reference file |

## Escaping and Quoting

```bash
find /path -name "*.txt"          # Quotes prevent shell expansion
find /path -name \*.txt           # Backslash escapes the asterisk
find /path -name '*.txt'          # Single quotes also work
```

---

**Note:** Always test your `find` commands, especially with `-delete` or `-exec rm`, on a small subset first or use `-print` to verify results before executing destructive operations.