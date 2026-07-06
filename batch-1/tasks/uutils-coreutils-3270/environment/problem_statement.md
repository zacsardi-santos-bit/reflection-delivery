## Description

The disk usage reporting utility is displaying incorrect values in the "available" space column. It is currently showing the total number of free disk blocks, which includes space reserved for the system administrator, rather than the number of disk blocks actually available to ordinary (unprivileged) users.

On many Linux filesystems, a portion of disk space is reserved for root, so the total "free" count is larger than the "available" count that normal users can actually use. The standard disk usage tool that ships with most Linux distributions has always reported the user-available figure in its "Avail" column. Our implementation was instead reporting the total free blocks, which can lead users to believe they have more usable space than they actually do.

## Expected Behavior

- The "Avail" column should display the number of bytes/blocks available to unprivileged users, not the total free bytes/blocks (which includes space reserved for the system administrator).
- When a filesystem has fewer user-available blocks than total free blocks (due to reserved space), the reported available value should reflect what users can actually write.

## Why This Matters

This discrepancy can be confusing or misleading. If a user sees 750 available but can only write 600 worth of data before getting a "disk full" error, that is a bug in the reporting, not in the filesystem. Fixing this brings the output in line with what users expect and what comparable tools report.
