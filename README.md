
# ABOUT THE SCRIPT

As quickly as possible, restore a never-booted High Sierra OS image to an external machine
connected via Target Disk Mode.  Will automatically determine the optimal OS image filesystem
based on the detected storage media (SSD = APFS, HDD = HFS, Fusion Drive = HFS).

## Benefits

1. Restore an APFS OS image in ~3 minutes

2. Restore an HFS OS image in ~2 minutes

3. Errors result in outputting intelligent recommendations for resolution

## Requirements & Assumptions:

1. The machine running this script is running High Sierra or later.

2. The external machine to be restored already has the same version of High Sierra installed
as the OS restore image.
	- Apple's firmware updates come solely via the macOS installer app & macOS updates

3. You are using [AutoDMG](https://github.com/MagerValp/AutoDMG) to build never-boot OS images.

4. You intend to either restore an OS image of the same filesystem (HFS > HFS; APFS > APFS)
or move from HFS to APFS (SSDs only) based on the drive storage media.
	- In my limited testing trying to go APFS > HFS via ASR (10.13.4), repartitioning the APFS-formatted drive fails.
	- An interesting read about APFS on HDDs https://blog.macsales.com/43043-using-apfs-on-hdds-and-why-you-might-not-want-to

5. An APFS and/or HFS image lives on the local machine
	- Image restoring over the network has not been tested, nor is it intended. There are other tools better suited for that ([Restor](https://github.com/google/restor) & [Imagr](https://github.com/grahamgilbert/imagr))

6. There is only 1 external computer connected via Target Disk Mode to the machine running this script.  

## Additional (Optional) Capabilities

- Use `--force-hfs` to force an HFS image restore on an HFS-formatted SSD, rather than default to using an APFS image.

- Use `--no-imagescan` with `--dry-run` to skip the asr imagescan process
	- Unless you use [AutoDMG](https://github.com/MagerValp/AutoDMG) to build images from the command-line and use [`--skip-asr-imagescan`](https://github.com/MagerValp/AutoDMG/issues/144), you can safely use `--no-imagescan` with all your dry runs.

- Use `--keep-jamflog` to copy the jamf.log off a JAMF-managed machine prior to OS restore and copy it back afterward. Similar to what you may have been used to with Jamf Imaging.

- Use `--compname` to write a file with the desired computer hostname for collection & use later in your macOS deployment workflow.

- If you've restored an OS image via this script previously and used `--compname / -c` to write a file with your desired hostname, use `--reuse-compname` to refer to this file instead of entering the same or different computer hostname.

- Use `--timestamps` to write the OS image restore start and end date and time for collection & use later in your deployment workflow.
	- For example, I like to know how long it takes to go from base Mac to fully deployed and use this as part of the calculation.

- Use `--log-path` to specify an alternate path from the script's default LOGPATH.

## Usage

`./macOSImageRestore.sh --help`
- Show help message

`./macOSImageRestore.sh --version`
- Show version information

`./macOSImageRestore.sh --exitcodes`
- Show the exit code error list

### Examples

`sudo ./macOSImageRestore.sh [argument] --dry-run`
- Runs script printing all output given supplied arguments, but does not complete a restore or write any files to the external machine. Will instead perform an imagescan on the OS_IMAGE file to verify. Great for testing!

`sudo ./macOSImageRestore.sh [argument] --dry-run --no-imagescan`
- Same as above, but skips the asr imagescan.

`sudo ./macOSImageRestore.sh --compname <compname>`
- Following OS restore, writes file (COMPNAME_FILE) with the computer name

`sudo ./macOSImageRestore.sh --reuse-compname`
- Checks for pre-existing COMPNAME_FILE on external machine, and if found will collect it and write it back following successful OS restore.

`sudo ./macOSImageRestore.sh --keep-jamflog`
- Prior to OS restore, copies the /var/log/jamf.log file off the exernal machine and copies it back following successful OS restore.

`sudo ./macOSImageRestore.sh --log-path /path/to/log/folder`
- Writes log output to the specified path.

`sudo ./macOSImageRestore.sh --timestamps`
- Following OS restore, will write the collected restore start and end timestamps to a PLIST on the external machine.
