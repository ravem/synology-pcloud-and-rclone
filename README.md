# How to install rclone on a Synology NAS and use it with pcloud (tested on DS2016j, on DSM 7 and 2FA enabled).

First of all you must enable SSH access on your Synology NAS:
Log in into the Synology NAS, then, navigate DSM Control Panel > Terminal & SNMP > Terminal.
Finally, tick Enable SSH service.

Once connected to you NAS via SSH you must obtain root access:
```console
admin@mynas:/$ sudo -i
Password:
root@mynas:~# 
```
From there you will need to download the Rclone install script:
```console
wget https://rclone.org/install.sh
```
and set permissions:
```console
chmod +x install.sh
```
Finally, you must install Rclone:
```console
./install.sh
```
You won't be able to install man pages, and you'll probably get an error about it, but don't care, it works fine anyway. 

Do not close ther connection.

The initial setup involves getting a token from pCloud. 

Since you're on a terminal and you don't have access to a browser this can be done in two separate steps.

On a pc open a WSL terminal, (assuming ubuntu) or you favourite linux shell and install rclone if not available:
```console
curl https://rclone.org/install.sh | sudo bash
```
Do not install with package manager, the version in the repository can be old.

Now that we have all the pieces we must start putting everything together an it's time to create a remote, that is the configuration to access the remote storage.

For us the remote storage will be pcloud, and we will name the remote pcloud, boring, I know, sometimes my wife thinks the same..

