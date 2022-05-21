SAVED FOR FUTURE REFERENCE
https://apple.stackexchange.com/a/359056/457053



The Disk Utility application probably can not be use to fix your Mac. Below is a alternate procedure.

    Start up the Mac from macOS Recovery over the Internet by restarting and immediately pressing and holding the the key combination Option-⌘-R.
    From the menu bar, select to open a Terminal application window.

    Enter the following command to get the GUID Partition Table (GPT) entries.

    $ gpt -r show disk0

    An example output is shown below.

    $ gpt -r show disk0
          start       size  index  contents
              0          1         PMBR
              1          1         Pri GPT header
              2         32         Pri GPT table
             34          6         
             40     409600      1  GPT part - C12A7328-F81F-11D2-BA4B-00A0C93EC93B
         409640   97656248      2  GPT part - FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF
       98065888        544         
       98066432   97245184      3  GPT part - 7C3457EF-0000-11AA-AA11-00306543ECAC
      195311616        891         
      195312507         32         Sec GPT table
      195312539          1         Sec GPT header

    Enter the command given below to change the GPT.

    gpt -f remove -i 3 disk0
    gpt -f remove -i 2 disk0
    gpt -f add -i 2 -b  <start> -s <size> -t apfs disk0

    Where <start> and <size> are integers taken from the row with index equal to 2 in the table shown in step 2. An example is shown below.

    $ gpt -f remove -i 3 disk0
    disk0s3 removed
    $ gpt -f remove -i 2 disk0
    disk0s2 removed
    $ gpt -f add -i 2 -b  409640 -s 97656248 -t apfs disk0  
    disk0s2 added

    You will need to replace 409640 and 97656248 with the values shown in your GPT table.

    Restart the Mac back to macOS Mojave.

    Enter the command given below in a Terminal application window to reclaim the free space.

    $ sudo diskutil apfs resizeContainer disk0s2 0

