**Disk image**

A disk image is a huge dump of many numbers. it has 4 main layers 
	* Media 
		* Media is the lowest level, providing key information to access the deeper layers, but not shedding much light on the data contained in the image. the media layer tools all are prepended with 'mm'
	*  block
		* the block layer is the second lowest level of the four layers considered here. Block layer tools are prepended with 'blk' in the Sleuthkit. `blkcat` is a block layer tool that outputs the contents of a single block. The block layer is the numbers of the disk image broken into equal-sized chunks. A single file is likely to contain multiple blocks.
	* inode
		* the inode layer is the bookkeeping layer of a disk image. It’s like the table of contents, with the chapter numbers being like the inodes, and the pages like the blocks of a file. Inode layer tools are prepended with 'i'. `icat` is an inode layer tool that outputs a single file based on its inode number. we can read a file by icat 
		  r/r means file d /d means = directoty. A leading `*` in `fls` means **deleted**; `icat` can still often extract it by inode.
	* filename
		* Filename layer tools are prepended by 'f'. `fls` lists the files on an image starting at the root. This is what we will use for our exploration of the disk image.