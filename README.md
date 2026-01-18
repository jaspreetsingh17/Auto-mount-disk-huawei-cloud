# Auto Mount Disk for Huawei Cloud

```bash
curl -sSL https://raw.githubusercontent.com/jaspreetsingh17/Auto-mount-disk-huawei-cloud/refs/heads/main/install.sh | sudo bash
```

This script automates the process of mounting an additional disk on Huawei Cloud instances. It installs necessary packages, formats the disk, and configures it for permanent mounting at `/data`.

## Prerequisites

- Ubuntu/Debian-based system on Huawei Cloud
- An additional unformatted disk attached to your instance
- Root/sudo access

## What the Script Does

1. **Installs essential packages:**
   - `logrotate`
   - `jq`
   - `postfix`
   - `curl` and `unzip`
   - `aria2c`

2. **Disk Setup:**
   - Creates `/data` directory
   - Automatically detects the largest unmounted partition
   - Formats the disk with XFS filesystem
   - Configures permanent mount in `/etc/fstab`
   - Mounts the disk and sets proper ownership

## Usage

### Quick Install

```bash
git clone https://github.com/jaspreetsingh17/Auto-mount-disk-huawei-cloud.git
cd Auto-mount-disk-huawei-cloud
chmod +x install.sh
sudo ./install.sh
```

### Manual Steps

If you prefer to run commands manually:

```bash
# Update system packages
sudo apt-get update -y && sudo apt-get upgrade -y

# Install required packages
sudo apt-get install -y logrotate jq postfix curl unzip aria2c

# Create the data directory
sudo mkdir /data

# Find the largest unmounted disk
disk2=$(lsblk -nx size -o kname | tail -1 | awk '{printf "/dev/"$1}')

# Format the disk with XFS
sudo mkfs -t xfs $disk2

# Backup fstab
sudo cp /etc/fstab /etc/fstab-old

# Get the disk UUID
VUUID=$(sudo blkid -o value -s UUID $disk2)

# Add permanent mount entry
sudo su -c "echo 'UUID=$VUUID  /data   xfs   defaults        0       0' >> /etc/fstab"

# Mount the disk
sudo mount -a

# Set ownership to current user
sudo chown -R $USER:$USER /data
```

## Verification

After running the script, verify the disk is mounted:

```bash
# Check if /data is mounted
df -h /data

# Verify the fstab entry
cat /etc/fstab | grep /data

# Check disk ownership
ls -la /data
```

## ⚠️ Warning

- This script will **format** the detected disk, erasing all existing data
- Always ensure you have backups before running
- The script automatically selects the largest unmounted disk - verify this is correct for your setup