The following text will help you through the interactive setup process but before you start, open a broser and log in pcloud, it will be useful when, at the end of the configuration you will need to authorize the access (you will be asked to open a link like this http://127.0.0.1:53682/auth?state=A_LONG_AUTHORIZATION_STRING ).

Let's go.

In your terminal/bash/wsl launch:

```
rclone config

No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n 
name> pcloud
Option Storage.
Type of storage to configure.
Choose a number from below, or type in your own value.
 1 / 1Fichier
   \ (fichier)
 2 / Akamai NetStorage
   \ (netstorage)
 3 / Alias for an existing remote
   \ (alias)
 4 / Amazon Drive
   \ (amazon cloud drive)
 5 / Amazon S3 Compliant Storage Providers including AWS, Alibaba, Ceph, Digital Ocean, Dreamhost, IBM COS, Lyve Cloud, Minio, RackCorp, SeaweedFS, and Tencent COS
   \ (s3)
 6 / Backblaze B2
   \ (b2)
 7 / Better checksums for other remotes
   \ (hasher)
 8 / Box
   \ (box)
 9 / Cache a remote
   \ (cache)
10 / Citrix Sharefile
   \ (sharefile)
11 / Compress a remote
   \ (compress)
12 / Dropbox
   \ (dropbox)
13 / Encrypt/Decrypt a remote
   \ (crypt)
14 / Enterprise File Fabric
   \ (filefabric)
15 / FTP Connection
   \ (ftp)
16 / Google Cloud Storage (this is not Google Drive)
   \ (google cloud storage)
17 / Google Drive
   \ (drive)
18 / Google Photos
   \ (google photos)
19 / Hadoop distributed file system
   \ (hdfs)
20 / Hubic
   \ (hubic)
21 / In memory object storage system.
   \ (memory)
22 / Jottacloud
   \ (jottacloud)
23 / Koofr, Digi Storage and other Koofr-compatible storage providers
   \ (koofr)
24 / Local Disk
   \ (local)
25 / Mail.ru Cloud
   \ (mailru)
26 / Mega
   \ (mega)
27 / Microsoft Azure Blob Storage
   \ (azureblob)
28 / Microsoft OneDrive
   \ (onedrive)
29 / OpenDrive
   \ (opendrive)
30 / OpenStack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ (swift)
31 / Pcloud
   \ (pcloud)
32 / Put.io
   \ (putio)
33 / QingCloud Object Storage
   \ (qingstor)
34 / SSH/SFTP Connection
   \ (sftp)
35 / Sia Decentralized Cloud
   \ (sia)
36 / Storj Decentralized Cloud Storage
   \ (storj)
37 / Sugarsync
   \ (sugarsync)
38 / Transparently chunk/split large files
   \ (chunker)
39 / Union merges the contents of several upstream fs
   \ (union)
40 / Uptobox
   \ (uptobox)
41 / Webdav
   \ (webdav)
42 / Yandex Disk
   \ (yandex)
43 / Zoho
   \ (zoho)
44 / http Connection
   \ (http)
45 / premiumize.me
   \ (premiumizeme)
46 / seafile
   \ (seafile)
Storage> 31
Option client_id.
OAuth Client Id.
Leave blank normally.
Enter a value. Press Enter to leave empty.
client_id>
Option client_secret.
OAuth Client Secret.
Leave blank normally.
Enter a value. Press Enter to leave empty.
client_secret>
Edit advanced config?
y) Yes
n) No (default)
y/n> n
Use auto config?
 \* Say Y if not sure
 \* Say N if you are working on a remote or headless machine
y) Yes (default)
n) No
y/n> y
2022/03/25 16:14:46 NOTICE: If your browser doesn't open automatically go to the following link: http://127.0.0.1:53682/auth?state=A_LONG_AUTHORIZATION_STRING
2022/03/25 16:14:46 NOTICE: Log in and authorize rclone for access
2022/03/25 16:14:46 NOTICE: Waiting for code...
2022/03/25 16:14:54 NOTICE: Got code
--------------------
[pcloud]
type = pcloud
hostname = eapi.pcloud.com <--- will be api.pcloud.com if you're in the USA
token = {"access_token":"YOUR_ACCESS_TOKEN_WILL_BE_HERE","token_type":"bearer","expiry":"0001-01-01T00:00:00Z"}
--------------------
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y

Current remotes:

Name                 Type
====                 ====
pcloud               pcloud

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q
```

So, if you have followed until now, you have created the configuration to access pcloud from your computer, that is, the part between the two `-------------------` up there,  and you just need to move it in the Synology NAS.

If you're on some linux probably the config has been created in 
`.config/rclone/rclone.conf`.

If you're on WSL it should be here:
`C:\Users\USERNAME\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu20.04onWindows_79rhkp1fndgsc\LocalState\rootfs\home\USERNAME\.config\rclone\rclone.conf`.

Copy the file content (or copy it from the terminal windows you have used for rclone config, omitting the dashes).

Go back to the SSH connection to the NAS you have used to install rclone, here you must edit the NAS configuration file. 

The editor installed in the terminal is VI *_tense creepy background music_*...
VI it's a powerful editor, but it's like cilantro, love it or hate it.
Since it's what we have, let's try to survive.

Launch this command:
```console
vi .config/rclone/rclone.conf
```
When you first open a file with VI, you are in Command mode. 
Command mode means that you can use keyboard keys to navigate, delete, copy, paste, and do a number of other tasks EXCEPT entering text, for thet you will need Insert mode.
To enter Insert mode, press `i`,  do it, you will see `-- INSERT --` in the lower left corner of the terminal.
Now you can enter text, so paste the config you have in your clipboard (right click?).

Exit the Insert mode pressing `Esc`.

Now, if everything has been done correctly and the Almighty is on your side, you must save the file and copy it in location you like, eg `/var` (but not in the deestination folder or it will be overwritten).

You can do it entering  `:wq`.
If something doesn't look good `:q!`will close VI without saving.

Cool. 

Rclone is configured, now try the following command.
```console
rclone lsd pcloud:
```
You shoud see all the folders created in you pcloud storage root.
Or, if you really like to watch scrolling text on a terminal:
```console
rclone ls pcloud:
```
This will list all the files you have in pcloud.

If this works che configuration is done explore more commands here: https://rclone.org/commands/

Now you may want to setup you backup/sync/copy from/to pcloud, I will give only two quick examples, since there is plenty of documentation on this.
Let's say you want to mirror some or all your cloud data in your NAS: your command is `rclone sync`, let's learn someting more about it.
This command makes source and dest identical, modifying destination only.
`From the command synopsis:

Sync the source to the destination, changing the destination only. Doesn't transfer files that are identical on source and destination, testing by size and modification time or MD5SUM. Destination is updated to match source, including deleting files if necessary (except duplicate objects, see below).

Important: Since this can cause data loss, test first with the --dry-run or the --interactive/-i flag.

rclone sync -i SOURCE remote:DESTINATION
Note that files in the destination won't be deleted if there were any errors at any point. Duplicate objects (files with the same name, on those providers that support it) are also not yet handled.`

First case:
you want to mirror ALL the content of the folder DATA that's in your pcloud storage IN your NAS.

Find in your NAS the location of the destination folder, it should be in /PATH/TO/NAME_OF_YOUR_VOLUME.
Take a look under Storage manager or give a simple: 
```console
ls / 
```
and check the volume name.
Let's say it's `/volume1/share/DATA`, to sync you you shall launch:
```console
rclone --config="/var/rclone.conf" sync pcloud:DATA/ /volume1/share/DATA 
```
Second case:
You want to sync ALL pcloud content IN your storage: the command is:
```console
rclone sync --config="/var/rclone.conf" pcloud:/ /volume1/share/CloudSync/pcloud/
```
If you want to sync the folders you have the command rclone sync, again look here https://rclone.org/commands/ for the documentation.

Ok last step, set up a scheduled job.
Log in to the Synology NAS.
Navigate to DSM Control Panel > Task Scheduler.
Next click on the create button and then click on scheduled task > user-defined script.
Then, in the create task window, enter your task name in the general settings tab.
Next, in the schedule tab, choose the schedule.
Finally, on the task setting tab, paste your user-defined script.

Click `Ok`.

Done.
