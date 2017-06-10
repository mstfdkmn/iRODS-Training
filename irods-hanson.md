# iRODS training Handon (1.5 hours)


Contents:

- Connect to iRODS (15 minutes)
- Explore iCommands (15 mins)
- Data up and download (15 minutes)
- Structuring data and Access control (15 minutes)
- Metadata (30 minutes)

Pre-requistics:

- Terminal or SSH client (On Windows machines install MobaXterm or PuTTY)
-  username and password to login to a UNIX system (Lisa).
-  username and password to login to iRODs server

## Connect to iRODS (15 minutes)
Goal: We will see how to connect to an iRODS instance and will have a look at the environment.

Login to Lisa with your **sdemo\<XXX>** accounts. (replace login 'narges' with your own sdemo login) 

```
ssh narges@lisa.surfsara.nl
```

Then load the icommands module on Lisa. This module provides the commandline tools with which you can connect to iRODS.


```
module load icommands
```

### Connection
To connect to an iRODS server from Lisa, type in

```
iinit
```
This initialize a session, so you don't need to retype your password evry time.
The system will ask you for information where to connect to:

```
Enter the host name (DNS) of the server to connect to:  <ip adrdress>
Enter the port number: 1247
Enter your irods user name: <irodsuser>
Enter your irods zone: <zonename>
```

Provide the required information as below: 

- For **host name (DNS) of the server** use: **145.100.58.46**
- For **port number** use: **1247**
- For **irods user name** use: **di4r-user\<X>**
- For **irods zone** use: **eveZone**


Provide your password and .. there you are logged in.

If you are logging-in for the second time, you only need to provide your password to connect to the iRODS zone. The information about the server, port number,... will be saved in a hidden file (.irods) in your home directory on Lisa.

### Environment

With the command
```
ienv
```
you can check how the iRODS zone is composed


```
ienv

NOTICE: Release Version = rods4.1.10, API Version = d
NOTICE: irods_session_environment_file - 
	/home/narges/.irods/irods_environment.json.10301
NOTICE: irods_user_name - di4r-user1
NOTICE: irods_host - 145.100.58.46
...
NOTICE: irods_port - 1247
...
NOTICE: irods_zone_name - eveZone
NOTICE: irods_client_server_policy is not defined
NOTICE: irods_client_server_negotiation is not defined
...
NOTICE: created irodsHome=/eveZone/home/di4r-user1
NOTICE: created irodsCwd=/eveZone/home/di4r-user1
```

The ".irods/irods_environment.json" stores the data that you just provided to login. Next time you login iRODS will check this file, so you do not have to provide these details again.

Have a look at the iRODS session environment:

```
cat /home/narges/.irods/irods_environment.json
```
The file contains the minimal information for a session.


Command `iuserinfo` shows information about your iRODS user account.

```
name: di4r-user1
id: 10429
type: rodsuser
zone: eveZone
info: 
comment: 
create time: 01492436109: 2017-04-17.15:35:09
modify time: 01492436109: 2017-04-17.15:35:09
member of group: public
member of group: training
member of group: di4r-user1
```

### HELP

- List all commands available for iRODS
```
ihelp
```
- Get help on a specific commands
`ihelp iuserinfo` or `iuserinfo -h`




## Explore iCommands (15 mins)

iRODS command line is equivalent to standard Unix operations

- ils
- icd
- ipwd
- iput
- iget
- imkdir
- ihelp
- irepl

You can see the list of full iRODS commands by typing:

```
ihelp
```

### The working directory

With the command `ils` we can check whether there is data in our iRODS-home directory.

```
ils
/eveZone/home/di4r-user1:
``` 
- eveZone: the name of the iRODS zone
- `/home/<user>`: your default working directory 


Lets create a file on your Lisa machine and quickly store in on the iRODS server: 

```
touch test1
iput test1
``` 

With the `ils` command we can see the file on iRODS (logical file names)

```
ils
/eveZone/home/di4r-user1:
  test1
```
Command `ils -l` shows more detail, including the logical resource name

```
ils -l
/eveZone/home/di4r-user1:
  di4r-user1        0 demoResc            0 2017-06-09.16:46 & test1
```

