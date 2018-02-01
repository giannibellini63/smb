# Condivisione file ambiente X e ambiente W

## SMB

### What is an SMB Port? What is Port 445 and Port 139 used for?

NetBIOS stands for Network Basic Input Output System. It is a software protocol that allows applications, PCs, and Desktops on a local area network (LAN) to communicate with network hardware and to transmit data across the network. Software applications that run on a NetBIOS network locate and identify each other via their NetBIOS names. A NetBIOS name is up to 16 characters long and usually, separate from the computer name. Two applications start a NetBIOS session when one (the client) sends a command to “call” another client (the server) over TCP Port 139.

### What is Port 139 used for

**NetBIOS** on your WAN or over the Internet, however, is an enormous security risk. All sorts of information, such as your domain, workgroup and system names, as well as account information can be obtained via NetBIOS. So, it is essential to maintain your NetBIOS on preferred network and ensure it never leaves your network.
*Firewalls*, as a measure of safety always block this port first, if you have it opened. The port 139 is used for File and Printer Sharing but happens to be the single most dangerous Port on the Internet. This is so because it leaves the hard disk of a user exposed to hackers.

Once an attacker has located an active Port 139 on a device, he can run NBSTAT a diagnostic tool for NetBIOS over TCP/IP, primarily designed to help troubleshoot NetBIOS name resolution problems. This marks an important first step of an attack — Footprinting.

Using NBSTAT command, the attacker can obtain some or all of the critical information related to

* A list of local NetBIOS names
* Computer name
* A list of names resolved by WINS
* IP addresses

Contents of the session table with the destination IP addresses
With the above details at hand, the attacker has all the important information about the OS, services, and major applications running on the system. Besides these, he also has private IP addresses that the LAN/WAN and security engineers have tried hard to hide behind NAT.  Moreover, User IDs are also included in the lists provided by running NBSTAT.
This makes it easier for hackers to gain remote access to the contents of hard disk directories or drives. They can then, silently upload and run any programs of their choice via some freeware tools without the computer owner ever being aware.
If you are using a multi-homed machine, disable NetBIOS on every network card, or Dial-Up Connection under the TCP/IP properties, that is not part of your local network.

### What is an SMB Port

While Port 139 is known technically as ‘NBT over IP’, Port 445 is ‘SMB over IP’. SMB stands for ‘Server Message Blocks’. Server Message Block in modern language is also known as Common Internet File System. The system operates as an application-layer network protocol primarily used for offering shared access to files, printers, serial ports, and other sorts of communications between nodes on a network.
Most usage of SMB involves computers running Microsoft Windows, where it was known as ‘Microsoft Windows Network’ before the subsequent introduction of Active Directory. It can run on top of the Session (and lower) network layers in multiple ways.
For instance, on Windows, SMB can run directly over TCP/IP without the need for NetBIOS over TCP/IP. This will use, as you point out, port 445. On other systems, you’ll find services and applications using port 139. This means that SMB is running with NetBIOS over TCP/IP.
Malicious hackers admit, that Port 445 is vulnerable and has many insecurities. One chilling example of Port 445 misuse is the relatively silent appearance of NetBIOS worms. These worms slowly but in a well-defined manner scan the Internet for instances of port 445, use tools like PsExec to transfer themselves into the new victim computer, then redouble their scanning efforts. It is through this not much-known method, that massive “Bot Armies“, containing tens of thousands of NetBIOS worm compromised machines, are assembled and now inhabit the Internet.

### How to deal with Port 445

Considering the above perils, it is in our interest to not expose Port 445 to the Internet but like Windows Port 135, Port 445 is deeply embedded in Windows and is hard to close safely. That said, its closure is possible, however, other dependent services such as DHCP (Dynamic Host Configuration Protocol) which is frequently used for automatically obtaining an IP address from the DHCP servers used by many corporations and ISPs, will stop functioning.
Considering all the security reasons described above, many ISPs feel it necessary to block this Port on behalf of their users. This happens only when port 445 is not found to be protected by NAT router or personal firewall. In such a situation, your ISP may probably prevent port 445 traffic from reaching you.

## Installazione di Samba

L’installazione dei pacchetti necessari si può effettuare con il comando:
sudo apt-get install samba

### Attività preliminari
A installazione terminata dovremo creare una cartella che andremo poi a condividere. “User” è il nome dell’utente sotto cui andremo a creare la nostra cartella che per semplicità è l’utente che avete creato quando avete installato il server Debian.

  mkdir /home/user/test

Io preferisco dare alla cartella creata i permessi di lettura, scrittura ed esecuzione a tutti gli utenti (per semplicità):

  chmod 777 -R /home/user/test

Dovete sapere che Samba crea il suo proprio archivio di password. Per questo motivo dovremo assegnare all’utente “user”, o a qualsiasi utente presente nel nostro server che dovrà accedere alla condivisione, una password in Samba:

  sudo smbpasswd -a user

### Modifica del file smb.con

Per prima cosa salviamo una copia del file smb.conf

  sudo cp /etc/samba/smb.conf ~

Ora possiamo tranquillamente effettuare la modifica del nostro file di configurazione:

  sudo nano /etc/samba/smb.conf

All’interno del file di configurazione aggiungiamo o modifichiamo se già presenti queste righe di configurazione:

  [global]
  workgroup = WORKGROUP
  server string = DESCRIZIONE COMPUTER
  security = share
  Successivamente condividiamo la cartella creata all’inizio di questa guida:
  [test]
  path = /home/user/test
  available = yes
  valid users = user
  read only = no
  browseable = yes
  public = yes
  writable = yes

Salviamo le modifiche e usciamo dal’editor.

Alcune nozioni:

•	WORKGROUP: il nome della rete Windows. Se non avete modificato il nome Windows ha come default Workgroup.
•	DESCRIZIONE COMPUTER: il nome da dare al server Linux visto sulla rete Windows
•	/home/user/test: il percorso della directory Linux da condividere con Windows. potete condividere la cartella che volete. 

Basterà cambiare questo percorso ovviamente.

•	[test]: il nome che ho dato alla cartella condivisa
•	available: la cartella condivisa è disponibile per i client in rete
•	valid users: utenti che possono accedere alla cartella condivisa dalla rete
•	read only: se vogliamo rendere la cartella di sola lettura
•	writable: se il contenuto della cartella è modificabile
