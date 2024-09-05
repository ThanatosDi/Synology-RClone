# Hey! This repo is no longer supported
Synology-RClone has been deprecated in favor of: https://github.com/ThanatosDi/rclone-serve



# Synology-RClone
Backup Synology NAS files with RClone

This template is not limited to running on Synology NAS; it can be used on any NAS with Docker.

# Configure Upload Tasks
Each service represents a separate upload task. The method to schedule these tasks to run periodically is not covered here; please research this on your own.

The image used is the official RClone image.

For detailed RClone configuration, please refer to [RClone Configure](https://rclone.org/docs/#configure).

```yaml
services:
  template:
    image: rclone/rclone:${RCLONE_VERSION:-latest}
    environment:
      - PHP_TZ=${TZ:-Etc/GMT}
      - TZ=${TZ:-Etc/UTC}
      - PUID=${PUID:-1000}
      - PGID=${PGID:-1000}
      # Specify rclone's config directory
      - RCLONE_CONFIG_DIR=/rclone/config
      # Specify rclone's config file
      - RCLONE_CONFIG=/rclone/config/rclone.conf
      # Specify rclone's log file
      - RCLONE_LOG_FILE=/rclone/logs/template.log
      # Specify rclone's temp directory
      - RCLONE_TEMP_DIR=/rclone/tmp
      # Show progress
      - RCLONE_PROGRESS=true
      # Prevent directory tree traversal, suitable for large target directories
      - RCLONE_NO_TRAVERSE=true
      # Delete files in the target directory that are not in the source directory
      - RCLONE_DELETE_EXCLUDED=true
      # Specify file names to exclude
      - RCLONE_EXCLUDE_FROM=/rclone/.rcloneignore
      # Check 8 files simultaneously
      - RCLONE_TRANSFERS=8
      # Check 8 files simultaneously
      - RCLONE_CHECKERS=8
      # Enable multi-threaded streaming, 8 threads per file
      - RCLONE_MULTI_THREAD_STREAMS=8
      # Output verbosity, 1 for -v, 2 for -vv
      - RCLONE_VERBOSE=1
      # Only copy files modified in the last 24 hours
      - RCLONE_MAX_AGE=24h
    working_dir: /rclone
    volumes:
      # Mount the config file to the container, read-only
      - ./config/nas.conf:/rclone/config/rclone.conf:ro
      # Mount the ignore file to the container
      - ./.rcloneignore:/rclone/.rcloneignore
      # Mount the log directory to the container, read-write
      - ./logs:/rclone/logs:rw
      # Mount the temp directory to the container, read-write
      - ./tmp:/rclone/tmp:rw
      # Mount the directory to be uploaded to the container, read-only
      - /foo/bar:/rclone/backup/bar:ro
    command: copy /rclone/backup/bar {remote}:/remote/path
    deploy:
      resources:
        limits:
          memory: 1024M

```