Command `ils -L` shows even more detail, including the physical path where the file was stored

```
ils -L
/eveZone/home/di4r-user1:
  di4r-user1        0 demoResc            0 2017-06-09.16:46 & test1
        generic    /var/lib/irods/iRODS/Vault/home/di4r-user1/test1
```
What does the output of `ils -L` mean? 

The file test that we uploaded is known in iRODS as `/eveZone/home/di4r-user1/test1`. 
 
- The first item is the name of the owner of the file (in this case, “di4r-user1”).
- The second item is the replication number, which is the number of replicas of the file in different physical locations. (In this case, '0') 
- The third item is the Logical Resource Name (In this case, 'demoResc'). 
- The fourth item is the size of the file in bytes (in this case, '0'). 
- The fifth item is the date. 
- The sixth item (“&”) indicates the file is up-to-date. If a replica is modified, the “&” flag is removed from the out-of-date copies.
- When iRODS stored a copy of the file onto the storage resource “demoResc”, the copy was made at the physical location: `/var/lib/irods/iRODS/Vault/home/di4r-user1/test1`

Make a new subcollection (or directory) on iRODS:

```
imkdir t1
``` 

Make t1 the current default working directory

```
icd t1
``` 

Now, lets make another file and store it on iRODS:

```
touch test2
iput test2
``` 
Now. let make an `ils -L` to see where the file is stores on iRODS:

```
 ils -L
/eveZone/home/di4r-user1/t1:
  di4r-user1        0 demoResc            0 2017-06-02.10:56 & test2
        generic    /var/lib/irods/iRODS/Vault/home/di4r-user1/t1/test2
```
The file you upload to iRODS will be put in your working directory.
Lets check our current working directory:

```
ipwd
/eveZone/home/di4r-user1/t1
```

Lets move back to the home directory

```
icd ..

```

Data can be deleted with the command `irm <filename>`

```
irm test1
```
To delete an irods collection (directory):

```
irm -r t1
```

**Note:** the commands to steer iRODS are very similar to bash commands and can easily be confused! 

## Data up and download (15 minutes)

### Create data

Open a file with `nano <filename>` on the linux filesystem

```
nano test.txt
```

### Data upload

With the linux command `ls` you can check that the file has been created and is accessible on the User Interface machine (Lisa):

```
ls 
test.txt
```

We now upload the data to the iRODS server and remove the original file (-K option triggers checksum calculation):

```
iput -K test.txt
rm test.txt
```

The file is now only available on the iRODS server:

```
ils
/eveZone/home/di4r-user1:
  test.txt
```
 
But not on the local linux system (check with `ls`).



### Connection between logical and physical namespace

iRODS provides an abstraction from the physical location of the files. I.e. `/eveZone/home/di4r-user1/test.txt` is the logical path which only iRODS knows. But where is the file actually on the server that hosts iRODS?
test

```
ils -L
/eveZone/home/di4r-user1:
  di4r-user1        0 demoResc           27 2017-06-09.16:53 & test.txt
    4f70b054700b222f04674c65d8f749ec    generic    /var/lib/irods/iRODS/Vault/home/di4r-user1/test.txt
```

What does this mean?
The file `test.txt` that we uploaded is known in iRODS as `/eveZone/home/di4r-user1/test.txt`. It is owned by the user `di4r-user1` and lies on the storage resource `demoResc` and there is no other replica of that file in the iRODS system (0 in front of 'demoResc'). The size of the file is 27KB. It is stored with a time stamp and a checksum (4f70b0...749ec). Actually, the checksum calculation was triggered by the option '-K' of the `iput` command.

### Data download

We deleted our local copy of our test file and want to restore it. We can download the version stored in iRODS with:

```
iget -K test.txt test-restore.txt
```

We downloaded test.txt and renamed our local copy to test-restore.txt. With the option '-K' we trigger that the checksum of the local file is compared with the checksum of the file on the iRODS server.

**Note,** iRODS can be used as external storage service, we can store extra system information, i.e. checksums which are used to verify data integrity upon data moving.

### Small exercise:

