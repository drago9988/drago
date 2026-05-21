# TinyOS Setup Guide: Ubuntu 16.04 on VirtualBox

This guide explains how to set up Ubuntu 16.04 in VirtualBox, configure
USB access for TelosB and Iris motes, and install TinyOS.

------------------------------------------------------------------------

## 1. Download and Install Required Software

Download and install the following:

-   VirtualBox
-   VirtualBox Extension Pack
-   Ubuntu 16.04 ISO

**Make sure you check the "Install Guest Additions" option in the "Set up unattended guest OS installation" when installing Ubuntu 16.04 LTS in the Virtual Box.** 

------------------------------------------------------------------------

## 2. Enable `sudo` and Terminal Access

Boot Ubuntu 16.04 into recovery mode:

1.  While Ubuntu 16.04 LTS is booting, hold the **SHIFT** key.

2.  Choose:

    ``` text
    Advanced Options for Ubuntu -> <kernel_version> (recovery mode)
    ```

3.  Choose **root** and press **ENTER**.

Remount the filesystem as writable:

``` bash
mount -o remount,rw /
```

Add your user to the `sudo` group:

``` bash
adduser <your_username> sudo
```

Edit the locale file:

``` bash
nano /etc/default/locale
```

Update the entries so that all values are:

``` text
en_US.UTF-8
```

Save and exit, then run:

``` bash
locale-gen
reboot
```

Verify terminal access after reboot:

1.  Log in to Ubuntu 16.04.
2.  Press:

``` text
Ctrl + Alt + T
```

------------------------------------------------------------------------

## 3. Add User to `vboxusers` in the host machine (only if the host machine is Linux based)

``` bash
sudo adduser $USER vboxusers
```

Then log out and back in, or reboot.

------------------------------------------------------------------------

## 4. Install VirtualBox Recommendations

Add the VirtualBox Extension Pack:

Extensions > Install > \<the downloaded extension pack\>

------------------------------------------------------------------------

## 5. Configure USB Devices in VirtualBox

1.  Close the VM.

2.  Plug in the TelosB and Iris motes.

3.  In VirtualBox, right-click the VM.

4.  Go to:

    ``` text
    Settings -> USB
    ```

5.  Click **Add Filter**.

6.  Choose the Iris and TelosB motes.

------------------------------------------------------------------------

## 6. Update and Upgrade Packages

Run:

``` bash
sudo apt update && sudo apt upgrade
```

------------------------------------------------------------------------

## 7. Install Necessary Packages

Run:

``` bash
sudo apt install -y python2.7 python-minimal openjdk-8-jdk gcc-avr avr-libc nescc minicom wget unzip automake autoconf libtool avrdude curl gcc-msp430 git python-serial tinyos-tools
```

------------------------------------------------------------------------

## 8. Install TinyOS

Clone the TinyOS repository:

``` bash
git clone https://github.com/tinyos/tinyos-main.git
```

Go into the TinyOS tools directory:

``` bash
cd ~/tinyos-main/tools
```

Build and install TinyOS tools:

``` bash
./Bootstrap
./configure
make
sudo make install
```

------------------------------------------------------------------------

## 9. Configure USB Permissions

Add your user to the `dialout` group:

``` bash
sudo usermod -aG dialout $USER
```

Reboot:

``` bash
reboot
```

## 10. Create Shared Folder

1. Close the VM
2. Go to Settings > Shared Folders > Add new shared folder
3. Pick a folder (or better, create a new one to the Host machine and choose this one)
4. Check the auto-mount option
5. apply changes and open the VM
6. In the terminal, run:
``` bash
sudo usermod -aG vboxsf $USER
```
7. Log-out and login back or reboot

------------------------------------------------------------------------

## Done

Ubuntu 16.04, TinyOS, and mote USB access should now be configured.

# Περαιτέρω οδηγίες

## Σχετικά με τις μετρήσεις

1. Όσες ομάδες έχουν TelosB mote, να χρησιμοποιήσουν τους πραγματικούς sensors του TelosB
2. Όσες ομάδες δεν έχουν TelosB mote, να χρησιμοποιήσουν 'fake' μετρήσεις.

Συγκεκριμένα, μελετήστε το παράδειγμα Sensing. Όσοι έχουν TelosB, να χρησιμοποιήσουν το tmote_onboard_sensors module, ενώ όσοι δεν έχουν να χρησιμοποιήσουν το universal_sensors module. Περισσότερες λεπτομέρειες στο README του παραδείγματος.

## Εύρεση των θυρών USB

Για να βρείτε σε ποιά θύρα USB είναι συνδεδεμένο το mote, χρησιμοποιήστε την παρακάτω εντολή:

``` bash
ls /dev | grep ttyUSB* 
```

## Compile and Flash programs

### TelosB

Για να φορτώσετε κώδικα στα TelosB εκτελείτε τις παρακάτω εντολές:

``` bash
sudo make clean  // Optional, but useful sometimes
sudo make telosb install.<TOS_NODE_ID>  bsl,/dev/ttyUSBx  // Βρείτε τη θύρα USB στην οποία είναι συνδεδεμένη η συσκευή σας
```

Παράδειγμα εκτέλεσης:

``` bash
sudo make clean
sudo make telosb install.0 bsl,/dev/ttyUSB0 // Εδώ περνάμε TOS_NODE_ID=0 και το mote είναι συνδεδεμένο στη θύρα USB0
```

### IRIS

Αντίστοιχα, για τα IRIS χρησιμοποιήστε τις παρακάτω εντολές:
``` bash
sudo make clean
sudo make iris install.<TOS_NODE_ID> mib520,/dev/ttyUSBx  // Βρείτε τη θύρα USB στην οποία είναι συνδεδεμένη η συσκευή σας
```

