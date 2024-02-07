### sudo gives you admin rights
```bash
sudo apt upgrade
```
 ![Alt text](<Picture 1.png>)

### pwd shows current directory

```bash 
pwd -L
```
 ![Alt text](<Picture 2.png>)
 
 
### cd changes directory
```bash
 
cd /home/ubuntu/CommandsLinux
 
```
 ![Alt text](<Picture 3.png>) 
 
 
 
 
 
### cd changes directory
```bash
 
Cd ..
 
```
 ![Alt text](<Picture 4.png>)  
 
 
 
 
### ls lists out the files in a directory
```bash
 
ls /home/ubuntu/Documents
 
```
  ![Alt text](<Picture 5.png>) 
 
 
 
### cat is used to concatenate files
```bash
 
cat sqlite_commands.sh
 
```
  ![Alt text](<Picture 6.png>) 
 
 
 
### cat is used to concatenate files
```bash
 
cat filename1.txt filename2.txt > filename3.txt
 
```
  ![Alt text](<Picture 7.png>) 
 
 
 
### cp is used to copy a file 
```bash
 
cp sqlite_commands.sh /home/ubuntu/unixcommands
 
```
  ![Alt text](<Picture 8.png>) 
 
 
 
### cp is used to copy a file 
```bash
 
cp filename1.txt filename2.txt filename3.txt /home/ubuntu/Documents
 
```
  ![Alt text](<Picture 9.png>) 
 
 
 
### cp is used to copy a file 
```bash
 
cp filename1.txt filename2.txt
 
```
  ![Alt text](<Picture 10.png>) 
 
 
 
### cp is used to copy a file 
```bash
 
cp -R /home/ubuntu/Documents /home/ubuntu/Documents_backup
 
```
  ![Alt text](<Picture 11.png>) 
 
### mv is used to cut a file and paste it in the specified directory
```bash
 
mv sqlite_commands.sh /home/ubuntu/CommandsLinux
 
 
```
  ![Alt text](<Picture 12.png>) 
 
 
 
 
### mv is used to cut a file and paste it in the specified directory
```bash
 
mv sqlite_commands.sh /home/ubuntu/CommandsLinux
 
 
```
  ![Alt text](<Picture 13.png>) 
 
 
 
### mkdir is used to create a directory
```bash
 
mkdir Music
 
```
  ![Alt text](<Picture 14.png>) 
 
 
 
### rmdir -p is used to delete a directory
```bash
 
rmdir -p Music/Songs
 
```
  ![Alt text](<Picture 15.png>) 
 
 
 
 
 
### rm is used to delete a file
```bash
 
rm filename1 filename2 filename3
 
```
  ![Alt text](<Picture 16.png>) 
 
 
 
### locate is used to search for a file
 
```bash
 
locate -i school*note
 
```
  ![Alt text](<Picture 17.png>) 
 
 
 
 
### find is used to search through directories
 
```bash
 
find /home -name sql_commands.sh
 
```
  ![Alt text](<Picture 18.png>) 
 
 
 
 
### grep values is used to search through files
 
```bash
 
grep values sql_commands.sh
 
```
  ![Alt text](<Picture 19.png>) 
 
 
 
 
### df -h is used check system disk space
 
```bash
 
df -h
 
```
  ![Alt text](<Picture 20.png>) 
 
 
 
### du is used check how much space a file or directory takes up
 
```bash
 
du /home/ubuntu/CommandsLinux
 
```
  ![Alt text](<Picture 21.png>) 
 
 
 
### head is used display the first 10 lines of a file
 
```bash
 
head deploy1.yml
 
```
  ![Alt text](<Picture 22.png>) 
 
 
 
### tail is used display the last 10 lines of a file
 
```bash
 
tail -n 5 deploy1.yml
 
```
  ![Alt text](<Picture 23.png>) 
 
 
 
### diff is used to check the difference between 2 files
 
```bash
 
diff deploy1.yml deploy2.yml
 
```
  ![Alt text](<Picture 24.png>) 
 
 
 
 
 
### tar is used to archive a file
 
```bash
 
tar -cvf newarchive.tar /home/ubuntu
 
```
  ![Alt text](<Picture 25.png>) 
 
 
 
### chmod is used to change file or folder permission
 
```bash
 
chmod 777 deploy1.yml
 
```
  ![Alt text](<Picture 26.png>) 
 
 
 
 
### chwon is used to change file ownership
```bash
chown root filename.txt
```
  ![Alt text](<Picture 27.png>) 
 
 
 
 
### jobs, ps ux and kill are used to check and kill processes
```bash
jobs [options] jobID
ps ux
kill SIGKILL 63773
 
 
```
  ![Alt text](<Picture 28.png>) 
 
 
 
### ping checks if server is available
 
```bash
ping google.com
 
```
  ![Alt text](<Picture 29.png>) 
 
 
 
 
### wget downloads files from the internet
 
```bash
wget https://wordpress.org/latest.zip
 
```
  ![Alt text](<Picture 30.png>) 
 
 
 
### Uname -a prints out information about the linux system
 
```bash
Uname -a
 
```
  ![Alt text](<Picture 31.png>) 
 
 
 
 
### Top lists out all running processes
 
```bash
Top
 
```
  ![Alt text](<Picture 32.png>) 
 
 
 
### History -c clears history
 
```bash
History -c
 
```
  ![Alt text](<Picture 33.png>) 
 
 
 
 
### Man -ls shows manual page for ls command
 
```bash
Man -ls
 
```
  ![Alt text](<Picture 34.png>) 
 
 
 
 
### zip creates a compressed archive file
 
```bash
zip archive.zip note.txt
 
```
  ![Alt text](<Picture 35.png>) 
 
 
### unzip unzips your archive file
 
```bash
unzip archive.zip
 
```
  ![Alt text](<Picture 36.png>) 
 
 
 
### hostname -I spits out your IP
 
```bash
hostname -I
 
```
  ![Alt text](<Picture 37.png>) 
 
 
 
 
### theuseradd allows you to add a user to the machine
 
```bash
useradd dareio
 
```
  ![Alt text](<Picture 38.png>) 
 
 
 
### the userdel allows you to delete a user from the machine
 
```bash
userdel username
 
```
  ![Alt text](<Picture 39.png>) 
 
 
 
### apt allows you to install a package
 
```bash
sudo apt install zip
 
```
  ![Alt text](<Picture 40.png>) 
 
 
 
 
### nano allows you to create a file using the nano editor
 
```bash
Nano file24
 
```
  ![Alt text](<Picture 41.png>) 
 
 
 
 
### vi allows you to create a file using the VI editor
 
```bash
Vi file25
 
```
  ![Alt text](<Picture 42.png>) 

   ![Alt text](<Picture 42_1.png>) 
 
 
 
 
 
 
### alias allows you to create a shortcut for a command
```bash
alias k=’kill’
 
```
  ![Alt text](<Picture 43.png>) 
 
 
 
 
 
### the htop command monitors system resources
```bash
Htop
 
```
  ![Alt text](<Picture 44.png>) 
 
 
 