- Verify that the checksum in iRODS is the same as for your local file. You can calculate the checksum in linux with `md5sum` command.

Run `md5sum test-restore.txt`


## Structuring data & Access Control (15 minutes)

On a normal PC you would create folder structures to keep the overview over your data. In iRODS you can also create folders, however, they are called collections.

In iRODS you have the commands `imkdir` and `imv`.

To create an iRODS collection:

```
imkdir books
```

Now lets download a file to your linux account:

```wget http://www.gutenberg.org/files/52521/52521-0.txt```

Upload it to iRODS using `iput` command.

```
iput -K 52521-0.txt
```
 
Where is it uploaded? 

```
ils
/eveZone/home/di4r-user1:
  52521-0.txt
  test.txt
  C- /eveZone/home/di4r-user1/books
```
  
Now let us move our test file to the books collection and list the contents of the collection

```
imv 52521-0.txt books
ils -L books
```

or list the whole home directory recursively `ils -L -r`

```
ils -L -r
/eveZone/home/di4r-user1:
  di4r-user1        0 demoResc           37 2017-06-09.17:20 & test.txt
    751d08e828a38291381a059595d555dd    generic    /var/lib/irods/iRODS/Vault/home/di4r-user1/test.txt
  C- /eveZone/home/di4r-user1/books
/eveZone/home/di4r-user1/books:
  di4r-user1        0 demoResc       522843 2017-06-09.17:37 & 52521-0.txt
    c1d33293b8c86258fe7d9a050a6e2145    generic    /var/lib/irods/iRODS/Vault/home/di4r-user1/books/52521-0.txt

```

`-C` stands for the collection (directory or folders) in iRODS. You see that the logical iRODS collection '/eveZone/home/di4r-user1/books' has the physical counterpart '/var/lib/irods/iRODS/Vault/home/di4r-user1/books/'. So data does not end up on the iRODS server randomly but follows a structure.

We can also put data directly into an iRODS collection. Lets create a directory in our local unix machine and move the folder to iRODS under the collection 'books' in one go.

```
mkdir mathbooks
cd mathbooks
touch file1 file2 file3
cd ..

iput -K -r mathbooks books/mathbooks
```

We need to use the flag `-r` for recursive upload and we gave a different name to the folder in iRODS.


### Small exercise:

- What happens in the following two lines?

 ``` 
 ils -L
 ```
 ```
 iput -K test-restore.txt
 ```
- How can you list your iRODS home directory now?
- Change your working directory again to your iRODS home collection. Verify with ipwd!!!
- Remove test-restore.txt from iRODS (but not from your linux home!)

<!--
## Exercise: Moving data in iRODS (10 minutes)

1. Create a new collection for another author under you iRODS home collection.
2. Add a file from 'gutenberg.org' to the new collection.
 Download a file to your linux account:
 `wget http://www.gutenberg.org/files/52521/52521-0.txt`
 Upload it to iRODS using `iput`
3. Create some subcollections
4. List the content of the collection including the content of all possible subcollections.

 For experts (outlook to the next section):
5. How can you grant access to the data in your collection?
-->


## Access control


Check the current access of your data with

```
ils -r -A books
```

```
ils -r -A books
/eveZone/home/di4r-user1/books:
        ACL - di4r-user1#eveZone:own   
        Inheritance - Disabled
  52521-0.txt
        ACL - di4r-user1#eveZone:own   
  C- /eveZone/home/di4r-user1/books/mathbooks
/eveZone/home/di4r-user1/books/mathbooks:
        ACL - di4r-user1#eveZone:own   
        Inheritance - Disabled
  file1
        ACL - di4r-user1#eveZone:own   
  file2
        ACL - di4r-user1#eveZone:own   
  file3
        ACL - di4r-user1#eveZone:own    
```


The collection and all its data is owned by the user 'di4r-user1'. No one else has access rights.

Collections have a flag 'Inheritance'. If this flag is set to true, all content of the folder will inherit the accession rights from the folder.

Let us change the accession rights of 'books'. Choose another irods user (i.e. user 'di4r-user2') who you want to give access (as your neighbour team):