Παράδειγμα εκτέλεσης:

``` bash
sudo make clean
sudo make telosb install.0 bsl,/dev/ttyUSB0 // Εδώ περνάμε TOS_NODE_ID=0 και το mote είναι συνδεδεμένο στη θύρα USB0
```

**Προσοχη: Όταν συνδέσετε ένα TelosB mote, θα δείτε ότι εμφανίζει μία μόνο θύρα USB στον φάκελο /dev. Ωστόσο, για το IRIS mote, εμφανίζει 2 θύρες. Θα χρησιμοποιήσετε την 1η θύρα μόνο κατα τη διαδικασία flash την εφαρμογής σας και τη δεύτερη θύρα μόνο όταν θέλετε να διαβάσετε output της συσκευής (π.χ. τα αποτελέσματα των printf εντολών σας).**

## PrintfClient

Αν γράψετε printf εντολές, τότε πρέπει να ξεκινήσετε έναν PrintfClient για να μπορέσετε να δείτε τα αποτελέσματα των printf εντολών. Για να το κάνετε, εκτελέστε μία από τις παρακάτω εντολές, ανάλογα με το mote που θέλετε να ελέγξετε:

### TelosB

``` bash
sudo java net.tinyos.tools.PrintfClient -comm serial@/dev/ttyUSBx:telosb   // Βρείτε τη θύρα USB στην οποία είναι συνδεδεμένο το mote
```

### IRIS
```bash
sudo java net.tinyos.tools.PrintfClient -comm serial@/dev/ttyUSBx:iris   // Βρείτε τη θύρα USB στην οποία είναι συνδεδεμένο το mote
```

## SerialForwarder

Προτείνουμε, αντί να ξεκινήσετε έναν Client ο οποίος θα "μιλάει" κατευθείαν με τη θύρα USB, μπορείτε να ξεκινήσετε ένα Network Socket, το οποίο θα "μιλάει" με τη συσκευή μέσω UART και o Client θα "μιλάει" με το Network Socket. Γενικά, είναι πιο stable σαν μέθοδος. Επίσης, optionally μπορείτε να χρησιμοποιήσετε και το δικό σας port number. Το default είναι 9002.

### TelosB (Παράδειγμα για PrintfClient)
``` bash
sudo java net.tinyos.sf.SerialForwarder -comm serial@/dev/ttyUSB*:telosb <-port 9002> // Βρείτε τη θύρα USB στην οποία είναι συνδεδεμένο το mote
sudo java net.tinyos.tools.PrintfClient -comm sf@localhost:<port>
```

### IRIS (Παράδειγμα για PrintfClient)
``` bash
sudo java net.tinyos.sf.SerialForwarder -comm serial@/dev/ttyUSB*:iris <-port 9002> // Βρείτε τη θύρα USB στην οποία είναι συνδεδεμένο το mote
sudo java net.tinyos.tools.PrintfClient -comm sf@localhost:<port>
```


# Προτάσεις

Δείτε αναλυτικά τα παρακάτω παραδείγματα που περιέχονται στη βιβλιοθήκη, καθώς και τα παραδείγματα (examples) που δίνονται στο παρόν Repository:

1. examples/Blink
2. examples/BlinkToRadio
3. examples/Sensing

Τα παραδείγματα αυτά είναι παραλλαγές των αρχικών παραδειγμάτων του TinyOS:

1. apps/Blink
2. apps/tutorials/BlinkToRadio
3. examples/LowPowerSensing

# Αναφορά

Σε αυτό το section γράψτε μία σύντομη αναφορά σχετικά με την υλοποίηση και τα βήματα της άσκησης.

## 1ο μέρος
   
## 2ο μέρος

## 3ο μέρος
Για την υλοποίηση του τρίτου ερωτήματος, υλοποιήθηκαν δύο αρχεία ( index.html, app.py), για την υλοποίηση του dashboard σε μια web-based εφαρμογή.
1. Το αρχείο app.py, λειτουργεί ως backend της web εφαρμογής, γραμμένος σε python. Ο σκοπός του είναι να συνδέει το frontend (index.html) μέσω της θύρας 5000 με τα δεδομένα της βάσης MongoDB, με αποτέλεσμα την απεικόνιση τους στο Dashboard. Η βιβλιοθήκη flask χρησιμοποιείται για την δημιουργία web εφαρμογών και την διαχείριση του API τους. Αρχικά γίνεται σύνδεση με την βάση δεδομένων MongoDB και από εκεί, με χρονική σειρά, μεταφέρουμε τα δεδομένα, μέσω της συνάρτησης get_latest(). Αυτό το μέρος αποστέλλει τα δεδομένα του route "/api/latest", έτσι ώστε να παρουσιάζονται "real-time" τα δεδομένα. Τέλος, με την βοήθεια της συνάρτησης get_history(), δημιουργείται ένα query για το MongoDB, με βάση το timestamp και επιστρέφονται για παρουσίαση τους στον πίνακα του frontend.
2. Το αρχείο index.html, παρουσιάζει τα ζητούμενα του ερωτήματος 3, θέτοντας την διεπαφή χρήστη της εφαρμογής. Εδώ η συνάρτηση updateCharts(), καλείται ανά 20 δευτερόλεπτα κάνει fetch τα δεδομένα που περνούν μέσα από το backend και τα αντιστρέφει, για την ορθή απεικόνιση τους. Επιπρόσθετα, τα δεδομένα της get_history() του backend, αναδεικνύονται με την συνάρτηση loadHistory(), στον πίνακα κάτω από το Dashboard.

## 4ο μέρος

