Filesystem ext4

ext4 (fourth extended filesystem o «cuarto sistema de archivos extendido») es un sistema de archivos transaccional 
(en inglés journaling), anunciado el 10 de octubre de 2006 por Andrew Morton, como una mejora compatible de ext3. 
El 25 de diciembre de 2008 se publicó el kernel Linux 2.6.28, que elimina ya la etiqueta de "experimental" de código de ext4.

Las principales mejoras son:

Soporte de volúmenes de hasta 1024 PiB.
Soporte añadido de extent.
Menor uso del CPU.
Mejoras en la velocidad de lectura y escritura.

Desarolladores: Mingming Cao, Dave Kleikamp, Alex Tomas, Andrew Morton, y otros

En ext4 se introducen los exents, que se utilizan para reemplazar al tradicional esquema de bloques utilizado por 
ext2 y ext3. Los exents mejoran el rendimiento al trabajar con ficheros de gran tamaño.

Sistema de archivos de gran tamaño:
El sistema de archivos ext4 es capaz de trabajar con volúmenes de gran tamaño, hasta 1 exbibyte1 y ficheros de 
tamaño de hasta 16 TiB.

Límite de 32000 subdirectorios superado:
En ext3 el nivel de profundidad en subdirectorios permitido estaba limitado a 32000. 
Este límite ha sido aumentado a 64000 en ext4, permitiendo incluso ir más allá de este límite (haciendo uso de "dir_nlink"). 
Para permitir un rendimiento continuo, dada la posibilidad de directorios mucho más grandes, htree está activado por defecto 
en ext4. Esta función está implementada desde la versión 2.6.23. htree está también disponible en ext3 cuando la función dir_index 
está activada.
Fuente: https://geekytheory.com/sistemas-de-archivos-fat32-ntfs-ext3-y-ext4/

EXT4
The EXT4 filesystem primarily improves performance, reliability, and capacity. To improve reliability, metadata and journal checksums were added. To meet various mission-critical requirements, the filesystem timestamps were improved with the addition of intervals down to nanoseconds. The addition of two high-order bits in the timestamp field defers the Year 2038 problem until 2446—for EXT4 filesystems, at least.

In EXT4, data allocation was changed from fixed blocks to extents. An extent is described by its starting and ending place on the hard drive. This makes it possible to describe very long, physically contiguous files in a single inode pointer entry, which can significantly reduce the number of pointers required to describe the location of all the data in larger files. Other allocation strategies have been implemented in EXT4 to further reduce fragmentation.

EXT4 reduces fragmentation by scattering newly created files across the disk so that they are not bunched up in one location at the beginning of the disk, as many early PC filesystems did. The file-allocation algorithms attempt to spread the files as evenly as possible among the cylinder groups and, when fragmentation is necessary, to keep the discontinuous file extents as close as possible to others in the same file to minimize head seek and rotational latency as much as possible. Additional strategies are used to pre-allocate extra disk space when a new file is created or when an existing file is extended. This helps to ensure that extending the file will not automatically result in its becoming fragmented. New files are never allocated immediately after existing files, which also prevents fragmentation of the existing files.

Aside from the actual location of the data on the disk, EXT4 uses functional strategies, such as delayed allocation, to allow the filesystem to collect all the data being written to the disk before allocating space to it. This can improve the likelihood that the data space will be contiguous.

Older EXT filesystems, such as EXT2 and EXT3, can be mounted as EXT4 to make some minor performance gains. Unfortunately, this requires turning off some of the important new features of EXT4, so I recommend against this.

EXT4 has been the default filesystem for Fedora since Fedora 14. An EXT3 filesystem can be upgraded to EXT4 using the procedure described in the Fedora documentation, however its performance will still suffer due to residual EXT3 metadata structures. The best method for upgrading to EXT4 from EXT3 is to back up all the data on the target filesystem partition, use the mkfs command to write an empty EXT4 filesystem to the partition, and then restore all the data from the backup.

Inode

The inode, described previously, is a key component of the metadata in EXT filesystems. Figure 2 shows the relationship between the inode and the data stored on the hard drive. This diagram is the directory and inode for a single file which, in this case, may be highly fragmented. The EXT filesystems work actively to reduce fragmentation, so it is very unlikely you will ever see a file with this many indirect data blocks or extents. In fact, as you will see below, fragmentation is extremely low in EXT filesystems, so most inodes will use only one or two direct data pointers and none of the indirect pointers.

inodesanddataallocation-01_0.png

Figure 2: The inode stores information about each file and enables the EXT filesystem to locate all data belonging to it.

The inode does not contain the name of the file. Access to a file is via the directory entry, which itself is the name of the file and contains a pointer to the inode. The value of that pointer is the inode number. Each inode in a filesystem has a unique ID number, but inodes in other filesystems on the same computer (and even the same hard drive) can have the same inode number. This has implications for links, and this discussion is beyond the scope of this article.