```
ichmod read di4r-user2 books
```

The user 'di4r-user1' can list the collection and see the data to which he has the respective permission.

```
ils -r -A
/eveZone/home/di4r-user1:
        ACL - di4r-user1#eveZone:own   
        Inheritance - Disabled
  test.txt
        ACL - di4r-user1#eveZone:own   
  C- /eveZone/home/di4r-user1/books
/eveZone/home/di4r-user1/books:
        ACL - di4r-user1#eveZone:own   di4r-user2#eveZone:read object   
        Inheritance - Disabled
  52521-0.txt
        ACL - di4r-user1#eveZone:own   
  C- /eveZone/home/di4r-user1/books/mathbooks
/eveZone/home/di4r-user1/books/mathbooks:
        ACL - di4r-user1#eveZone:own   
        Inheritance - Disabled
  file1
        ACL - di4r-user1#eveZone:own   
  file2
        ACL - di4r-user1#eveZone:own   
  file3
        ACL - di4r-user1#eveZone:own   
```

<!-- Change the inheritance and place some new data in the collection:

```
ichmod inherit lewiscarroll
iput -K test-restore.txt lewiscarroll/test1.txt
```
```
ils -A -r lewiscarroll 

```
Only the newly placed file will inherit the ACLs from the folder. Old data will keep their ACLs.

```
ils -r -A lewiscarroll

/eveZone/home/di4r-user1/lewiscarroll:
        ACL - di4r-user1#eveZone:own   di4r-user2#eveZone:read object   
        Inheritance - Enabled
  test1.txt
        ACL - di4r-user1#eveZone:own   di4r-user2#eveZone:read object   
  C- /eveZone/home/di4r-user1/lewiscarroll/book-aliceInWonderland
/eveZone/home/di4r-user1/lewiscarroll/book-aliceInWonderland:
        ACL - di4r-user1#eveZone:own   
        Inheritance - Disabled
  aliceInWonderland-DE.txt.utf-8
        ACL - di4r-user1#eveZone:own   
  C- /eveZone/home/di4r-user1/lewiscarroll/book-aliceInWonderland/aliceInWonderland
/eveZone/home/di4r-user1/lewiscarroll/book-aliceInWonderland/aliceInWonderland:
        ACL - di4r-user1#eveZone:own   
        Inheritance - Disabled
  aliceInWonderland-EN.txt.utf-8
        ACL - di4r-user1#eveZone:own   
  aliceInWonderland-IT.txt.utf-8
        ACL - di4r-user1#eveZone:own  
```

### Small exercise

**THIS PART TO BE REMOVED**

1. Pair up with another team.
2. Make one of your collections or subcollections accessible to other team
3. Switch on the inheritance
4. Put some data in the folder (`imv` or `icp` some data that is already in iRODS)
5. Use `iget` and try to download the data your partnering team gave access to.  

-->

## Metadata (30 minutes)

In the previous section we up and downloaded data to an iRODS server and set permissions. So far it is nothig special compared to a normal (unix) filesystem apart from the strange commands.

What does iRODS offer to the user that exceeds such functionality?

### Create Attribute, Value, Unit triples


Metadata attribute-value-units triples (AVUs) consist of an Attribute-Name, Attribute-Value, and an optional Attribute-Units.  

We can annotate files with so-called AVUs triples. These triples are added to a database and are searchable, e.g. you can ask the iRODS system give me all data (files and collections) whose author is "Alice" and which were created in 2016.

First we will explore how to create these metadata for which we can search later.

- Annotate a data file:
 
 ```
 imeta add -d test.txt 'distance' '12' 'meter'
 ```

 ```
 imeta add -d test.txt 'author' 'Alice'
 ```
 Here the 'Unit part is empty'.
 
- Annotate a collection 
 
 ```
imeta add -C books 'collection' 'books' 
 ```

With `imeta add` you can add metadata to dataobjects (iRODS files) or collections (iRODS folders). For each command, -d, -C, -R, or -u is used to specify which type of object to work with: dataobjs, collections, resources, or users.
 
### List metadata

To list metadata do:

```
imeta ls -d test.txt
```
and

```
imeta ls -C books
```

