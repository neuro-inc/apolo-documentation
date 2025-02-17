# Disks

As opposed to standard platform storage, disks provide a faster, short-lived, non-partitioned space for jobs. This makes disks perfect for manipulating large amounts of data.

For example, if there's a large dataset that you need to process, it's better to create a disk, upload all of the required data from the storage to this disk, and only then perform all the necessary operations. In some cases, this can save you about 10%-20% of time depending on the amount of data and the operations you perform with it. After the data is processed, you can download it back to the storage for further use.

The Disks section is accessible via the left-hand navigation menu in the Apolo Console. It allows users to:

* Create new disks.
* View details of existing disks.
* Manage and delete disks.

## Creating a New Disk

1. Click "Create New Disk" button.
2. Fill out disk details:
   * _Storage:_ Specify the storage size in GB. This determines the disk's capacity.
   * _Name:_ Provide a unique name to identify the disk within the cluster.
   * _Lifespan:_ Define the disk’s lifespan using a duration format (e.g., 1d for one day, 2h for two hours, etc.). Lifespan determines how long the disk is allowed to exist before it is automatically removed.
3. Click "Create" button.

## Viewing Disk Details

1. Select a disk from the grid to display its details in the right-hand panel.
2. Review the following information:
   * _ID:_ The unique identifier for the disk.
   * _Status:_ Indicates whether the disk is ready for use or still being processed.
   * _Owner:_ Displays the author of the disk.
   * _Storage:_ Total storage capacity of the disk.
   * _Lifespan:_ Defined time before the disk expires.
   * _Created:_ The date the disk was created.

Additionally, the properties pop-up includes two buttons: one for sharing access to the selected disk and another for removing the disk.