The inode contains the metadata about the file, including its type and permissions as well as its size. The inode also contains space for 15 pointers that describe the location and length of data blocks or extents in the data portion of the cylinder group. Twelve of the pointers provide direct access to the data extents and should be sufficient to handle most files. However, for files that have significant fragmentation, it becomes necessary to have some additional capabilities in the form of indirect nodes. Technically these are not really inodes, so I use the term "node" here for convenience.

An indirect node is a normal data block in the filesystem that is used only for describing data and not for storage of metadata, thus more than 15 entries can be supported. For example, a block size of 4K can support 512 4-byte indirect nodes, allowing 12 (direct) + 512 (indirect) = 524 extents for a single file. Double and triple indirect node support is also supported, but most of us are unlikely to encounter files requiring that many extents.

Data fragmentation

For many older PC filesystems, such as FAT (and all its variants) and NTFS, fragmentation has been a significant problem resulting in degraded disk performance. Defragmentation became an industry in itself with different brands of defragmentation software that ranged from very effective to only marginally so.

Linux's extended filesystems use data-allocation strategies that help to minimize fragmentation of files on the hard drive and reduce the effects of fragmentation when it does occur. You can use the fsck command on EXT filesystems to check the total filesystem fragmentation. The following example checks the home directory of my main workstation, which was only 1.5% fragmented. Be sure to use the -n parameter, because it prevents fsck from taking any action on the scanned filesystem.

fsck -fn /dev/mapper/vg_01-home
I once performed some theoretical calculations to determine whether disk defragmentation might result in any noticeable performance improvement. While I did make some assumptions, the disk performance data I used were from a new 300GB, Western Digital hard drive with a 2.0ms track-to-track seek time. The number of files in this example was the actual number that existed in the filesystem on the day I did the calculation. I did assume that a fairly large amount of the fragmented files (20%) would be touched each day.

Total files	271,794
% fragmentation	5.00%
Discontinuities	13,590
 	 
% fragmented files touched per day	20% (assume)
Number of additional seeks	2,718
Average seek time	10.90 ms
Total additional seek time per day	29.63 sec
 	0.49 min
 	 
Track-to-track seek time	2.00 ms
Total additional seek time per day	5.44 sec
 	0.091 min
Table 1: The theoretical effects of fragmentation on disk performance

I have done two calculations for the total additional seek time per day, one based on the track-to-track seek time, which is the more likely scenario for most files due to the EXT file allocation strategies, and one for the average seek time, which I assumed would make a fair worst-case scenario.

As you can see from Table 1, the impact of fragmentation on a modern EXT filesystem with a hard drive of even modest performance would be minimal and negligible for the vast majority of applications. You can plug the numbers from your environment into your own similar spreadsheet to see what you might expect in the way of performance impact. This type of calculation most likely will not represent actual performance, but it can provide a bit of insight into fragmentation and its theoretical impact on a system.

Most of my partitions are around 1.5% or 1.6% fragmented; I do have one that is 3.3% fragmented but that is a large, 128GB filesystem with fewer than 100 very large ISO image files; I've had to expand the partition several times over the years as it got too full.

That is not to say that some application environments don't require greater assurance of even less fragmentation. The EXT filesystem can be tuned with care by a knowledgeable admin who can adjust the parameters to compensate for specific workload types. This can be done when the filesystem is created or later using the tune2fs command. The results of each tuning change should be tested, meticulously recorded, and analyzed to ensure optimum performance for the target environment. In the worst case, where performance cannot be improved to desired levels, other filesystem types are available that may be more suitable for a particular workload. And remember that it is common to mix filesystem types on a single host system to match the load placed on each filesystem.

Due to the low amount of fragmentation on most EXT filesystems, it is not necessary to defragment. In any event, there is no safe defragmentation tool for EXT filesystems. There are a few tools that allow you to check the fragmentation of an individual file or the fragmentation of the remaining free space in a filesystem. There is one tool, e4defrag, which will defragment a file, directory, or filesystem as much as the remaining free space will allow. As its name implies, it only works on files in an EXT4 filesystem, and it does have some limitations.

If it becomes necessary to perform a complete defragmentation on an EXT filesystem, there is only one method that will work reliably. You must move all the files from the filesystem to be defragmented, ensuring that they are deleted after being safely copied to another location. If possible, you could then increase the size of the filesystem to help reduce future fragmentation. Then copy the files back onto the target filesystem. Even this does not guarantee that all the files will be completely defragmented.
Source: https://opensource.com/article/17/5/introduction-ext4-filesystem?sc_cid=70160000000xYJ9AAM