With `imeta ls` you can retrieve the AVUs when given a file or collection name. In the next Section we will see how we can retrieve the file and folder names when given an attribute or value.

### Exercise: Create Metadata (15min)

1. Create the author Bob for the file test.txt
2. Inspect the list of AVUs
3. Add a new triple 'distance' '36' 'miles' 
4. Explore the options of `imeta` (`imeta -h`)
5. Overwrite the unit in (distance, 36, miles) with feet. Do not create a new triple and delete the old one. 
Find a way to update the existing triple.
6. Remove the author Alice

 (For experts)

7. What does `imeta set` do?
Collapses all metadata entries with the same key to one with the new value.


<!--

## iRODS resources (30 minutes, optional)

(Note: commands and resource hierarchies are still todo)

With the command `ils -L` we explored the link between the iRODS logical namespace and the the physical location of files and folders. The same is done with resources.

iRODS resources are pieces of a file system, external servers or software in which data can be stored.

You can list all resources you have available with:

```
ilsresc
``` 
You will see the resource tree.
```
demoResc
knmiDataResc
roundRobin:roundrobin
├── storage1
└── storage2
storage3
```
There are the storage resources: demoResc, knmiDataResc, storage1, storage2, ...

The resources demoResc, storage3 and knmiDataResc can be used directly to store data.
The resources storage1 and 2 are managed by a coordinating resource called roundRobin and ...

If not further specified all your data will go to 'demoResc'.

You can specify the resource on which your data shall be stored directly with the put command. Let us put some data on storage3 resource.

```
iput -K -R storage3 test-restore.txt testfile-on-storage3.txt
```

BIG advantage: As a user you do not need to know which storage medium is hidden behind the resource, you simply use the 
icommands to steer your data movements in the backend.

### User defined replication of data

Once your data is lying in iRODS you can also replicate your data to another predefined resource.
Use `irepl` to replicate the German version of Alice in Wonderland to storage3

```
irepl -R storage3 irepl -R storage3 \
lewiscarroll/book-aliceInWonderland/aliceInWonderland-DE.txt.utf-8
```

```
ils -L lewiscarroll/book-aliceInWonderland/aliceInWonderland-DE.txt.utf-8
 rods              0 demoResc      4909056 2017-02-22.12:40 & testfile.txt
        generic    /irodsVault/home/rods/testfile.txt
  rods              1 knmiDataResc      4909056 2017-02-27.18:44 & testfile.txt
        generic    /data/home/rods/testfile.txt
```
The replicas are enumerated. With this number you can specifically remove a replica. Let us remove the replica on the demoResc:

```
irm -n 0 lewiscarroll/book-aliceInWonderland/aliceInWonderland-DE.txt.utf-8
``` 
We still have a copy of the German version in our system, so the logical name still exists:

```
ils lewiscarroll/book-aliceInWonderland
/aliceZone/home/di4r-user1/lewiscarroll/book-aliceInWonderland:
  aliceInWonderland-DE.txt.utf-8
  aliceInWonderland-EN.txt.utf-8
  aliceInWonderland-IT.txt.utf-8
```

If you do an 

```
irm testfile.txt
```
all replicas will be removed. The filename will only be removed from the logical namespace if there is no replica left.


### Resources have a life of their own

In iRODS the system admin can group resources and enforce certain data replication or copying policies.

- Round Robin

```
iput -K -R roundRobin test-restore.txt testfile-on-rr.txt
ils -L testfile-on-rr.txt
```

### Small exercise

- Where is your data stored physically. 
- Where is your neighbours data stored?
- Upload several files after each other. Where does the data land physically?
- Try to put data on storage1 directly.


- Replication
Replication resource that automatically copies your data on two child resources.

## Exercise: Explore the data policy behind replResc (20min)

1. We have seen that a round robin implements a certain data policy. Which data policy is hidden behind 'replResc'?
2. Where are all the resources located? (`ilsresc -l`)
3. How many servers does the iRODS system use?

**Note** As an iRODS user you do not need to know which servers and storage systems are involved. 
You only need an idea about the policies hidden behind grouped resources.

-->













